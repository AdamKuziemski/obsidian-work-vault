---
tags:
  - topic/programming/angular
author: Jahollari Enea
source: https://itnext.io/a-change-detection-zone-js-zoneless-local-change-detection-and-signals-story-9344079c3b9d
read: false
---
Angular is a component-driven framework. And just like every other framework out there, it is supposed to show the data to the user and refresh the view when the data changes.

![](https://miro.medium.com/v2/resize:fit:667/1*eVpyKXodMU9thMSDn1XY4Q.png)

A `UserCard` component that shows user data in the template

Over time, we build more and more components and compose them together, and we may end up with a component tree like the one below.

![](https://miro.medium.com/v2/resize:fit:700/1*QbEmmWPDvOTKw60oW-RKmw.png)

Component structure

But, how does Angular know when to refresh the view? How does it know when the data changes? How does it know when to run the change detection?

## Change detection with synchronous code

Let’s start with a simple example. We have a component with a property `name` and a method `changeName`. When we click the button, we call the `changeName` method and change the `name` property.

```typescript
@Component({  
  template: `  
    <button (click)="changeName()">Change name</button>  
    <p>{{ name }}</p>  
  `  
})  
export class AppComponent {  
  name = 'John';

  changeName() {  
    this.name = 'Jane';  
  }  
}
```

When we click the button, the `changeName` method is called, and because everything is wrapped by Angular, we can safely assume that after the name is changed, Angular can run some code to update the view (and everything will be in sync).

> ⚠️ Imaginary under the hood Angular code:

```typescript
component.changeName();  
  
angular.runChangeDetection(); 
```

This works fine! But, most of the time when we change the data, we don’t do it synchronously. We usually make an HTTP request, or have some timers, or wait for some other events to happen before updating the data. And that’s where the problems start.

## Change detection with asynchronous code

Now, let’s say that we want to change the name after 1 second. We can do that with the `setTimeout` function.

```typescript
@Component({  
  template: `  
    <button (click)="changeName()">Change name</button>  
    <p>{{ name }}</p>  
  `  
})  
export class AppComponent {  
  name = 'John';

  changeName() {  
    setTimeout(() => {  
      this.name = 'Jane';  
    }, 1000);  
  }  
}
```

When we click the button, the `changeName` method is called, and the `setTimeout` function is called. The `setTimeout` function will wait for 1 second and then call the callback function. The callback function will change the name to **Jane**.

And now, let’s add the same imaginary under-the-hood Angular code as before.

> ⚠️ Imaginary under the hood Angular code:

```typescript
component.changeName(); 
  
angular.runChangeDetection();
```

Because of the **call stack**, the `setTimeout` callback will be called after the `angular.runChangeDetection` method. So, Angular ran the change detection but the name was not changed yet. And that’s why the view will not be updated. And that’s a broken application ⚠️. (In reality, it’s not, because we have 👇)

## Zone.js to the rescue

Zone.js has been around since the early days of Angular 2.0. It is a library that monkey patches the browser APIs and enables us to hook into the lifecycle of the browser events. What does that mean? It means that we can run our code before and after the browser events.

```typescript
setTimeout(() => {  
  console.log('Hello world');  
}, 1000);
```

The code above will print **Hello world** after 1 second. But, what if we want to run some code before or after the `setTimeout` callback? _You know, for business reasons_ 😄. A framework called **Angular** may want to run some code before and after the `setTimeout` callback.

**Zone.js** enables us to do that. We can create a zone (Angular does create a zone too) and hook into the `setTimeout` callback.

```typescript
const zone = Zone.current.fork({  
  onInvokeTask: (delegate, current, target, task, applyThis, applyArgs) => {  
    console.log('Before setTimeout');  
    delegate.invokeTask(target, task, applyThis, applyArgs);  
    console.log('After setTimeout');  
  }  
});
```

To run our `setTimeout` inside the zone, we need to use the **zone.run()** method.

```typescript
zone.run(() => {  
  setTimeout(() => {  
    console.log('Hello world');  
  }, 1000);  
});
```

Now, when we run the code above, we will see the following output.

Before setTimeout  
Hello world  
After setTimeout

And that’s how zone.js works. It monkey patches the browser APIs and enables us to hook into the lifecycle of the browser events.

## Zone.js + Angular

Angular loads **zone.js** by default in every application and creates a zone called **NgZone**. **NgZone** includes an `Observable` called **onMicrotaskEmpty**. This observable emits a value when there are no more microtasks in the queue. And that’s what Angular uses to know when all the asynchronous code is finished running and it can safely run the change detection.

![](https://miro.medium.com/v2/resize:fit:700/1*3Oe8-a_qFIOnAGEZsfM3vg.png)

NgZone wraps every angular application today

Let’s look at the underlying [code](https://github.com/angular/angular/blob/c4de4e1f894001d8f80b70297c5e576f2d11ec6f/packages/core/src/change_detection/scheduling/ng_zone_scheduling.ts#L31):

```typescript
this._onMicrotaskEmptySubscription = this.zone.onMicrotaskEmpty.subscribe({  
    next: () => this.zone.run(() => this.applicationRef.tick())  
});
```

What we see in the code above is that Angular will call the [**applicationRef.tick()**](https://github.com/angular/angular/blob/c4de4e1f894001d8f80b70297c5e576f2d11ec6f/packages/core/src/application/application_ref.ts#L537) method when the **onMicrotaskEmpty** observable emits a value. What’s this **tick** method 🤔? Do you remember the **runChangeDetection** method from the imaginary under-the-hood Angular code? Well, the **tick** method is the **runChangeDetection** method. It runs the change detection for the whole component tree **_synchronously_**.

But now, Angular knows that all the asynchronous code has finished running and it can **_safely_** run the change detection.

```typescript
tick(): void {  
    for (let view of this._views) {  
        view.detectChanges();   
    }  
}
```

The **tick** method will iterate over all the root views (most of the time we have only one root view/component which is **AppComponent**) and run **detectChanges** synchronously.

## Component Dirty marking

One other thing Angular does is that it marks the component as dirty when it knows something inside the component changed.

These are the things that mark the component as dirty:

- Events (click, mouseover, etc.)

Every time we click a button with a listener in the template, Angular will wrap the callback function with a function called [**wrapListenerIn_markDirtyAndPreventDefault**](https://github.com/angular/angular/blob/c4de4e1f894001d8f80b70297c5e576f2d11ec6f/packages/core/src/render3/instructions/listener.ts#L260). And as we can see from the name of the function 😅, it will mark the component as dirty.

```typescript
function wrapListener(): EventListener {  
  return function wrapListenerIn_markDirtyAndPreventDefault(e: any) {  
      
    markViewDirty(startView);   
  };  
}
```

- Changed inputs

Also, while running the change detection, Angular will check if the input value of a component has changed (**===** check). If it has changed, it will mark the component as dirty. [Source code here](https://github.com/angular/angular/blob/c4de4e1f894001d8f80b70297c5e576f2d11ec6f/packages/core/src/render3/component_ref.ts#L348).

```typescript
setInput(name: string, value: unknown): void {  
    if (Object.is(this.previousInputValues.get(name), value)) {  
        return;  
    }
      
    setInputsForProperty(lView[TVIEW], lView, dataValue, name, value);  
    markViewDirty(childComponentLView);   
}
```

- Output emissions

To listen to output emissions in Angular we register an event in the template. As we saw before, the callback fn will be wrapped and when the event is emitted, the component will be marked as dirty.

Let’s see what this [`markViewDirty`](https://github.com/angular/angular/blob/c4de4e1f894001d8f80b70297c5e576f2d11ec6f/packages/core/src/render3/instructions/mark_view_dirty.ts#L24) function does.

```typescript
export function markViewDirty(lView: LView): LView|null {  
  while (lView) {  
    lView[FLAGS] |= LViewFlags.Dirty;  
    const parent = getLViewParent(lView);  
      
    if (isRootView(lView) && !parent) {  
      return lView;  
    }  
      
    lView = parent!;  
  }  
  return null;  
}
```

As we can read from the comment, the `markViewDirty` function will mark the current view and all ancestors dirty. Let’s see the image below to better understand what that means.

![](https://miro.medium.com/v2/resize:fit:700/1*vWR8BluUbvyEdbezRbhYLw.png)

Dirty marking component and its ancestor up to the root

So, when we click the button, Angular will call our callback fn (_changeName_) and because it’s wrapped with the **wrapListenerIn_markDirtyAndPreventDefault** function, it will mark the component as dirty.

As we said before, Angular uses zone.js and wraps our app with it.

![](https://miro.medium.com/v2/resize:fit:700/1*qlIO2Wh0s9dSGhchJ9rh7g.png)

NgZone wraps Angular apps

After dirty marking to the top, **wrapListenerIn_markDirtyAndPreventDefault** fires and triggers zone.js

![](https://miro.medium.com/v2/resize:fit:700/1*DmRTN6SwNtGGqZA_E16Y_g.png)

Event listeners notify zone.js

Because Angular is listening to the **onMicrotaskEmpty** observable, and because the **(click)** registers an event listener, which zone has wrapped, zone will know that the event listener has finished running and it can emit a value to the **onMicrotaskEmpty** observable.

![](https://miro.medium.com/v2/resize:fit:700/1*I6ceCK5GilF6J65lvyqoAw.png)

**onMicrotaskEmpty** fires when there’s no microtask running anymore

**onMicrotaskEmpty** tells Angular it’s time to run the change detection.

## Component binding refresh

The moment Angular runs the change detection it will check every component from top to bottom. It will go over all the components (**dirty** and **non-dirty**) and check their bindings. If the binding has changed, it will update the view.

![](https://miro.medium.com/v2/resize:fit:700/1*x08OitRwQ2qT7gIs3DF9bA.png)

But, why does Angular check all the components 🤔? Why doesn’t it check only the dirty components 🤔?

Well, because of the change detection strategy.

## OnPush Change Detection

Angular has a change detection strategy called **OnPush**. When we use this strategy, Angular will only run the change detection for a component that is marked as dirty.

First, let’s change the change detection strategy to **OnPush**.

```typescript
@Component({  
  changeDetection: ChangeDetectionStrategy.OnPush  
})  
export class UserCard {}
```

Let’s look at the graphic below to better understand how the change detection works with the **OnPush** strategy.

![](https://miro.medium.com/v2/resize:fit:700/1*zELCaBs7IW92PvceLa_IOA.png)

Some components now are marked as **OnPush (and their children are implicitly OnPush components)**

Let’s do the same thing as before. Click a button in the component and change the name.

First, we will have the **Dirty Marking phase**.

![](https://miro.medium.com/v2/resize:fit:700/1*AeeeFm-_fHGjbFAACMr1hA.png)

Then, the event listener will notify zone.js.

![](https://miro.medium.com/v2/resize:fit:700/1*iP28hqjA-YdazbKm-zg_tA.png)

Event notifies zone.js

When everything async has finished running, **onMicrotaskEmpty** will fire.

![](https://miro.medium.com/v2/resize:fit:700/1*frosM42uC9BbaC8P3fIS5g.png)

Now, Angular will run the **tick** method, and what it will do is traverse all components from top to bottom and check each component.

If the component is:

- **OnPush** + **Non-Dirty** -> **Skip**
- **OnPush** + **Dirty** -> **Check bindings** -> **Refresh bindings** -> **Check children**

![](https://miro.medium.com/v2/resize:fit:700/1*Clr0BonprSL5sPAE-5JRTw.png)

As we can see, by using **OnPush** we can skip parts of the tree that we know haven’t had any changes.

## OnPush + Observables + async pipe

When we work with Angular, observables have been our number one tool to manage data and state changes. To support observables, Angular provides the **async** pipe. The **async** pipe subscribes to an observable and returns the latest value. To let Angular know that the value has changed, it will call the **markForCheck** method that comes from the **ChangeDetectorRef** class (the component’s **ChangeDetectorRef**).

```typescript
@Pipe()  
export class AsyncPipe implements OnDestroy, PipeTransform {  
  constructor(ref: ChangeDetectorRef) {}

  transform<T>(obj: Observable<T>): T|null {  
      // ...
  }

  private _updateLatestValue(async: any, value: Object): void {  
    this._ref!.markForCheck();   
  }  
}
```

I’ve written more about it here (create async pipe from scratch and understand how it works):

And what the **markForCheck** method does is that it will just call the `markViewDirty` function that we saw before.

```typescript
markForCheck(): void {  
  markViewDirty(this._cdRefInjectingView || this._lView);  
}
```

So, the same as before, if we use observables with **async** pipe in the template it will act the same as if we used the **(click)** event. It will mark the component as dirty and Angular will run the change detection.

![](https://miro.medium.com/v2/resize:fit:700/1*LRpedou2RqKPanLTPRUbTQ.png)

data$ | async pipe marks component as dirty

## OnPush + Observables + Who is triggering zone.js?

If our data changes without our interaction (click, mouseover etc.) probably there is a `setTimeout` or **setInterval** or an HTTP call being made somewhere under the hood that triggers zone.js.

Here’s how we can easily break it 🧨

```typescript
@Component({  
  selector: 'todos',  
  standalone: true,  
  imports: [AsyncPipe, JsonPipe],  
  template: `{{ todos$ | async | json }}`,  
  changeDetection: ChangeDetectionStrategy.OnPush,  
})  
export class TodosComponent {  
  private http = inject(HttpClient);  
  private ngZone = inject(NgZone);

  todos$ = of([] as any[]);

  ngOnInit() {  
    this.ngZone.runOutsideAngular(() => {  
      setTimeout(() => {  
          
        this.todos$ = this.getTodos();  
      });  
    });  
  }

  getTodos() {  
    return this.http  
      .get<any>('https://jsonplaceholder.typicode.com/todos/1')  
      .pipe(shareReplay(1));  
  }  
}
```

What we have done here is:

- In **ngOnInit,** we have used **ngZone.runOutsideAngular()** an API that allows us to run things outside the Angular zone.
- We use `setTimeout` (to skip the first task being run and also because Angular runs change detection at least once by default) and inside the **setTimeo``, we assign a value to the observable (yay we have a change).
- Because `setTimeout` won’t run inside the zone, also the API call will be made outside the zone because the code is run inside **runOutsideAngular,** there is nothing notifying zonejs that something changed.
- Run this code in your app and see that only “**[]”** will be shown in the browser.
- **Broken State 🧨!**

Not great 😄! But, one other thing that we start questioning is:

## Why do we need to mark all the ancestors dirty?

The reason for this is simple, if we don’t mark all ancestors as dirty, we can get a broken state even faster. How?

Let’s see the above example again, but now, mark only the component and its children as dirty.

![](https://miro.medium.com/v2/resize:fit:700/1*WR3R89nCi2ej-J0JDBUKjg.png)

So, we mark only the component that had the click and its children to be marked for check. The moment the **tick** happens it will get to the parent component which is **OnPush,** check that it’s not dirty, and skip it.

![](https://miro.medium.com/v2/resize:fit:700/1*eTO0ASGOnxsQm9sW-i6EVQ.png)

Broken state if we don’t mark ancestors as dirty when using markForCheck

That’s how we get to a broken state again 🧨!

## Why can’t we just run the change detection for the component that is marked as dirty?

We can do that using the **detectChanges** method in the **ChangeDetectorRef** class. But it has its drawbacks. Because that method runs change detection synchronously, it can cause performance issues. Because everything will be done in the same browser task, it may block the main thread and cause jank. Imagine detecting changes for a list of 100 items every 1 or 2 seconds. That’s a lot of work for the browser.

## ==**markForCheck vs detectChanges (coalesced run vs sync runs)**==

When we use markForCheck we just tell Angular that a component is dirty, and nothing else happens, so even if we call markForCheck 1000 times it’s not going to be an issue. But, when we use **detectChanges**, Angular will do actual work like checking bindings and updating the view if needed. And that’s why we should use **markForCheck** instead of **detectChanges**.

## Can’t we schedule** detectChanges **in the next browser task?

We can, that’s what [**push pipe**](https://www.rx-angular.io/docs/template/api/push-pipe) or [**rxLet**](https://www.rx-angular.io/docs/template/api/rx-let-directive) directive from [**rx-angular**](https://www.rx-angular.io/) does. It schedules the change detection in the next browser task. But, it’s not a good idea to do that for every component. Because, if we have a list of 100 items, and we schedule the change detection for every item, we will have 100 browser tasks. And that’s not good for performance either.

## Signals 🚦

The frontend world is moving towards signals. Solid.js, Svelte, Vue, and Angular are creating their signal implementations. And that’s because signals are a better way to **manage state** and **state changes**.

Signals in Angular have brought a lot of **DX** benefits. We can easily create and derive state and also run side effects when the state changes using effects. We don’t have to subscribe to them, we don’t have to unsubscribe from them, and we don’t have to worry about memory leaks 🧯.

We can just call them and they will return their current value.

```typescript
const name = signal('John');   
const upperCaseName = computed(() => name().toUpperCase()); 

effect(() => {  
  console.log(name() + ' ' + upperCaseName());   
});

name.set('Jane'); 
```

We can also use signals in the template just like normal function calls.

```typescript
@Component({  
  template: `  
    <button (click)="name.set('Jane')">Change name</button>  
    <p>{{ name() }}</p>  
  `  
})  
export class AppComponent {  
  name = signal('John');  
}
```

If you ask if calling a function in the template is a good idea, I would say that it’s a good idea if the function call is cheap, and calling a signal is cheap. It’s just a function call that returns a value (without computing anything).

I’ve also written about it here:

## Signals and Change Detection

In v17 Angular change detection got an upgrade 🚀!

Angular templates now understand signals as something more than function calls. Below is one of the PRs making this a reality.

Before we used the **async** pipe, so it would call the **markForCheck** method, and with signals, we just have to normally call them. Angular now will register an **effect** (_consumer_) that will listen to this signal and mark the template for check every time the signal changes.

![](https://miro.medium.com/v2/resize:fit:700/1*JvVZ73v8E5zXRzyCOnt3Vg.png)

The first benefit is that we don’t need async pipe anymore 🎉.

The second PR that improves change detection is this one:

Which solved an issue not related to signals but to change detection itself (which I won’t explain in detail).

By using the mechanism introduced by it, we got the 3rd PR which added **Glo-cal** change detection (Global + Local change detection) (a term coined by my friend @[Matthieu Riegler](https://twitter.com/Jean__Meche) )

So let’s better understand glo-cal (local) change detection 👇

## Local Change Detection (Targeted mode)

One of those PRs I linked above introduced two new flags in Angular.

![](https://miro.medium.com/v2/resize:fit:522/1*gvQx3FEpysqIW8RpfX91Pw.png)

Two new flags (RefreshView & HAS_CHILD_VIEWS_TO_REFRESH)

How do they work?

When the template effect runs, Angular will run a function called **markViewForRefresh** which sets the current component flag to **RefreshView** and then calls **markAncestorsForTraversal** which will mark all the ancestors with **HAS_CHILD_VIEWS_TO_REFRESH.**

```typescript
export function markViewForRefresh(lView: LView) {  
  if (lView[FLAGS] & LViewFlags.RefreshView) {  
    return;  
  }  
  lView[FLAGS] |= LViewFlags.RefreshView;  
  if (viewAttachedToChangeDetector(lView)) {  
    markAncestorsForTraversal(lView);  
  }  
}
```

And here’s how it looks in the graph (updated tree structure to showcase more edge cases)👇

![](https://miro.medium.com/v2/resize:fit:700/1*DRDqyuI6pLlELqlA48m5vw.png)

So, the component that has signal changes is marked with the orange color border and the parents now have the ⏬ icon to tell that they have child views to refresh.

> NOTE: We still depend on zone.js to trigger change detection.

The moment zone.js kicks in (the same reasons as before) it will call **appRef.tick()** and then we will have top-down change detection with some differences and new rules!

## Targeted Mode Rules

NgZone triggers change detection in **GlobalMode** (it will go top-down checking & refreshing all components)

In **GlobalMode** we check **CheckAlways** _(normal component without any change detection strategy set)_ and **Dirty OnPush** components

**What triggers TargetedMode?**

- When in **GlobalMode** we encounter a **Non-Dirty OnPush** component, we switch to **TargetedMode!**

In **TargetedMode:**

- Only refresh a view if it has the **RefreshView** flag set
- **DO NOT** Refresh **CheckAlways** or regular **Dirty** flag views
- If we reach a view with **RefreshView** flag, traverse children in **GlobalMode**

Let’s go one by one on components.

1. Root component is a normal component (**CheckAlways**) so we just check & refresh bindings if needed and we continue to its children.

![](https://miro.medium.com/v2/resize:fit:700/1*MwEruHykzCIvzX901X4WOw.png)

2. All **CheckAlways** components will continue to work the same as before.

![](https://miro.medium.com/v2/resize:fit:700/1*JbNnofQxP7dnAqRpjmro_Q.png)

All CheckAlways components are refreshed

3. OnPush will continue to work the same, so if it’s not marked as dirty it won’t be checked.

4. If we check the other component that is **OnPush** + **HAS_CHILD_VIEWS_TO_REFRESH** but not dirty we get our trigger for **TargetedMode** (check the rules above)

![](https://miro.medium.com/v2/resize:fit:700/1*XmdIz4ykbWYzPGWeAYfMqg.png)

6. The component itself is not going to be refreshed, let’s go to the children

![](https://miro.medium.com/v2/resize:fit:700/1*OltY0HzHDhj1MDLYEMabuw.png)

**TargetedMode** on **CheckAlways** component -> Skip

7. Then we reach a **RefreshView** component and we are on **TargetedMode**, which means we refresh the bindings. We also convert to **GlobalMode** to make sure **CheckAlways** children components also get refreshed correctly.

![](https://miro.medium.com/v2/resize:fit:700/1*67Qjlpzdqai93xdGgH8Img.png)

8. Now we are in **GlobalMode** and we have a **CheckAlways** component, so we just refresh normally)

![](https://miro.medium.com/v2/resize:fit:700/1*uvzUYErJ3jjenEzC9h2itA.png)

That’s all about the new Targeted Change Detection.

If we look at the final tree, we can see that we skipped more components than before when we reach an OnPush component that is not dirty.

![](https://miro.medium.com/v2/resize:fit:700/1*IAhmVb58R8oaE-IrGid43A.png)

> Targeted Change Detection = OnPush without footguns 🔫

You can play with all these change detection rules in this app by [Mathieu Riegler](https://github.com/JeanMeche) 🔨

![](https://miro.medium.com/v2/resize:fit:700/1*eqLX13lrKYs_4tKk9uk-hQ@2x.png)

Understand Angular Change Detection

## Zoneless Angular —** Let’s remove zone.js from Angul

The moment we remove zone.js from Angular we are left with code that runs but doesn’t update anything in the view (The bootstrap time of zone.js and all the pressure it puts in the browser gets removed too! We also remove `15kb` from the bundle size 😎). Because nothing is triggering **appRef.tick()**.

But, Angular has some APIs that tell it that something changed. Which ones?

- markForCheck (used by async pipe)
- signal changes
- event handlers that mark view dirty
- setting inputs on components created dynamically with **setInput()**

Also, OnPush components already works with the idea that it needs to tell Angular that something changed.

So, instead of having zone.js schedule **tick()** we can have Angular schedule **tick()** when it knows something changed.

In this PR (**experimental**) we can see that `markViewDirty` now will notify the **changeDetectionScheduler** that something changed.

```typescript
export function markViewDirty(lView: LView): LView|null {  
  lView[ENVIRONMENT].changeDetectionScheduler?.notify();  
}
```

The scheduler is supposed to schedule tick(), as we can see in this other **experimental** implementation of a zoneless scheduler.

```typescript
@Injectable({providedIn: 'root'})  
class ChangeDetectionSchedulerImpl implements ChangeDetectionScheduler {  
  private appRef = inject(ApplicationRef);  
  private taskService = inject(PendingTasks);  
  private pendingRenderTaskId: number|null = null;

  notify(): void {  
    if (this.pendingRenderTaskId !== null) return;

    this.pendingRenderTaskId = this.taskService.add();  
    setTimeout(() => {  
      try {  
        if (!this.appRef.destroyed) {  
          this.appRef.tick();  
        }  
      } finally {  
        const taskId = this.pendingRenderTaskId!;  
        this.pendingRenderTaskId = null;  
        this.taskService.remove(taskId);  
      }  
    });  
  }  
}
```

This is experimental, but we can see that basically, it will coalesce all notify() calls and run them only once (in this code is only once per macroTask — setTimeout, but maybe we get it only once per microTask — Promise.resolve())

**What are we supposed to understand from this?**

Apps that are currently using the OnPush change detection strategy will work fine in a zoneless angular world.

## Zoneless Angular !== Glo-cal (local) change detection

Zoneless Angular is not the same as local change detection. Zoneless Angular is just removing **zone.js** from Angular and using the APIs that Angular already has to schedule tick().

**Real Local change detection** is a new feature that will allow us to run change detection for only a sub-tree of components (not the whole component tree) that currently use the OnPush change detection strategy.

## Signals change detection (No OnPush, No Zone.js, Signals only)

One thing signal change detection will bring is native unidirectional data flow (two-way data binding without headaches).

Watch this video **Rethinking Reactivity w/ Alex Rickabaugh | Angular Nation**

While glo-cal change detection with OnPush and zoneless are great, with only signal components, we probably can do even better.

What if we didn’t have to use **OnPush**? Or mark the parents with **HAS_CHILD_VIEWS_TO_REFRESH** and run change detection for the whole component tree? What if we could just run change detection for the views inside components that have changed?

Read more in the RFC:

## Happy New Year 2024! 🎉 A signals year for Angular 🚦!

This year Angular brought us a lot of new features. And I’m sure that next year will be even better.

Make sure to check out the

- [Angular roadmap for 2024](https://angular.dev/roadmap) 🎉
- [Angular Christmas Calendar 2023](https://angularchristmascalendar.com/)