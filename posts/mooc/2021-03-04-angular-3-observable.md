---
layout: post
title: "MOOC: Angular 3 - Observable"
tags: [MOOC, Angular]
toc: true
icon: angular.svg
keywords: "observable subject eventemitter subscribe rxjs error complete stream subscribe subscription create operations learnrxjs udemy maximilian observer hooks unsubscribe @Output"
---

{% assign img-url = '/img/post/angular' %}

This is my note for the course "[Angular - The Complete Guide (2021 Edition)](https://www.udemy.com/course/the-complete-guide-to-angular-2/learn/lecture/13914134#overview)" which is taught by Maximilian SchwarzmΓΌller on Udemy. This is an incredible course for you to start to learn Angular from basic. The official Angular docs is good but it's not for the beginner.

π **PART 1** β [Angular  1 - Basics & Components & Databinding & Directives](/angular-1-basics-components-databinding-directives/)
π **PART 2** β [Angular 2 - Services & Dependency Injection & Routing](/angular-2-service-dependency-injection-routing/)
π **PART 3** β [Angular 3 - Observable](/angular-3-observable/)
π **PART 4** β  [Angular 4 - Forms](/angular-4-forms/)

π [Github repo](https://github.com/dinhanhthi/learn-angular-complete-guide)
π **Other note** (taken before this course): [Angular 101](https://www.notion.so/Angular-101-fcbd5683f8e941f89c709595792b62d2)
π Other note: [RxJS](https://www.notion.so/RxJS-b4bc7e2d1f7d49b79d4cb701785e355f).

::: warning
This note contains only the important things which can be look back later, it cannot replace either [the course](https://www.udemy.com/course/the-complete-guide-to-angular-2/learn/lecture/13914134#overview) nor [the official document](https://angular.io/start)!
:::

## Docs

- [Learn RxJS](https://www.learnrxjs.io/)

## What's Observable?

π Codes: [finish custom observables](https://github1s.com/dinhanhthi/learn-angular-complete-guide/tree/master/14-observable/obs-03-complete-custom-observable/src/app).

- An observable = a data source.
- Observable patterns β *observable* β- stream β- *observer*
- *Observer* (in previous codes, you subscribe something to the event from observable) β 3 type of data received (3 hooks): Handle data, Handle error, Handle completion.
- Observables are contracts to which you subscribe to be informed about the changes in data.

![Angular_3_-_Observable_f0445b12735d4542956fd4f2eefca8e4/Untitled.png]({{ img-url }}/angular-3/Untitled.png)

## Create observable

There are several ways to create an observable from rxjs package.

```jsx
// home.component.ts
import { interval, Subscription } from 'rxjs';

export class ... implements OnInit, OnDestroy {
	private firstObsSub: Subscription;

	ngOnInit() {
		this.firstObsSub = interval( 1000 ) // every seconds, a new event will be emitted
			.subscribe( count => {
				console.log(count); // log to the console
			})
	}

	// we have to .unsubscribe() -> if not, we get more and more observables
	//   whenever we get back to home
	ngOnDestroy(): void {
//|						// ^return nothing
//^whenever we leave the component
		this.firstObsSub.unsubscribe(); // prevent memory leak
	}
}
```

All observables provided by Angular β no need to import from "rxjs" + no need to unsubscribe().

### `.subscribe()`

```jsx
customIntervalObs.subscribe(
	<function_for_next>, // performed when get an emit
	<function_for_error>, // performed when there is error
	<function_for_complete> // perform when finish getting emit
);
```

You rarely build your own observable β use the ones given by angular or somewhere else. Below are just for more understanding how to do so if you want!

### `.create()`

Create manually `interval()`,

```jsx
import { Observable } from 'rxjs';

export ... {
	private customObsSub: Subscription;

	ngOnInit() {
		const customIntervalObs = Observable.create(observer => {
			let count = 0;
			setInterval(() => {
				observer.next(count);
						//  ^method to emit a new value, there are also: .error(), .complete()
				count++;
			}, 1000);
		});

		customObsSub = customIntervalObs.subscribe(data => {
			console.log(data);
		});
	}

	ngOnDestroy(): void {
		this.customObsSub.unsubscribe();
	}
}
```

### `.error()`

If we send HTTP request β we usually get .

```jsx
// using the same structure of code as previous code box
setInterval(() => {
	observer.next(count);
	if (count > 3) {
		observer.error(new Error('Count is greater than 3!'));
	} // stop subscription -> observer ends at "4"
	count++;
}, 1000);

customObsSub = customIntervalObs.subscribe(data => {
	console.log(data);
}, error => {
	console.log(error); // or you could send error to backend
	alert(error.message); // "Count is greater than 3!"
});
```

### `.complete()`

There are some observables can be completed β what we will do when it happens?

Cancled due to an error IS DIFFERENT than it completes! β complete function in `.subscribe()` is not executed when there is an error!

```jsx
setInterval(() => {
	observer.next(count);
	if (count === 2) {
		observer.complete(); // complete observable before count=3
	}
	if (count > 3) {
		observer.error(new Error('Count is greater than 3!'));
	}
	count++;
}, 1000);

customObsSub = customIntervalObs.subscribe(data => {
	console.log(data);
}, error => {
	console.log(error); // or you could send error to backend
	alert(error.message); // "Count is greater than 3!"
}, () => {
	console.log("Completed!"); // you don't need to unsubscribe if your observable
														 //   did complete BUT there nothing wrong if you
														 //   keep using .unsubcribe()
});
```

## Operators

π [Operators - Learn RxJS](https://www.learnrxjs.io/learn-rxjs/operators/)
π [Codes for this section](https://github1s.com/dinhanhthi/learn-angular-complete-guide/blob/master/14-observable/obs-04-operators/src/app/home/home.component.ts).

```jsx
import { map } from 'rxjs/operators';
```

Operators are the magic features of the RxJS library turn observables β awesome construct!

![Angular_3_-_Observable_f0445b12735d4542956fd4f2eefca8e4/Untitled_1.png]({{ img-url }}/angular-3/Untitled_1.png)

Example: we wanna add "Round" before the number emiited (Round 1, Round 2,....) β an old option is to add "Round" in the `.subscribe()` function. β We can do that before theh subscription using Operators! β `.pipe()` comes in!

```jsx
this.firstObsSubscription = customIntervalObservable.pipe(filter(data => {
    return data > 0;
  }), map((data: number) => {
    return 'Round: ' + (data + 1);
  })).subscribe(data => {
    console.log(data);
  }, ...);
```

We need a transformation of the received data before you subscribe β using operators.

With `.pipe()`, we can apply 1 or more operators inside it!

```jsx
// import operators from rxjs/operators first!
.pipe(<operator>, <operator>, ...)
```

## Subjects

π [Codes for this section](https://github1s.com/dinhanhthi/learn-angular-complete-guide/blob/master/14-observable/obs-05-finished/src/app/user.service.ts).

Instead of using `EventEmitter` (from `@angular/core`), to emit and subscribe event between components, we can use a subject!

```jsx
// user.service.ts
import { Subject } from 'rxjs';

@Injectable({providedIn: 'root'})
export class UserService {
  activatedEmitter = new Subject<boolean>();
}

// compare with using EventEmitter
@Injectable()
export ... {
	activatedEmitter = new EventEmitter<boolean>();
}
```

```jsx
// user.component.ts
onActivate() {
	this.userService.activatedEmitter.next(true);
}

// compare with EventEmitter
onActivate() {
	this.userService.activatedEmitter.emit(true);
}
```

```jsx
// app.component.ts
// we use the same for both EventEmitter and Subject
ngOnInit() {
	this.userService.activateEmitter.subscribe(...);
}
```

Unlike normal observable (you can created) β `.next()` is used INSIDE the observable (when you created it) β subject is different: we can call `.next()` outside the observable!

![Angular_3_-_Observable_f0445b12735d4542956fd4f2eefca8e4/Untitled_2.png]({{ img-url }}/angular-3/Untitled_2.png)

There are also other subclasses for Subjects: `BehaviorSubject`, `ReplaySubject`,...

**GOOD PRACTICE**:

- Don't use `EventEmitter`, use `Subject`!!!! β don't forget to unsubcribe to Subect when you don't need them. β by storing the subscription + then unsubscribe().
    - Only use Subject to communicate across components through services/mechanisms where you in the end subscribe to somewhere!
        - Not subscribe to an event which probably is an output if you do plan to subscribe manually!
- ONLY USE `EventEmitter` on the `@Output` property!! β EventEmitter gets cleaned auto (no need unsubscribe)