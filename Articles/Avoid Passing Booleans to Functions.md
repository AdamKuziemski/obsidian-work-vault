---
tags:
  - topic/programming/good-practices
source: https://alexkondov.com/should-you-pass-boolean-to-functions/
author: Alex Kondov
read: true
---
Proper software design is a challenge in which functions play a critical part. They are the fundamental building block that groups and ecnapsulated logic. It’s important for them to have a clean and intuitive API so they can be easily understood and used.

Writing good functions is no easy task. We must be wary of their length, the logic and their API. We need to manage side effects and be careful with which functions we’d expose publicly.

Sometimes we’d like a function to behave differently in certain conditions. So a common approach is to pass a boolean parameter, also known as a flag. While this may be initially convenient it is widely considered a bad practice and a code smell.
## Function Cohesion

From what we’ve read in books and articles on clean code we know that functions should be doing only one thing. They should adhere to the single responsibility principle and retain strong cohesion. A boolean argument in a function signals exactly the opposite

A flag is used to alter the behaviour of a function based on the context in which it is used. It is a good signal that a function is doing more than one thing and is not cohesive.

A flag could cause only a minor change in the logical flow of a function. But most times it results in more than a single conditional statement or an adjustment in calculations. The logic may branch off in other directions and lead to different bugs and side effects.

We expose ourselves to a lot of potential problems by having one function do multiple things. If there is no good reason why this should remain so, it’s always better to split the logic in two separate functions. Then the common logic between them could be abstracted and reused.

```javascript
// ❌ Avoid using boolean flags in functions
distance(pointA, pointB, true)

// ✅ Instead, split the logic in two separate functions
distanceInKilometers(pointA, pointB)
distanceInMiles(pointA, pointB)
```
## Readability

The single responsibility principle is not the only problem with flags. Booleans in function parameters hurt readability as well. A function call like the following doesn’t tell you much about what the flag does or what you should expect.

```javascript
distance(pointA, pointB, true)
```

It obviously calculates the distance between two points. But what does the `true` stand for? In order to find out you’d need to inspect it. But you shouldn’t have to go through the implementation of each function to understand it.

Even if a named variable is passed to the function it won’t give you enough context. If the boolean variable was named `isKilometers` how could you know what it would do if `false` was passed? Is it going to use miles, meters or something else?
## Context

Flags are problematic because they can have different purpose based on the context. A similar function called with a flag may mislead you into thinking that it has the same meaning. But another developer may default their calculations to miles and expect the caller to pass `false` when they want to use kilometers.

```javascript
// The flag has the opposite meaning here
function area(points, isMiles) {}

// But the call looks the same
area(points, true)
```

So you may lose a significant amount of time debugging to see why passing `true` to another function is giving you a higher number than you’d expect.
## Booleans Are Limiting

Another problem is that a flag limits you to only two possible options. If in the future you need to have a third one that would case you to refactor a large portion of the codebase. The alternative would be to add another parameter which is even worse.

Imagine that your company sells its software to an agricultural company that now needs areas calculated in hectares. You’d be better off using some alternative approach even when you’ve only got two options for those exact scanarios.

It won’t increase the complexity of the software. In fact it will even make it easier to think about. In TypeScript we can use an enumeration to specify the different values.

```typescript
interface Point {}

enum Units {
  Kilometers,
  Miles,
  Hectares,
}

function distance(from: Point, to: Point, units: Units): Number {}
```

If those are not to your liking we can explicitly specify the string values that an argument could take. This is a often used in React components.

```typescript
interface Points {}

type Unit = 'km' | 'mi' | 'ha'

function distance(from: Point, to: Point, units: Unit): Number {}
```
## When Can It Be Okay?

But as we know most things in software development are subjective. Everyone familiar with the rules of clean code would tell you that passing a boolean is a code smell. That they should be avoided at all costs and bring only turmoil. Nonetheless, you can find such parameters used in most codebases.

We know that the best practice is to avoid them. But I don’t hold blindly to a rule. I can imagine a scenario in which passing a boolean to a function would be okay.

Single argument functions that are named properly make a good exception to the rule. To me the following code is clear enough and leaves no ambiguity on what it is doing.

```javascript
setLoading(true)
```

If you absolutely must pass a boolean make sure that you make its purpose as obvious as possible. In JavaScript you may pass an object to achieve that. This way you force the caller to explicitly write down the flag’s property.

```javascript
distance({
  from: pointA,
  to: pointB,
  isKilometers: true,
})
```

The potential problems we mentioned above are still valid but at least you leave no ambiguity about what the flag does.

## Summary

- Boolean flags are a code smell that can cause debugging and maintainability problems if left unchecked
- Flags are not a best practice because they reduce the cohesion of a function. It does more than one thing.
- Booleans make it hard to understand what a function does based on the call site.
- Boolean arguments should generally be avoided but there are still exceptions to the rule
- Single argument functions and passing an object with named values improves the readability.