---
layout: post
title: "Angular discrete notes"
tags: ["Angular"]
toc: true
notfull: 1
icon: angular.svg
keywords: ""
date: 2022-02-16
---

## Should "always" use `SimpleChanges`

👇 [Source.](https://dev.to/nickraphael/ngonchanges-best-practice-always-use-simplechanges-always-1feg)

```jsx
@Input() myFirstInputParameter: string;

ngOnChanges(changes: SimpleChanges) {
  if (changes.myFirstInputParameter && changes.myFirstInputParameter.currentValue) {
    this.doSomething(this.myFirstInputParameter)
  }
}
```

Changes get called just because of EACH input.

## `:host` with `ViewEncapsulation.None`

👉 [Official doc: ViewEncapsulation](https://angular.io/api/core/ViewEncapsulation)
👉 [Official doc: :host](https://angular.io/guide/component-styles#host)

**Note**: `:host` doesn't work with `ViewEncapsulation.None`!

```css
/* With ViewEncapsulation.Emulated, use :host selector */
:host {
  color: red;
}

/* With ViewEncapsulation.None, use component selector */
app-child-encapsulation-none {
  color: green;
}
```

## `OnPush` Change Detection

👉 [Official doc](https://angular.io/api/core/ChangeDetectionStrategy).
👇 Idea from [this helpful video](https://www.youtube.com/watch?v=qklnVKgxMIY).

- `changeDetection: ChangeDetectionStrategy.OnPush` = auto change detection is deactivated.
- `changeDetection: ChangeDetectionStrategy.Default` (or `CheckAlways`) = change detection is automatic.

```html
<!-- app.component.html -->
<button (click)="onEditCourse1()">Button 1</button>
<button (click)="onEditCourse2()">Button 2</button>
<div>
  <course *ngFor="let course of courses" [course]="course"></course>
</div>
```

```jsx
// app.component.ts
onEditCourse1() {
  this.courses[0].title = "New title";
}
onEditCourse2() {
  const newCourse = { title: "New title };
  this.courses[0] = newCourse;
}
```

```html {% raw %}
<!-- course.component.html -->
<h1>{{ course.title }}</h1>
<input #courseTitle [value]="course.title" (keyup)="onTitleChanged(courseTitle.value)">
{% endraw %}
```

```jsx
// course.component.ts
@Component({
  // with or without
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class AppComponent {
  @Input() course
  constructor(){}

  onTitleChanged(title: string) {
    this.course.title = title;
  }
}
```

- Change value in the `<input>`, the value in `<h1>` **changes too** (*with or without* the `changeDetection`).
- Clicking on "Button 1" (on `app`), nothing changes when `ChangeDetectionStrategy.OnPush` on (But the title changes when we turn this off)! 👈 We mutate **a property** of **@Input()** `course` (but the input is not changed!)
- Clicking on "Button 2", there are changes! 👈 We change **@Input()** `course` object.

## Adding CSS classes

```html
<div
  class="default-class"
	[class.class-A]="condition"
>Content</div>
```

```html
<div
  class="default-class"
	[className]="'class-a'"
>Content</div>
```

```html
<div
  class="default-class"
	[className]="condition ? 'class-a' : 'other-class'"
>Content</div>
```

```html
<div
  class="default-class"
  [ngClass]="{
  	'class-a': condition-a,
    'class-b': condition-b
  }"
>Content</div>
```

