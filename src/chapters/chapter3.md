## Application Logic

In this chapter you’ll learn how to add JavaScript logic to your app, how to create services that talk to backend endpoints, and how to architect an app that uses multiple pages. There’s a lot to cover, so let’s start by discussing how to handle events and data binding.

### Events

Most user interfaces are driven by events. In NativeScript apps, those events are usually some user action, such as tapping, swiping, or rotating—and NativeScript abstracts the iOS- and Android-specific code for handling such events into a handful of easy-to-use APIs. Let’s start with the most common event you’ll use in a NativeScript app—`tap`.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a `tap` event handler
</h4>

Open `app/app.component.ts`, find the existing sign in button within your component’s `template` (`<Button text="Sign in"></Button>`), and replace it with the following code:

``` JavaScript
<Button text="Sign in" (tap)="signIn()"></Button>
```

Next, in the same file, replace the current `AppComponent` definition with the one shown below:

``` JavaScript
export class AppComponent {
  signIn() {
    console.log("hello");
  }
}
```

<div class="exercise-end"></div>

The `(eventName)="functionName()"` syntax is part of [Angular 2’s event binding system](https://angular.io/docs/ts/latest/guide/template-syntax.html#!#event-binding), which lets you bind an event that occurs on a UI element to a function in your component’s class. In this case, the `(tap)="signIn()"` syntax you just used tells Angular to run the `AppComponent` class’s `signIn()` function whenever the user taps the sign in button.

To verify this binding works you can try tapping the sign in button now; you should see “hello” logged in your terminal or command prompt as is shown below:

![Terminal showing the word hello logged](images/chapter3/terminal-1.png)

### Data Binding

### Services

### Routing
