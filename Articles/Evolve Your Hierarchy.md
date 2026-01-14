---
tags:
  - topic/programming/gamedev
source: https://www.gamedev.net/articles/programming/general-and-gameplay-programming/evolve-your-hierarchy-refactoring-game-entities-with-components-r3025/
author: Cowboy Coder
read: false
---
# Refactoring Game Entities with Components

Up until fairly recent years, game programmers have consistently used a deep class hierarchy to represent game entities. The tide is beginning to shift from this use of deep hierarchies to a variety of methods that compose a game entity object as an aggregation of components. This article explains what this means, and explores some of the benefits and practical considerations of such an approach. I will describe my personal experience in implementing this system on a large code base, including how to sell the idea to other programmers and management.

## GAME ENTITIES

Different games have different requirements as to what is needed in a game entity, but in most games the concept of a game entity is quite similar. A game entity is some object that exists in the game world, usually the object is visible to the player, and usually it can move around.

Some example entities:

- Missile
- Car
- Tank
- Grenade
- Gun
- Hero
- Pedestrian
- Alien
- Jetpack
- Med-kit
- Rock

Entities can usually do various things. Here are some of the things you might want the entities to do

- Run a script
- Move
- React as a rigid body
- Emit Particles
- Play located audio
- Be packed up by the player
- Be worn by the player
- Explode
- React to magnets
- Be targeted by the player
- Follow a path
- Animate

## TRADITIONAL DEEP HIERARCHIES

![](data:image/gif;base64,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)
  
The traditional way of representing a set of game entities like this is to perform an object-oriented decomposition of the set of entities we want to represent. This usually starts out with good intentions, but is frequently modified as the game development progresses - particularly if a game engine is re-used for a different game. We usually end up with something like figure 1, but with a far greater number of nodes in the class hierarchy.

As development progresses, we usually need to add various points of functionality to the entities. The objects must either encapsulate the functionality themselves, or be derived from an object that includes that functionality. Often, the functionality is added to the class hierarchy at some level near the root, such as the CEntity class. This has the benefit of the functionality being available to all derived classes, but has the downside of the associated overhead also being carried by those classes.

Even fairly simple objects such as rocks or grenades can end up with a large amount of additional functionality (and associated member variables, and possibly unnecessary execution of member functions). Often, the traditional game object hierarchy ends up creating the type of object known as "the blob". The blob is a classic "anti-pattern" which manifests as a huge single class (or a specific branch of a class hierarchy) with a large amount of complex interwoven functionality.

While the blob anti-pattern often shows up near the root of the object hierarchy, it will also show up in leaf nodes. The most likely candidate for this is the class representing the player character. Since the game is usually programmed around a single character, then the object representing that character often has a very large amount of functionality. Frequently this is implemented as a large number of member functions in a class such as CPlayer.

The result of implementing functionality near the root of the hierarchy is an overburdening of the leaf objects with unneeded functionality. However, the opposite method of implementing the functionality in the leaf nodes can also have unfortunate consequence. Functionality now becomes compartmentalized, so that only the objects specifically programmed for that particular functionality can use it. Programmers often duplicate code to mirror functionality already implemented in a different object. Eventually messy re-factoring is required by re-structuring the class hierarchy to move and combine functionality.

Take for example the functionality of having an object react under physics as a rigid body. Not every object needs to be able to do this. As you can see in figure 1, we just have the CRock and the CGrenade classes derived from CRigid. What happens when we want to apply this functionality to the vehicles? You have to move the CRigid class further up the hierarchy, making it more and more like the root-heavy blob pattern we saw before, with all the functionality bunched in a narrow chain of classes from which most other entity classes are derived.

## AN AGGREGATION OF COMPONENTS

The component approach, which is gaining more acceptance in current game development, is one of separating the functionality into individual components that are mostly independent of one another. The traditional object hierarchy is dispensed with, and an object is now created as an aggregation (a collection) of independent components.

Each object now only has the functionality that it needs. Any distinct new functionality is implemented by adding a component.

A system of forming an object from aggregating components can be implemented in one of three ways, which may be viewed as separate stages in moving from a blob object hierarchy to a composite object.

## OBJECT AS ORGANIZED BLOB

A common way of re-factoring a blob object is to break out the functionality of that object into sub-objects, which are then referenced by the first object. Eventually the parent blob object can mostly be replaced by a series of pointers to other objects, and the blob object's member functions become interface functions for the functions of those sub-objects.

This may actually be a reasonable solution if the amount of functionality in your game objects is reasonably small, or if time is limited. You can implement arbitrary object aggregation simply by allowing some of the sub-objects to be absent (by having a NULL pointer to them). Assuming there are not too many sub-objects, then this still allows you the advantage of having lightweight pseudo-composite objects without having to implement a framework for managing the components of that object.

The downside is that this is still essentially a blob. All the functionality is still encapsulated within one large object. It is unlikely you will fully factor the blob into purely sub-objects, so you will still be left with some significant overhead, which will weight down your lightweight objects. You still have the overhead of constantly checking all the NULL pointers to see if they need updating.

## OBJECT AS COMPONENT CONTAINER

The next stage is to factor out each of the components (the "sub-objects" in the previous example) into objects that share a common base class, so we can store a list of components inside of an object.

This is an intermediate solution, as we still have the root "object" that represents the game entity. However, it may be a reasonable solution, or indeed the only practical solution, if a large part of the code base requires this notion of a game object as a concrete object.

