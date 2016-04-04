## Application Logic

In this chapter you’ll learn how to add JavaScript logic to your app, how to create services that talk to backend endpoints, and how to architect an app that uses multiple pages. There’s a lot to cover, so let’s start by discussing how to handle events and data binding.

### Events

Most user interfaces are driven by events. In NativeScript apps, those events are usually some user action, such as tapping, swiping, or rotating—and NativeScript abstracts the iOS- and Android-specific code for handling such events into a handful of easy-to-use APIs. Let’s start with the most common event you’ll use in a NativeScript app: `tap`.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a `tap` event handler
</h4>

Open `app/app.component.ts`, find the existing sign in button within your component’s `template` (`<Button text="Sign in"></Button>`), and replace it with the following code:

``` JavaScript
<Button text="Sign in" id="submit-button" (tap)="submit()"></Button>
```

Next, in the same file, replace the current `AppComponent` declaration with the one shown below:

``` JavaScript
export class AppComponent {
  submit() {
    console.log("hello");
  }
}
```

<div class="exercise-end"></div>

The `(eventName)="functionName()"` syntax is part of [Angular 2’s event binding system](https://angular.io/docs/ts/latest/guide/template-syntax.html#!#event-binding), which lets you bind an event that occurs on a UI element to a function in your component’s class. In this case, the `(tap)="submit()"` syntax tells Angular to run the `AppComponent` class’s `submit()` function whenever the user taps the sign in button.

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

In `app/app.component.ts` replace the current `AppComponent` declaration with the one shown below, which adds a new `email` property, and changes the `submit()` method to display its value:

``` TypeScript
export class AppComponent {
  email = "user@nativescript.org";
  submit() {
    alert("You’re using: " + this.email);
  }
}
```

Next, find the first `<TextField>` in your component’s `template` and replace it with the code below, which adds a new `text` attribute:

``` XML
<TextField hint="Email Address" id="email" keyboardType="email" [text]="email"
  autocorrect="false" autocapitalizationType="none"></TextField>
```

<div class="exercise-end"></div>

The key thing to note here is the `[text]="email"` attribute on the `<TextField>`. This is Angular 2’s syntax for attribute binding, and it allows you to bind the value of an attribute to a property in your TypeScript class. In this case, the `text` attribute of the `<TextField>`—which is roughly equivalent to a DOM `<input>`’s `value` attribute—is bound to the `AppComponent`’s `email` attribute. Therefore the app now has an email address prefilled when it loads:

![Android with prefilled email](images/chapter3/android/1.png)
![iOS with prefilled email](images/chapter3/ios/1.png)

> **NOTE**: It’s very easy to confuse Angular 2’s event binding syntax `(eventName)="functionName()"` with its attribute binding syntax `[attributeName]="propertyName"` 🤔. Don’t worry though; if you get them backwards, the problem is usually easy to debug, as the functionality you’re attempting to add just won’t work. These syntaxes are common enough that you’ll be using them a lot, and eventually you should be able to commit them to memory.

This attribute binding approach works really well when you need the data binding to be one way—that is, when you need TypeScript properties, and changes to those properties done in TypeScript code, to appear in the user interface. But in the case of user interface controls that accept user input, such as the text field in this example, usually you need data binding to work two way—that is, you additionally need changes the user makes to be reflected in your TypeScript code.

To show that the current example’s data binding is only one way, head back to your app, change the email text field’s value (type a few extra letters or something like that), and then tap the “Sign In” button. Because your `submit()` function alerts the current value of your component’s `email` property—`alert("You’re using: " + this.email)`—you might expect to see the updated value in the alert. Instead, however, you see the original value. Notice how the typed text and the alert value don’t match in the screenshot below.

![Android with email address that do not match](images/chapter3/android/2.png)

To fix this, you need to switch to Angular 2’s two-way data binding syntax.

<h4 class="exercise-start">
    <b>Exercise</b>: Two-way data binding with Angular 2
</h4>

In `app/app.component.ts`, find the find the first `<TextField>`, and replace it with the `<TextField>` below, which introduces a new `[(ngModel)]` attribute:

``` XML
<TextField hint="Email Address" id="email" keyboardType="email" [(ngModel)]="email"
  autocorrect="false" autocapitalizationType="none"></TextField>
```

<div class="exercise-end"></div>

At first glance the `[(ngModel)]` syntax looks more than a little odd, as it’s essentially a combination of the event and attribute binding syntax that you used in earlier examples. In the case of this example, `[(ngModel)]="email"` is shorthand for `[text]="email" (emailChange)="email=$event"`, which binds the email element’s `text` attribute to an `email` property and adds a `change` event handler that updates the `email` property’s value whenever the user makes a change.

Don’t worry too much about the details here while we’re still getting started. In your head you can think of `[(ngModel)]` as the way to implement two-way data binding when you need it on form controls. To show that it works, if you again modify your app’s email address and click the “Sign In” button, you’ll see the updated value in the alert as expected:

![Android with email addresses that do match](images/chapter3/android/3.png)

At this point, you have a basic login screen setup with two-way data binding—not bad for 20 some lines of code of TypeScript. (Think about how much code you’d have to write in Android Studio _and_ Xcode to accomplish the same task.) To this point though you’ve been placing all of your logic in a single TypeScript file, which doesn’t scale all that well for real-world applications.

Before we tie this app to a backend and make this login screen fully functional, let’s take a step back and setup a structure that can scale.

<!--commenting this out as I don't think it belongs here. We just said we're going to move code out of the single file, then we start talking about more stuff to add to the single file...confuzzled-->
<!--<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

app.component.ts — Replace the two buttons with this

``` XML
<Button [text]="isLoggingIn ? 'Sign in' : 'Sign up'" id="submit-button" (tap)="submit()"></Button>
<Button [text]="isLoggingIn ? 'Sign up' : 'Back to login'" (tap)="toggleDisplay()"></Button>
```

app.component.ts - Use this for the AppComponent class:

``` TypeScript
export class AppComponent {
  email = "user@nativescript.org";
  isLoggingIn = true;

  submit() {
    alert("You’re using: " + this.email);
  }
  toggleDisplay() {
    this.isLoggingIn = !this.isLoggingIn;
  }
}
```

<div class="exercise-end"></div>

TODO: Transition
-->



### Structuring your app

There are many reasons to segment any application into modular units, and you can [read about the various benefits on Wikipedia](https://en.wikipedia.org/wiki/Modular_programming). Modularizing NativeScript apps, in addition, has one unique benefit: the ability to share the code you write between Angular-2-built web apps, and Angular-2-built native apps.

Even if you have no plans to create an Angular 2 web app, separating out your code is still advantageous for a number of other reasons—testability, ease of maintenance, and so forth—but if you _do_ have plans to build an Angular 2 web app, having a chunk of functionality that you can reuse in your native and web apps can be an invaluable time saver.

To see how this works in action, let’s edit some files in the `/shared` folder and set them up to be imported.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a model object
</h4>

Let’s start by creating a simple model object to store user data. Open `app/user/user.ts` and paste in the following code:

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

Here you import the `User` class that you just defined. Note the parallel between the `export` command used in the previous example and the `import` command used here. The reason the `User` class is available to import is because it was explicitly exported. You’ll see other examples of `import` and `export` as you go through this guide.

Next, replace the existing `AppComponent` definition with the one below, which uses the `User` class you just imported.

<!-- Todo, explain the isLoggingIn piece - from the exercise above I think. The button XML needs to be edited -->
``` JavaScript
export class AppComponent {
  user: User;
  isLoggingIn = true;

  constructor() {
    this.user = new User();
  }
  submit() {
    alert("You’re using: " + this.user.email);
  }
  toggleDisplay() {
    this.isLoggingIn = !this.isLoggingIn;
  }
}
```

Instead of storing data on the `AppComponent` directly, you’re now using the `User` model object, which is reusable outside of this page and even outside of this application. You instantiate an instance of the `User` class in a new `constructor` function, which Angular 2 invokes when it bootstraps your application.

Your final step is to use this new model object in your template. To do that, replace the two existing `<TextField>`s with the code shown below, which updates the `[(ngModel)]` bindings to point at the new `User` object:

``` XML
<TextField hint="Email Address" id="email" keyboardType="email" [(ngModel)]="user.email"
  autocorrect="false" autocapitalizationType="none"></TextField>
<TextField hint="Password" id="password" secure="true" [(ngModel)]="user.password"></TextField>
```

TODO: Better explanation. Move the full template into the login.html file and use `templateUrl`. 

If you got lost during this section, here’s a copy-and-paste friendly version of the full `app.component.ts` you should have at this point:

``` JavaScript
import {Component} from "angular2/core";
import {User} from "./shared/user/user";

@Component({
  selector: "my-app",
  templateUrl: "pages/login/login.html",
  styleUrls: ["pages/login/login-common.css", "pages/login/login.css"]
})
export class AppComponent {
  user: User;
  isLoggingIn = true;

  constructor() {
    this.user = new User();
  }
  submit() {
    alert("You’re using: " + this.user.email);
  }
  toggleDisplay() {
    this.isLoggingIn = !this.isLoggingIn;
  }
}

```

<div class="exercise-end"></div>

With this setup you now have a `User` class that you can share across pages in your app and even across applications. But a model object that’s four simple lines of code isn’t all that exciting. Where this approach really pays off is when you’re able to share your business logic, and the code that hits your backend systems. In Angular 2 those classes are known as services. Let’s look at them next.

### Services

A login screen isn’t all that useful if it doesn’t actually log users into anything. Therefore, our next task is to take the user’s email address and password, and send them to a backend endpoint to retrieve an authentication token we’ll use later in this guide. We’ll build this functionality as an Angular 2 service.

For the purposes of this tutorial we prebuilt a handful of backend endpoints using [Telerik Backend Services](http://www.telerik.com/platform/backend-services), and we’ll be using those endpoints to make this app functional. Let’s see how they work.

> **NOTE**: You don't have to use Telerik Backend Services to power your app’s backend; you can use any HTTP API in a NativeScript app. Telerik Backend Services is convenient for us to use for this tutorial because it lets us spin up HTTP endpoints quickly.

<h4 class="exercise-start">
    <b>Exercise</b>: Add an Angular 2 service
</h4>

Open `app/shared/user/user.service.ts` and paste in the following code:

``` TypeScript
import {Injectable} from "angular2/core";
import {Http, Headers, Response} from "angular2/http";
import {User} from "./user";
import {Config} from "../config";
import {Observable} from "rxjs/Rx";
import "rxjs/add/operator/do";
import "rxjs/add/operator/map";

@Injectable()
export class UserService {
  constructor(private _http: Http) {}

  register(user: User) {
    var headers = new Headers();
    headers.append("Content-Type", "application/json");

    return this._http.post(
      Config.apiUrl + "Users",
      JSON.stringify({
        Username: user.email,
        Email: user.email,
        Password: user.password
      }),
      { headers: headers }
    )
    .catch(this.handleErrors);
  }

  handleErrors(error: Response) {
    console.log(JSON.stringify(error.json()));
    return Observable.throw(error);
  }
}
```

TODO: Explain that mess above, and probably break it into a lot of steps.

In `app.component.ts`, add the lines below to the top:

``` TypeScript
import {HTTP_PROVIDERS} from "angular2/http";
import {UserService} from "./shared/user/user.service";
```

Then change the constructor to this:

``` TypeScript
constructor(private _userService: UserService) {
  this.user = new User();
}
```

Add this line to the @Component:

``` TypeScript
providers: [UserService, HTTP_PROVIDERS],
```

Then change the submit function to this:

``` TypeScript
submit() {
  if (this.isLoggingIn) {
    this.login();
  } else {
    this.signUp();
  }
}
```

Then add these two functions:

``` TypeScript
login() {
  // TODO: Define
}
signUp() {
  this._userService.register(this.user)
    .subscribe(
      () => {
        alert("Your account was successfully created.");
        this.toggleDisplay();
      },
      () => alert("Unfortunately we were unable to create your account.")
    );
}
```

Create an account, then hardcode those credentials in your constructor to make testing easier:

``` TypeScript
constructor(private _userService: UserService) {
  this.user = new User();
  this.user.email = "user@nativescript.org";
  this.user.password = "password";
}
```

Full app.component.ts:

``` TypeScript
import {Component} from "angular2/core";
import {HTTP_PROVIDERS} from "angular2/http";
import {User} from "./shared/user/user";
import {UserService} from "./shared/user/user.service";

@Component({
  selector: "my-app",
  templateUrl: "pages/login/login.html",
  styleUrls: ["pages/login/login-common.css", "pages/login/login.css"],
  providers: [UserService, HTTP_PROVIDERS]
})
export class AppComponent {
  user: User;
  isLoggingIn = true;

  constructor(private _userService: UserService) {
    this.user = new User();
    this.user.email = "user@nativescript.org";
    this.user.password = "password";
  }
  submit() {
    if (this.isLoggingIn) {
      this.login();
    } else {
      this.signUp();
    }
  }
  login() {
    // TODO: Define
  }
  signUp() {
    this._userService.register(this.user)
      .subscribe(
        () => {
          alert("Your account was successfully created.");
          this.toggleDisplay();
        },
        () => alert("Unfortunately we were unable to create your account.")
      );
  }
  toggleDisplay() {
    this.isLoggingIn = !this.isLoggingIn;
  }
}
```

<div class="exercise-end"></div>

TODO: Show that user account creation now works and transition to routing.

### Routing

TODO: Intro

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Copy app/app.component.ts into app/pages/login/login.component.ts, change the name of the class from “AppComponent” to “LoginPage, and update the two paths below accordingly:

``` TypeScript
import {User} from "../../shared/user/user";
import {UserService} from "../../shared/user/user.service";
```

Open app/app.component.ts back up and paste in the following code:

``` TypeScript
import {Component} from "angular2/core";
import {HTTP_PROVIDERS} from "angular2/http";
import {RouteConfig} from "angular2/router";
import {NS_ROUTER_DIRECTIVES, NS_ROUTER_PROVIDERS} from "nativescript-angular/router";
import {LoginPage} from "./pages/login/login.component";

@Component({
  selector: "main",
  directives: [NS_ROUTER_DIRECTIVES],
  providers: [NS_ROUTER_PROVIDERS],
  template: "<page-router-outlet></page-router-outlet>"
})
@RouteConfig([
  { path: "/Login", component: LoginPage, as: "Login", useAsDefault: true },
])
export class AppComponent {}
```

Explain what’s going on here.

<div class="exercise-end"></div>

Let’s actually log the user in then, shall we?

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open pages/list/list.component.ts and paste in this:

``` TypeScript
import {Component} from "angular2/core";

@Component({
  selector: "list",
  templateUrl: "pages/list/list.html",
  styleUrls: ["pages/list/list-common.css", "pages/list/list.css"]
})
export class ListPage {}
```

Next, open pages/list/list.html and paste in this:

``` XML
<Label text="Hello world"></Label>
```

Now, go back to app/app.component.ts and paste in this at the top of the file:

``` TypeScript
import {ListPage} from "./pages/list/list.component";
```

And this in the `@RouteConfig`:

``` TypeScript
{ path: "/List", component: ListPage, as: "List" }
```

Now go to app/shared/user/user.service.ts and add this function:

``` TypeScript
login(user: User) {
  var headers = new Headers();
  headers.append("Content-Type", "application/json");

  return this._http.post(
    Config.apiUrl + "oauth/token",
    JSON.stringify({
      username: user.email,
      password: user.password,
      grant_type: "password"
    }),
    { headers: headers }
  )
  .map(response => response.json())
  .do(data => {
    Config.token = data.Result.access_token;
  })
  .catch(this.handleErrors);
}
```

Finally, go to `app/login/login.component.ts` and change the login function to do this:

``` TypeScript
this._userService.login(this.user)
  .subscribe(
    () => this._router.navigate(["List"]),
    (error) => alert("Unfortunately we could not find your account.")
  );
```

To make this work, in the same file, add this to the top:

``` TypeScript
import {Router} from "angular2/router";
```

And this to the constructor:

``` TypeScript
constructor(private _router: Router, private _userService: UserService) {
```

<div class="exercise-end"></div>

You can login now! And navigate! Show a gif of the navigation. Talk about how in NativeScript you get native behavior automatically—aka a back button on iOS and a hardware back button on Android.

Then transition to modules.
