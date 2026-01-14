---
tags:
  - topic/programming/gamedev
source: https://www.facebook.com/permalink.php?id=100006735798590&story_fbid=1923486231219218
author: Carmack John
read: false
---
These two simple tips can speed up your rendering by 40%!

I mentioned in my notes a couple days ago about Pinball FX2 VR that it wasn’t holding 60 fps. I investigated with Qualcomm’s Snapdragon Profiler and it turns out that it suffers from mobile GPU specific pitfalls that would not be at all obvious to developers coming from a PC or console background.

Both the Snapdragon Adreno and ARM Mali GPUs found in Gear VR use a tiled rendering architecture. Mali uses small, fixed size tiles, and Adreno uses larger, variable sized “bins”, but the performance characteristics are similar. The idea is that instead of drawing triangles one at a time directly to a color / depth buffer in main memory, the triangles are grouped so that all the triangles in a small are can be drawn at once, using a tiny on-chip color buffer and depth buffer. If things go right, the traffic to main memory is just linear writes of the color buffer, the depth buffer doesn’t ever actually exist in main memory, and both power and performance are saved.

However, for this to work, you have to do all your drawing to a surface in one batch.

In the old days of simple rendering techniques, drawing straight to the screen, this happened pretty much automatically. However, many modern techniques involve rendering something to a separate buffer so it can be used as a texture, which can ruin things.

This is an anti-pattern for mobile graphics:

Start drawing stuff to your scene.

Switch to another framebuffer to render some effect.

Switch back to your scene to use that texture.

There is another subtle case that can cause inefficiency – if you don’t explicitly clear or invalidate all the render buffers you use, the driver has to be pessimistic and assume that you are expecting whatever you last drew to the buffer to remain there. Some apps would do things like only updating the dirty rectangles in a changing UI instead of rendering it completely, so that case does matter for correctness. Not knowing ahead of time if the drawing is going to cover every pixel, the driver is forced to initialize the internal tile memory by reading the real framebuffer contents from main memory.

The driver will skip that read if you have done a glClear() that covers the entire buffer. Simple apps often do that, but on most platforms it has become a standard optimization for developers to skip the clear of the color buffer, because every reasonable app does actually cover every pixel with drawing. On mobile, this is a de-optimization! The clear will always be faster than the forced read from main memory. The even-better solution is to use glInvalidateFramebuffer(), which does nothing but tell the driver that it can safely skip the read, even if you don’t clear.

EDIT: I forgot to mention that you should also do a glInvalidateFramebuffer() on your depth buffer after you have finished rendering, but before flushing. This tells the driver that you promise not to use the depth buffer again, so it doesn't have to write it out to memory along with the color buffer.

On Adreno, if you are using glInvalidateFramebuffer() and you can noclip outside of your world, the leftover garbage data in the areas where nothing is drawn let you see the actual bin layout on screen. On Mali, invalidates are equivalent to clears.

A less common historical optimization was to also avoid clearing the depth buffer by halving your depth precision and switching the depth range and test sense each frame. Don’t ever do that nowadays – it will trigger the bad reads on mobile, but it will also screw up the various fast-Z optimizations on desktop GPUs.

The attached image is a trace view from Snapdragon Profiler from a frame of Pinball FX2 VR.

The fact that there is a single green IB1 Start Marker and red Flush Marker is a good thing, it means that the CPU didn’t try to read anything back mid-render, so everything proceeded without any stalls, as fast as it can.

There are a lot of surfaces, which is concerning. Ideally, you just want to see two surfaces, one for each VR eye. What is happening here is that one eye is rendered to a 973 x 973 resolution surface (which is odd – should be 1024 x 1024, there is edge stretching visible), then several small and fast surfaces are rendered, then the second eye is rendered, then several small and fast surfaces for that eye, then the poison part – it goes back to both eye buffer surfaces to use the results of the little texture renderings. This go-back phase is just a single full screen quad, but it is taking a third of the total GPU time!

The green Binning blocks show the time spent sorting all the triangles into the individual bins. This is largely dominated by the triangle count in the scene and the number of bins, which is a function of buffer options. This looks fine.

The red GMEM Load bars are all waste due to not clearing / invalidating the framebuffer before rendering, and returning to the partly-rendered framebuffer. *A good mobile app should not have any GMEM Loads at all*.

The cyan Render bars are where the GPU is running fragment shaders to the internal bin memory. This looks fine.

The GMEM Store bars are when the GPU writes the bin out to main memory, which is unavoidable, but it happens twice as much here as it should, because the same buffer is returned to twice.

Invalidating the color buffer is a trivial fix, but the mid-frame rendering is the more important problem, and more difficult to fix.

Many off screen renders, like shadow buffers, can, with a little effort, be done before the eye buffers are rendered (and shared between the two eyes!), rather than on-demand, midway through the eye buffers. Drivers can sometimes figure this out and do it automatically, but it is better to not rely on that, and structure your code properly.

Unfortunately, the internal rendering here is for a glow / glare effect, and it is using the “bright” pixels that were just rendered, so it isn’t obvious how to have it ready before the eyes are drawn.

What to do?

Option 1:

Don’t do the glare effect at all. Often the simplest solutions are best. It is easy to get a little bit over attached to some effects, and not judge them by their true value. Right now, this effect is costing a LOT. Skipping the glare means you don’t need to do the other surface renders, and you wind up with just the standard two surfaces being rendered.

Option 2:

Do the glare mip mapping before the eye buffer rendering, but use the previous frame’s eye buffer as the source, since it is already done. That wouldn’t be in exactly the right place if your head is turning, so the glare would swim around a little bit. That might be OK, but you could mostly correct for it by reprojecting the texture based on the view change, TimeWarp / temporal anti-aliasing style. Getting that right can be pretty tricky. It would not be the exact same effect, since the glare would be coming from an eye buffer with glare already applied. That might be pleasing, but it might also be a runaway feedback loop, depending on parameters.

Option 3:

Instead of copying the results of the main eye buffer rendering to the glare buffer, actually render the geometry again to that smaller buffer. Rendering everything would be bad, but if the “bright” surfaces that could contribute to the glare are a small subset of the entire scene, this can work out. There may be some artifacts due to bright objects not being occluded by surfaces that weren’t drawn.

https://alias.co/john-carmack/essays