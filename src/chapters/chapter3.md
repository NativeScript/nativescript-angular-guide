## Application Logic

In this chapter you’ll learn how to add JavaScript logic to your app, how to create services that talk to backend endpoints, and how to architect an app that uses multiple pages. There’s a lot to cover, so let’s start by discussing how to handle events and data binding.

### Events

Most user interfaces are driven by events. In NativeScript apps, those events are usually some user action, such as tapping, swiping, or rotating—and NativeScript abstracts the iOS- and Android-specific code for handling such events into a handful of easy-to-use APIs. Let’s start with the most common event you’ll use in a NativeScript app: `tap`.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a `tap` event handler
</h4>

Open `app/app.component.ts`, find the existing sign in button within your component’s `template` (`<Button text="Sign in"></Button>`), and replace it with the following code:

``` JavaScript
<Button text="Sign in" id="submit-button" (tap)="signIn()"></Button>
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

To verify this binding works tap the “Sign In” button in your app; you should see “hello” logged in your terminal or command prompt as such:

<img alt="Terminal showing the word hello logged" src="images/chapter3/terminal-1.png" class="plain">

> **TIP**:
> * In NativeScript you can find a list of events available in the appropriate UI element’s API documentation. For instance, the [button element’s API documentation](http://docs.nativescript.org/ApiReference/ui/button/Button) lists its `tap` event.
> * The Angular 2 docs have a helpful [cheat sheet](https://angular.io/docs/ts/latest/guide/cheatsheet.html) that includes the various syntaxes available when building templates. Don’t worry too much about knowing how all these work at the moment; we’ll progressively introduce the most common syntaxes in this guide.

With the `tap` event in place, you now have a way of tying the UI elements in your template to your TypeScript code. To make a login page actually work though, we need to introduce one other way of connecting a template to code: data binding.

### Data Binding

Angular 2 provides several ways to bind data in your TypeScript code to UI controls, and through the magic of NativeScript those same methods are available in your iOS and Android apps.

The first of these is a way to bind UI attributes to properties defined in your TypeScript class. Let’s look at how it works.

<h4 class="exercise-start">
    <b>Exercise</b>: Using Angular 2 attribute binding
</h4>

In `app/app.component.ts` replace the current `AppComponent` declaration with the one shown below, which adds a new `email` property, and changes the `signIn()` method to display its value:

``` TypeScript
export class AppComponent {
  email = "nativescriptrocks@telerik.com";
  signIn() {
    alert("You’re using: " + this.email);
  }
}
```

Next, find the first `<TextField>` in your component’s `template` and replace it with the code below, which adds a new `text` attribute:

``` XML
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

To fix this, you need to switch to Angular 2’s two-way data binding syntax.

<h4 class="exercise-start">
    <b>Exercise</b>: Two-way data binding with Angular 2
</h4>

In `app/app.component.ts`, find the find the first `<TextField>`, and replace it with the `<TextField>` below, which introduces a new `[(ngModel)]` attribute:

``` XML
<TextField hint="Email Address" keyboardType="email" [(ngModel)]="email"
  autocorrect="false" autocapitalizationType="none"></TextField>
```

<div class="exercise-end"></div>

At first glance the `[(ngModel)]` syntax looks more than a little odd, as it’s essentially a combination of the event and attribute binding syntax that you used in earlier examples. And that’s because the syntax actually is a shorthand for both an attribute binding and an event binding, or in code—`[text]="email" (emailChange)="email=$event"`, which binds a text field’s `text` attribute property to an `email` property, as well as adds a `change` event handler that updates the `email` property’s value whenever the user makes a change.

And, if you again modify your app’s email address and click the Sign In button, you’ll see the updated value in the alert as expected:

![iOS with email address that does match](images/chapter3/ios/3.png)

At this point, you have a basic login screen setup with two-way data binding—not bad for 20 some lines of code of TypeScript. (Think about how much code you’d have to write in Android Studio _and_ Xcode to accomplish the same task.) To this point though you’ve been placing all of your logic in a single TypeScript file, which doesn’t scale all that well for real-world applications.

Before we tie this app to a backend and make this login screen fully functional, let’s take a step back and setup a structure that can scale.

### Structuring your app