Your game object then becomes an interface object that acts as a bridge between the legacy code in your game, and the new system of composite objects. As time permits, you will eventually remove the notion of game entity as being a monolithic object, and instead address the object more and more directly via its components. Eventually you may be able to transition to a pure aggregation.

## OBJECT AS A PURE AGGREGATION

In this final arrangement, an object is simply the sum of its parts. Figure 2 shows a scheme where each game entity is comprised of a collection of components. There is no "game entity object" as such. Each column in the diagram represents a list of identical components, each row can be though of as representing an objects. The components themselves can be treated as being independent of the objects they make up.

![[ccs-209764-0-89904200-1365174059.gif]]
## PRACTICAL EXPERIENCE

I first implemented a system of object composition from components when working at Neversoft, on the Tony Hawk series of games. Our game object system had developed over the course of three successive games until we had a game object hierarchy that resembled the blob anti-pattern I described earlier. It suffered from all the same problems: the objects tended to be heavyweight. Objects had unnecessary data and functionality. Sometimes the unnecessary functionality slowed down the game. Functionality was sometimes duplicated in different branches of the tree.

I had heard about this new-fangled "component based objects" system on the sweng-gamedev mailing list, and decided it sounded like a very good idea. I set to re-organizing the code-base and two years later, it was done.

Why so long? Well, firstly we were churning out Tony Hawk games at the rate of one per year, so there was little time between games to devote to re-factoring. Secondly, I miscalculated the scale of the problem. A three-year old code-base contains a lot of code. A lot of that code became somewhat inflexible over the years. Since the code relied on the game objects being game objects, and very particular game objects at that, it proved to be a lot of work to make everything work as components.

## EXPECT RESISTANCE

The first problem I encountered was in trying to explain the system to other programmers. If you are not particularly familiar with the idea of object composition and aggregation, then it can strike you as pointless, needlessly complex, and unnecessary extra work. Programmers who have worked with the traditional system of object hierarchies for many years become very used to working that way. They even become very good at working that way, and manage to work around the problems as they arise.

Selling the idea to management is also a difficult. You need to be able to explain in plain words exactly how this is going to help get the game done faster. Something along the lines of:

"Whenever we add new stuff to the game now, it takes a long time to do, and there are lots of bugs. If we do this new component object thing, it will let us add new stuff a lot quicker, and have fewer bugs."

My approach was to introduce it in a stealth manner. I first discussed the idea with a couple of programmers individually, and eventually convinced them it was a good idea. I then implemented the basic framework for generic components, and implemented one small aspect of game object functionality as a component.

I then presented this to the rest of the programmers. There was some confusion and resistance, but since it was implemented and working there was not much argument.

## SLOW PROGRESS

Once the framework was established, the conversion from static hierarchy to object composition happened slowly. It is thankless work, since you spend hours and days re-factoring code into something that seems functionally no different to the code it replaces. In addition, we were doing this while still implementing new features for the next iteration of the game.

At an early point, we hit the problem of re-factoring our largest class, the skater class. Since it contained a vast amount of functionality, it was almost impossible to re-factor a piece at a time. In addition, it could not really be re-factored until the other object systems in the game conformed to the component way of doing things. These in turn could not be cleanly refactored as components unless the skater was also a component.

The solution here was to create a "blob component." This was a single huge component, which encapsulated much of the functionality of the skater class. A few other blob components were required in other places, and we eventually shoehorned the entire object system into a collection of components. Once this was in place, the blob components could gradually be refactored into more atomic components.

## RESULTS

The first results of this re-factoring were barely tangible. But over time the code became cleaner and easier to maintain as functionality was encapsulated in discreet components. Programmers began to create new types of object in less time simply by combining a few components and adding a new one.

We created a system of data-driven object creation, so that entirely new types of object could be created by the designers. This proved invaluable in the speedy creation and configuration of new types of objects.

Eventually the programmers came (at different rates) to embrace the component system, and became very adept at adding new functionality via components. The common interface and the strict encapsulation led to a reduction in bugs, and code that that was easier to read, maintain and re-use.

## IMPLEMENTATION DETAILS

Giving each component a common interface means deriving from a base class with virtual functions. This introduces some additional overhead. Do not let this turn you against the idea, as the additional overhead is small, compared to the savings due to simplification of objects.

Since each component has a common interface, it is very easy to add additional debug member functions to each component. That made it a relatively simple matter to add an object inspector that could dump the contents of the components of a composite object in a human readable manner. Later this would evolve into a sophisticated remote debugging tool that was always up to date with all possible types of game object. This is something that would have been very tiresome to implement and maintain with the traditional hierarchy.

Ideally, components should not know about each other. However, in a practical world, there are always going to be dependencies between specific components. Performance issues also dictate that components should be able to quickly access other components. Initially we had all component references going through the component manager, however when this started using up over 5% of our CPU time, we allowed the components to store pointers to one another, and call member functions in other components directly.

The order of composition of the components in an object can be important. In our initial system, we stored the components as a list inside a container object. Each component had an update function, which was called as we iterated over the list of components for each object.

Since the object creation was data driven, it could create problems if the list of components is in an unexpected order. If one object updates physics before animation, and the other updates animation before physics, then they might get out of sync with each other. Dependencies such as this need to be identified, and then enforced in code.

## CONCLUSIONS

Moving from blob style object hierarchies to composite objects made from a collection of components was one of the best decisions I made. The initial results were disappointing as it took a long time to re-factor existing code. However, the end results were well worth it, with lightweight, flexible, robust and re-usable code.