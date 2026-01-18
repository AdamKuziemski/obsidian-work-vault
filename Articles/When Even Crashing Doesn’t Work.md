---
tags:
  - topic/programming/cpp
  - topic/programming/low-level
source: https://randomascii.wordpress.com/2012/07/05/when-even-crashing-doesnt-work/
author: Bruce Dawson
read: false
---
I’ve written previously about the [importance of crashing](https://randomascii.wordpress.com/2011/12/07/increased-reliability-through-more-crashes/) in order to [improve code quality](https://randomascii.wordpress.com/2012/02/14/64-bit-made-easy/). However even the seemingly simple task of crashing can be more error prone than you might expect.

I’ve recently become aware of two different problems that can happen when crashing in 64-bit Windows. There is a Windows bug which can make debuggers forget where a crash happened, and there is a Windows design decision which sometimes causes a crash to be completely ignored!

Both problems are (mostly) avoidable once you know what to do, but the required techniques are far from obvious.

## Forgetting where a crash happened

It is a reasonable minimum requirement that a debugger should halt on the exact instruction that triggered a fault and then attempt to show source code, local variables, a call stack etc. There are all sorts of reasons it may be difficult or impossible to show source code (none available), local variables (optimized away), or a call stack (stack trashed), but for user-mode debugging it should always be possible to stop on the faulting instruction.

And indeed, in all the decades that I have used Visual C++ it has managed this task quite well – until recently.

[![image](https://randomascii.files.wordpress.com/2012/07/image_thumb.png?w=213&h=159 "image")](https://randomascii.files.wordpress.com/2012/07/image.png)

Starting a few months ago I noticed that, when the program that I was debugging crashed, the VC++ debugger would not halt on the faulting instruction. It wouldn’t even halt in the crashing function. Instead it would halt two levels into the OS, with a call stack that made no sense. At first I thought that the project I was working on was doing something weird with a structured exception handler but I was able to reproduce the bug on a fresh project created by the VC++ New Project Wizard. I briefly thought that maybe something was misconfigured on my machine, but then my coworkers started reporting this problem as well. Then I thought maybe it was a newly introduced VC++ bug – but the same problem can be triggered in windbg as well.

I wasn’t sure what was happening but it smelled like a recently introduced Windows bug.

Update: this Windows bug has been fixed – sometime around 2013 I believe.

My minimal test program for this bug was to call this Crash() function just before the message pump in a default Win32 program, debug build:

```cpp
void Crash()  
{   
  char* p = 0;  
  p[0] = 0;  
}
```

If I break on the instruction that will crash then I get the call stack below, and I should get the same call stack after crashing:

[![image](https://randomascii.files.wordpress.com/2012/07/image_thumb1.png?w=393&h=156 "image")](https://randomascii.files.wordpress.com/2012/07/image1.png)

That is indeed the call stack that I got in this scenario for years. However, starting a few months ago, on most 64-bit Windows 7 machines that I have tested this on, the actual call stack is this:

[![image](https://randomascii.files.wordpress.com/2012/07/image_thumb2.png?w=393&h=203 "image")](https://randomascii.files.wordpress.com/2012/07/image2.png)

Notice that the function that crashed is not even listed! This makes routine bug investigation an expert-level problem.

Sometimes the crash call stack is even worse, with even the parent of the crashing function missing:

[![image](https://randomascii.files.wordpress.com/2012/07/image_thumb3.png?w=391&h=175 "image")](https://randomascii.files.wordpress.com/2012/07/image3.png)

The actual stack displayed varies. Sometimes it is correct, and sometimes the two ZwRaiseException entries are listed. It seems to depend on subtle details of the code at the crash location, or the stack frames, or the phase of Venus.

Windbg defaults to halting on first-chance exceptions, so it normally avoids this bug. However if you continue execution after a crash then the exception handlers run and the bug appears.

I’ve created a simple test program with a “Crash normally” menu item so that you can easily test it. Source and the executable are available [here](ftp://ftp.cygnus-software.com/pub/crashtest.zip). You’ll have to build the project file (with VS 2010 or VS 2012) to get symbols in order to see this properly in a debugger.

Another blogger investigated this issue earlier this year and [found the root cause](http://www.os2museum.com/wp/?p=960). The issue is a bug in the [WoW64](http://en.wikipedia.org/wiki/WoW64) support for [AVX](http://en.wikipedia.org/wiki/Advanced_Vector_Extensions). Saving the state of the AVX registers requires additional space, and apparently the WoW64 debug support fails to reserve enough space, so the stack gets corrupted. Oops.

## There is a fix (well, a couple of workarounds)

The problem with correctly displaying the location of a crash only occurs if the first-chance exception handlers are allowed to run. First-chance exception handlers give a program a chance to take some action when a program crashes (such as saving a minidump, or translating raw exception numbers into something more readable).

Programmatically saving minidumps is unnecessary and inadvisable when you are running under the debugger, so that’s no loss. Translating raw exception numbers is valuable when debugging – I [demonstrated it](https://randomascii.wordpress.com/2012/04/21/exceptional-floating-point/) a few posts ago – but it’s not valuable enough to justify the complexity caused by not knowing where you crashed. Other uses of first-chance exceptions – such as ‘fixing’ bugs so that you can continue executing – are morally bankrupt and will not be acknowledged further here.

[![image](https://randomascii.files.wordpress.com/2012/07/image_thumb4.png?w=364&h=204 "image")](https://randomascii.files.wordpress.com/2012/07/image4.png)

Clearly what we want to do is to stop any exception handlers from running when our program crashes. We want the debugger to halt when an exception is thrown, instead of after it has complicated things by letting exception handlers run. This is actually the default behavior in windbg but in Visual Studio we have to change a setting. Go to the Debug menu, select Exceptions, and check the box beside Win32 Exceptions.

In an ideal world this would be a global setting and we would be done with the problem, but alas this is a per-solution setting, so you may have to click this check box many times. It’s a minor nuisance, and well worth it for the benefit of actually being able to debug your crashes.

Another workaround with a different set of tradeoffs was suggested by Michaln, author of the [os2museum](http://www.os2museum.com/) blog. He points out that you can disable AVX support and therefore avoid the problem. The obvious disadvantage is that you lose AVX support, which will eventually become unacceptable. The command below and a reboot will turn off AVX support.

> bcdedit /set xsavedisable 1

I think that there are two changes which Microsoft should make. One is that Visual Studio should default to halting immediately when Win32 exceptions are thrown – that is a safer policy in general, and would have avoided most of the impact of this bug.

The other change that Microsoft should make is to actually fix WoW64.

I have reported this bug to Microsoft through informal channels, but I’ve heard no reply so far.

## Failure to stop at all

An equally disturbing problem was introduced some years ago with 64-bit Windows and it causes some crashes to be silently ignored.

[Structured exception handling](http://www.microsoft.com/msj/0197/Exception/Exception.aspx) is the Windows system that underpins all exception handling (C++ exceptions are implemented using structured exception handling under the hood). Its full implementation relies on being able to unwind the stack (without or without calling destructors) in order to transfer execution from where an exception occurs to a catch/__except block.

The introduction of 64-bit Windows complicated this. On 64-bit Windows it is impossible to unwind the stack across the kernel boundary. That is, if your process calls into the kernel, and then the kernel calls back into your process, and an exception is thrown in the callback that is supposed to be handled on the other side of the kernel boundary, then Windows cannot handle this.

This may seem a bit esoteric and unlikely – writing kernel callbacks seems like a rare activity – but it’s actually quite common. In particular, a WindowProc is a callback, and it is often called by the kernel, as shown below:

[![image](https://randomascii.files.wordpress.com/2012/07/image_thumb5.png?w=436&h=139 "image")](https://randomascii.files.wordpress.com/2012/07/image5.png)

If your code crashes in the user code on the right – called from the kernel – then Windows has a problem. Since Windows can’t invoke your exception handlers in the box on the left, and it doesn’t know what they would do, it has to make an executive decision about this exception. It can either crash the process, or it can silently ignore the exception, unwind the stack back to the kernel boundary, and then continue executing as if nothing happened.

Crashing the process may significantly inconvenience users, especially if there is a bug specific to 64-bit Windows in an unsupported product. But silently swallowing the exception means that many developers may be crashing in their WndProc without realizing it, leaving their process in an indeterminate state that may be causing future pain and suffering. Microsoft tries to err on the side of maximum compatibility and stability, but sometimes this just sweeps problems under the rug.

Triggering this behavior is easy. In a Project Wizard “Win32 Project” just drop a call to the Crash() function in the paint handler. To make this demo particularly dramatic be sure to put the Visual Studio exception settings back to normal. That is, make it so that Visual Studio does not stop when an exception is thrown – only when it is unhandled. Here’s a sample of what the modified code could look like, complete with a new/delete pair that straddles the Crash() call:

```cpp
case WM_PAINT:  
{  
  hdc = BeginPaint(hWnd, &ps);  
  char* p = new char[1000000];  
  Crash();  
  delete [] p;  
  EndPaint(hWnd, &ps);  
  break;  
}
```

And here’s what the output window looks like:

[![image](https://randomascii.files.wordpress.com/2012/07/image_thumb6.png?w=588&h=227 "image")](https://randomascii.files.wordpress.com/2012/07/image6.png)

The more you resize the window the more frantically the debugger tries to tell you that your program is in trouble. And yet your program continues. Try running it not under the debugger and you will see that it appears to be running normally. But if you look in task manager as you resize the window you will see the memory consumption growing out of control – the delete statement is never reached.

Aside: inevitably somebody will suggest that if I used std::vector then I wouldn’t have this memory leak. And indeed, normally I would use std::vector or some other container class to manage memory – manually calling delete is for chumps. However there are a couple of points to consider here:

- The ability of std::vector to magically delete memory when an exception is thrown only works with C++ exceptions, not structured exceptions (crashes). There are ways to translate structured exceptions to C++ exceptions but this is misguided and, either slows your program or misses some exceptions. It’s just a bad idea. Don’t do it.
- Additionally, the whole point of this article is that when you are in a callback from the kernel the exception handling mechanism – structured and C++ exceptions – is impaired. You can’t count on it to save you.

## There is a fix

The default policy on 64-bit Windows is to silently swallow the crash, but on Windows 7 SP1 there is a choice (taken away in Windows 8 and above – huh). There is a pair of undocumented (not directly listed on MSDN, although they are [mentioned](http://support.microsoft.com/kb/976038)) functions that can be used to configure this behavior. You can read the hairy details (written from a pre-SP1 perspective) [here](http://blog.paulbetts.org/index.php/2010/07/20/the-case-of-the-disappearing-onload-exception-user-mode-callback-exceptions-in-x64/) or you can start the process of redemption by calling this function:

```cpp
void EnableCrashingOnCrashes()  
{  
  typedef BOOL (WINAPI *tGetPolicy)(LPDWORD lpFlags);
  typedef BOOL (WINAPI *tSetPolicy)(DWORD dwFlags);  
  const DWORD EXCEPTION_SWALLOWING = 0x1;

  HMODULE kernel32 = LoadLibraryA(“kernel32.dll”);  
  tGetPolicy pGetPolicy = (tGetPolicy)GetProcAddress(kernel32, “GetProcessUserModeExceptionPolicy”);  
  tSetPolicy pSetPolicy = (tSetPolicy)GetProcAddress(kernel32, “SetProcessUserModeExceptionPolicy”);  

  if (pGetPolicy && pSetPolicy)  
  {  
    DWORD dwFlags;  
    if (pGetPolicy(&dwFlags))  
    {  
      // Turn off the filter  
      pSetPolicy(dwFlags & ~EXCEPTION_SWALLOWING);  
    }  
  }  

  BOOL insanity = FALSE;  
  SetUserObjectInformationA(GetCurrentProcess(), UOI_TIMERPROC_EXCEPTION_SUPPRESSION, &insanity, sizeof(insanity));
}
```

The GetProcAddress dance is necessary because many versions of Windows don’t have these functions.

The SetUserObjectInformationA dance is necessary to stop Windows from swallowing exceptions in TimerProc calls. Sigh…

Calling this function – once at process startup will do – is a great start. It will ensure that crashes you hit during testing will get noticed and, one hopes, fixed. However there is probably another step that you will want to do. If you normally use an exception handler to record crash dumps then you will probably find that your exception handler is not recording these crashes. That’s because your exception handler is probably on the other side of the kernel boundary. You could try putting in more exception handlers, but then you’re playing callback whack-a-mole. The far simpler solution is to use [SetUnhandledExceptionFilter](http://msdn.microsoft.com/en-us/library/windows/desktop/ms680634(v=vs.85).aspx) to put in a process-wide exception handler of last resort. This will get called even when the stack based handlers cannot be, and this should allow your crash dump reporting to catch even the gnarliest of kernel-crossing crashes. Don’t count on unhandled exception filters [for too much](http://www.nynaeve.net/?p=43), but in this case they are better than nothing. Note also that the unhandled exception filter is not called when you are debugging.

Update: if you need the unhandled exception filter to be called when you are debugging you can use AddVectoredExceptionHandler with the First parameter set to 1, and your EH will be called even if you are debugging. This can be important if you are using your exception handler for advanced purposes, such as [manually handling page faults](https://twitter.com/basisspace/status/1281823030453219330).

[![image](https://randomascii.files.wordpress.com/2012/07/image_thumb7.png?w=331&h=129 "image")](https://randomascii.files.wordpress.com/2012/07/image7.png)The same test program that has the “Crash normally” menu item also has a “Crash in callback” menu item to enable crashing in the WM_PAINT handler. As in the sample code above it leaks memory. It even keeps track of how much memory has leaked. Source and the executable are available [here](ftp://ftp.cygnus-software.com/pub/crashtest.zip).

The test program also has an “Enable crashing on crashes” menu item. If you select this then the next callback crash – typically the next time that you resize the window – will be a real crash, instead of a silently ignored crash.

Note that CrashTest.exe will only exhibit the crash swallowing behavior on 64-bit Windows, and I’ve only tested it on Windows 7.

## Things that annoy me

[![image](https://randomascii.files.wordpress.com/2012/07/image_thumb8.png?w=390&h=153 "image")](https://randomascii.files.wordpress.com/2012/07/image8.png)I don’t like the Program Compatibility Assistant. It tells you that something has gone wrong (in this case a crash in a kernel callback) but because it doesn’t tell you _what_ went wrong there is no practical way for a Windows developer to do anything about it. Thus, the cycle of programs not running correctly continues.

It also doesn’t tell you what compatibility settings it has applied. I know that this information is not of interest to Windows users, but there should be some way to get specific information about what went wrong, so that developers are not left impotently scratching their heads.

## Your task list

Download the test program from [here](ftp://ftp.cygnus-software.com/pub/crashtest.zip) to see bad exception handling (if you have an AVX capable machine running 64-bit Windows 7 SP1), and exception swallowing (all 64-bit versions of Windows).

You should enable breaking when an exception is thrown for all Win32 Exceptions, using Visual Studio’s Debug menu, Exceptions dialog. Don’t forget to do this for every solution. It is unfortunate that the default behavior is for Visual Studio has been incorrect for a couple of decades now. Maybe VS 2015 will correct this. If you enable breaking when Win32 exceptions are thrown then even kernel-callback crashes will break into the debugger, when you are running under a debugger.

Vote on the [connect issue](https://connect.microsoft.com/VisualStudio/Feedback/details/752638) to request that the Visual Studio change the defaults so that the debugger halts on first-chance Win32 exceptions.

You should consider disabling AVX to avoid the WOW64 debugging bug: “bcdedit /set xsavedisable 1”

Call EnableCrashingOnCrashes() to ensure that crashes in callbacks are not ignored. Don’t use registry editing or other options to control this behavior.

If you have a crash dump saving system then use SetUnhandledExceptionFilter to ensure that it is called if your code crashes in a callback.

Test on 64-bit Windows.

Watch for more discussion on the compatibility assistant and exception handling in some future post.

## Update

The silent swallowing of exceptions documented above is for 32-bit processes on 64-bit Windows. The behavior for 64-bit processes is a bit different. On Windows 7 if a 64-bit process crashes in a kernel callback then it will actually crash. However, if the executable doesn’t have a Windows 7 compatibility manifest (subject of a later post) then the Program Compatibility Assistant will apply a shim that will suppress future crashes. Confused yet?

The summary remains the same, with the possible addition that if you are testing on Windows 7 then you should add a compatibility manifest to say that you are doing so.

Also, I hear rumors that the stack corruption caused by storing AVX state is known by Microsoft and will be fixed. Whether the fix will be for Windows 8 only is not clear at this point.

Other issues with handling crashes are described in this [excellent blog post](https://peteronprogramming.wordpress.com/2016/05/29/crashes-you-cant-handle-easily-1-seh-failure-on-x64-windows/).