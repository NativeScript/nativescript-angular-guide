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

Next, in the same file, replace the current `AppComponent` declaration with the one shown below:

``` JavaScript
export class AppComponent {
  signIn() {
    console.log("hello");
  }
}
```

<div class="exercise-end"></div>

The `(eventName)="functionName()"` syntax is part of [Angular 2’s event binding system](https://angular.io/docs/ts/latest/guide/template-syntax.html#!#event-binding), which lets you bind an event that occurs on a UI element to a function in your component’s class. In this case, the `(tap)="signIn()"` syntax tells Angular to run the `AppComponent` class’s `signIn()` function whenever the user taps the sign in button.

To verify this binding works try tapping the sign in button in your app; you should see “hello” logged in your terminal or command prompt as such:

![Terminal showing the word hello logged](images/chapter3/terminal-1.png)

> **TIP**:
> * In NativeScript you can find a list of events available in the appropriate UI element’s API documentation. For instance, the [button element’s API documentation](http://docs.nativescript.org/ApiReference/ui/button/Button) lists its `tap` event.
> * The Angular 2 docs have a helpful [cheat sheet](https://angular.io/docs/ts/latest/guide/cheatsheet.html) that includes the various syntaxes available when building templates. Don’t worry too much about knowing how all these work at the moment; we’ll progressively introduce the most common syntaxes in this guide.

With the `tap` event in place, we now have a way of tying our UI elements in our template to TypeScript code. To make a login page actually work though, we need to introduce one other way of connecting a template to code: data binding.

### Data Binding

Angular 2 provides several ways to bind data in your TypeScript code to UI controls, and through the magic of NativeScript those same methods are available in your iOS and Android apps.

The first of these is a way to bind UI attributes to properties defined in your TypeScript class. Let’s look at how it works.

<h4 class="exercise-start">
    <b>Exercise</b>: Using Angular 2 attribute binding
</h4>

In `app/app.component.ts` replace the current `AppComponent` declaration with the one shown below, which adds a new `email` property, and changes the `signIn()` method to display its value:

``` JavaScript
export class AppComponent {
  email = "nativescriptrocks@telerik.com";
  signIn() {
    alert("You’re using: " + this.email);
  }
}
```

Next, find the first `<TextField>` in your component’s `template` and replace it with the code below, which adds a new `text` attribute:

``` JavaScript
<TextField hint="Email Address" keyboardType="email" [text]="email"
  autocorrect="false" autocapitalizationType="none"></TextField>
```

<div class="exercise-end"></div>

The key thing to note here is the `[text]="email"` attribute on the `<TextField>`. This is Angular 2’s syntax for attribute binding, and it allows you to bind the value of an attribute to a property in your TypeScript class. In this case, the `text` attribute of the `<TextField>`—which is roughly equivalent to a DOM `<input>`’s `value` attribute—is bound to the `AppComponent`’s `email` attribute. Therefore the app now has an email address prefilled when it loads:

![Android with prefilled email](images/chapter3/android/1.png)
![iOS with prefilled email](images/chapter3/ios/1.png)

> **NOTE**: It’s very easy to confuse Angular 2’s event binding syntax `(eventName)="functionName()"` with its attribute binding syntax `[attributeName]="propertyName"` 🤔. Don’t worry though; if you get them backwards, the problem is really easy to debug, as the functionality you’re attempting to add just won’t work. These syntaxes are common enough that you’ll be using them a lot, and eventually you should be able to commit them to memory.

This attribute binding approach works really well when you need the data binding to be one way—that is, when you need TypeScript properties, and changes to those properties done in TypeScript code, to appear in the user interface. But in the case of user interface controls that accept user input, such as the text field in this example, usually you need data binding to work two way—and actually, it’s far easier to show this limitation in the current code.

Head back to your app, change the text field value (type a few extra letters or something like that), and then tap the Sign In button. Because your `signIn()` function alerts the current value of your component’s `email` property—`alert("You’re using: " + this.email)`—you might expect to see the updated value in the alert. Instead, however, you see the original value. Notice how the typed text and the alert value don’t match in the screenshot below.

![iOS with email address that doesn’t match](images/chapter3/ios/2.png)



### Services

### Routing