There are many reasons to segment your application into modular units, and you can [read about the various benefits on Wikipedia](https://en.wikipedia.org/wiki/Modular_programming). However, keeping NativeScript apps modular has one unique benefit: the ability to share the code you write between Angular-2-built web apps, and Angular-2-build native apps.

Even if you have no plans to create an Angular 2 web app, separating out your code is still advantageous for a number of other reasons—testability, ease of maintenance, and so forth—but if you _do_ have plans to build an Angular 2 web app, having a chunk of functionality that you can reuse for your native and web apps can be an invaluable time saver.

To see how this works in action let’s edit some files in the `/shared` folder and set them up to be imported.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a model object
</h4>

The first thing we’ll do is add a model object to store user data. Open `app/user/user.ts` and paste in the following code:

``` JavaScript
export class User {
  email: string;
  password: string;
}
```

This code defines a simple [TypeScript class](http://www.typescriptlang.org/Handbook#classes) that does nothing more than define two properties—`email` and `password`. Note the use of [TypeScript’s `export` keyword](http://www.typescriptlang.org/Handbook#modules-going-external), as we’ll see why that’s important momentarily.

Next, open `app/app.component.ts`, and first add the following `import` to the top of the file:

``` JavaScript
import {User} from "./shared/user/user";
```

Here you import the `User` class that you just defined. Note the parallel between the `export` command used in the previous example and the `import` command used in the previous example. The reason the `User` class is available to import is because it was explicitly exported. You’ll see other examples of `import` and `export` as you go through this guide.

Next, replace the existing `AppComponent` definition with the one below, which uses the `User` class you just imported.

``` JavaScript
export class AppComponent {
  user: User;
  constructor() {
    this.user = new User();
  }
  signIn() {
    alert("You’re using: " + this.user.email);
  }
}
```

Instead of storing data on the `AppComponent` directly, you’re now using the `User` model object, which is reusable outside of this page and even outside of this application. You also provide a TypeScript constructor, which is a function invoked when an instance of the `AppComponent` class is instantiated. In this app, Angular 2 instantiates an `AppComponent` for you with the call to `nativeScriptBootstrap(AppComponent)` done in `main.ts`. 

Your final step is to use this new model object in your template. To do that, replace the existing two `<TextField>`s with the code shown below, which updates the `[(ngModel)]` bindings to point at the new `User` object:

``` XML
<TextField hint="Email Address" keyboardType="email" [(ngModel)]="user.email"
  autocorrect="false" autocapitalizationType="none"></TextField>
<TextField hint="Password" secure="true" [(ngModel)]="user.password"></TextField>
```

If you got lost during this section, here’s a copy-and-paste friendly version of the full `app.component.ts` you should have at this point:

``` JavaScript
import {Component} from "angular2/core";
import {User} from "./shared/user/user";

@Component({
  selector: "my-app",
  template: `
    <StackLayout>
      <Image src="res://logo" stretch="none" horizontalAlignment="center"></Image>

      <TextField hint="Email Address" keyboardType="email" [(ngModel)]="user.email"
        autocorrect="false" autocapitalizationType="none"></TextField>
      <TextField hint="Password" secure="true" [(ngModel)]="user.password"></TextField>

      <Button text="Sign in" (tap)="signIn()"></Button>
      <Button text="Sign up for Groceries" class="link"></Button>
    </StackLayout>
  `
})
export class AppComponent {
  user: User;
  constructor() {
    this.user = new User();
  }
  signIn() {
    alert("You’re using: " + this.user.email);
  }
}
```

<div class="exercise-end"></div>

With this setup you now have a `User` class that you can share across pages in your app and even across applications. But a model object that’s four simple lines of code isn’t all that exciting. Where this approach really pays off is when you’re able to share your business logic, and the code that hits your backend systems. In Angular 2 those classes are known as services. Let’s look at them next.

### Services

A login screen isn’t all that useful if it doesn’t actually log users into anything. Therefore, our next task is to take the user’s email address and password, and send them to a backend endpoint to retrieve an authentication token we’ll use later in this guide. We’ll build this functionality as an Angular 2 service.

For the purposes of this tutorial we prebuilt a handful of backend endpoints using [Telerik Backend Services](http://www.telerik.com/platform/backend-services), and we’ll be using those endpoints to make this app functional. Let’s see how they work.

> **NOTE**: You don't have to use Telerik Backend Services to hit your app’s backend; you can use any HTTP API in a NativeScript app. Telerik Backend Services is convenient for us to use for this tutorial because it lets us spin up HTTP endpoints quickly.

<h4 class="exercise-start">
    <b>Exercise</b>: Add an Angular 2 service
</h4>

Open `app/shared/user/user.service.ts` and paste in the following code:

``` JavaScript

```

<div class="exercise-end"></div>

### Routing
