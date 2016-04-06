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
  email = "nativescriptrocks@telerik.com";
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

> **NOTE**: It’s very easy to confuse Angular 2’s event binding syntax `(eventName)="functionName()"` with its attribute binding syntax `[attributeName]="propertyName"`. Don’t worry though; if you get them backwards, the problem is usually easy to debug, as the functionality you’re attempting to add just won’t work. These syntaxes are common enough that you’ll be using them a lot, and eventually you should be able to commit them to memory.

This attribute binding approach works really well when you need the data binding to be one way—that is, when you need TypeScript properties, and changes to those properties done in TypeScript code, to appear in the user interface. But in the case of user interface controls that accept user input, such as the text field in this example, usually you need data binding to work two way—that is, you additionally need changes the user makes to data via form controls to be reflected in your TypeScript code.

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

Before we move on, let’s make one additional change to show what else you can do with Angular 2’s data binding APIs. The Groceries app uses the same UI for the “Sign In” form and the “Sign Up” form. Therefore, when the user clicks “Sign Up”, we need to update the text of the buttons (and eventually the functionality that occurs when you tap them). Let’s see how to make that happen.


<h4 class="exercise-start">
    <b>Exercise</b>: Using the ternary operator in templates
</h4>

Open `app/app.component.ts` and replace the two existing buttons with the code below:

``` XML
<Button [text]="isLoggingIn ? 'Sign in' : 'Sign up'" id="submit-button" (tap)="submit()"></Button>
<Button [text]="isLoggingIn ? 'Sign up' : 'Back to login'" (tap)="toggleDisplay()"></Button>
```

Next, replace the existing `AppComponent` declaration with the code below, which adds a new `isLoggingIn` property, and a new `toggleDisplay()` method:

``` TypeScript
export class AppComponent {
  email = "nativescriptrocks@telerik.com";
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

The previous examples switches each button’s’ `text` attribute from a simple string—e.g. `<Button text="Sign Up">`—to an attribute binding that is dependent on a value defined in the `AppComponent` class—e.g. `<Button [text]="isLoggingIn ? 'Sign in' : 'Sign up'">"`. Now, when the value of the `isLoggingIn` attributes changes after the user clicks the bottom button, Angular is smart enough to update the text of the button automatically. The result looks like this:

![Text changing on Android](images/chapter3/android/4.gif)
![Text changing on iOS](images/chapter3/ios/4.gif)

At this point, you have a basic login screen setup with two-way data binding—which isn’t bad for 20 some lines of code of TypeScript. (Think about how much code you’d have to write in Android Studio _and_ Xcode to accomplish the same task.) To this point though you’ve been placing all of your logic in a single TypeScript file, which doesn’t scale all that well for real-world applications.

Before we tie this app to a backend and make this login screen fully functional, let’s take a step back and setup a structure that can scale.

### Structuring your app

There are many reasons to segment any application into modular units, and you can [read about the various benefits on Wikipedia](https://en.wikipedia.org/wiki/Modular_programming). Modularizing NativeScript apps, in addition, has one unique benefit: the ability to share the code you write between Angular-2-built web apps, and Angular-2-built native apps.

Even if you have no plans to create an Angular 2 web app, separating out your code is still advantageous for a number of other reasons—testability, ease of maintenance, and so forth—but if you _do_ have plans to build an Angular 2 web app, having a chunk of functionality that you can reuse in your native and web apps can be an invaluable time saver.

To see how this works in action, let’s edit some files in the `shared` folder and set them up to be imported.

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

Next, open `app/app.component.ts`, and add the following `import` to the top of the file:

``` JavaScript
import {User} from "./shared/user/user";
```

Here you import the `User` class that you just defined. Note the parallel between the `export` command used in the previous example and the `import` command used here. The reason the `User` class is available to import is because it was explicitly exported. You’ll see other examples of `import` and `export` as you go through this guide.

Next, replace the existing `AppComponent` definition with the one below, which uses the `User` class you just imported.

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

Your next step is to use this new model object in your template. To do that, replace the two existing `<TextField>`s with the code shown below, which updates the `[(ngModel)]` bindings to point at the new `User` object:

``` XML
<TextField hint="Email Address" id="email" keyboardType="email" [(ngModel)]="user.email"
  autocorrect="false" autocapitalizationType="none"></TextField>
<TextField hint="Password" id="password" secure="true" [(ngModel)]="user.password"></TextField>
```

As one final change, because this template is getting to be a bit complex, let’s move it into a separate file. Open `app/pages/login/login.html` and paste in the following code:

``` XML
<StackLayout>
  <Image src="res://logo_login" stretch="none" horizontalAlignment="center"></Image>

  <TextField hint="Email Address" id="email" keyboardType="email" [(ngModel)]="user.email"
    autocorrect="false" autocapitalizationType="none"></TextField>
  <TextField hint="Password" id="password" secure="true" [(ngModel)]="user.password"></TextField>

  <Button [text]="isLoggingIn ? 'Sign in' : 'Sign up'" id="submit-button" (tap)="submit()"></Button>
  <Button [text]="isLoggingIn ? 'Sign up' : 'Back to login'" (tap)="toggleDisplay()"></Button>
</StackLayout>
```

Finally, in `app/app.component.ts`, replace the existing `template` property with the new `templateUrl` property shown below:

``` TypeScript
templateUrl: "pages/login/login.html"
```

In case you got lost during this section, here’s a copy-and-paste friendly of the `app/app.component.ts` file you should have at this point:

``` TypeScript
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

> **TIP**: With Angular 2 components you have the ability to specify templates and CSS styling in two places—directly within the component, or in external files. For simple components feel free to choose either approach based on your personal preference, but once your templates/styles get to ~10 lines of code, consider using external files exclusively, as mixing non-trivial XML, CSS, and JavaScript code makes your component code less readable.

With this setup you now have a `User` class that you can share across pages in your app and even across applications. But a model object that’s four simple lines of code isn’t all that exciting. Where this approach really pays off is when you’re able to share your business logic, and the code that hits your backend systems. In Angular 2 those classes are known as services. Let’s look at them next.

### Services

A login screen isn’t all that useful if it doesn’t actually log users into anything. Therefore, our next task is to allow users to create and log into accounts. We’ll build this functionality as an [Angular 2 service](https://angular.io/docs/ts/latest/tutorial/toh-pt4.html), which is Angular 2’s mechanism for reusable classes that operate on data.

For the purposes of this tutorial we prebuilt a handful of backend endpoints using [Telerik Backend Services](http://www.telerik.com/platform/backend-services), and we’ll be using those endpoints to make this app functional. Let’s see how they work.

> **NOTE**: You don't have to use Telerik Backend Services to power your app’s backend; you can use any HTTP API in a NativeScript app. Telerik Backend Services is convenient for us to use for this tutorial because it lets us spin up HTTP endpoints quickly.

<h4 class="exercise-start">
    <b>Exercise</b>: Add an Angular 2 service
</h4>

There’s are several new concepts to introduce with Angular services, so we’re going to start by stubbing out a new `register()` method, and then come back to the implementation later in this section. With that in mind, open `app/shared/user/user.service.ts` and paste in the following code:

``` TypeScript
import {Injectable} from "angular2/core";
import {User} from "./user";

@Injectable()
export class UserService {
  register(user: User) {
    alert("About to register: " + user.email);
  }
}
```

This creates a basic Angular service with a single method that takes an instance of the `User` object you created in the previous section. The one new thing is the `@Injectable` decorator. This decorator denotes this class as a candidate for [Angular’s dependency injection mechanism](https://angular.io/docs/ts/latest/guide/dependency-injection.html). For now just think of adding the `@Injectable` as a required convention for all services that you write.

Next, add the following line to the top of `app/app.component.ts`, which imports the service you just defined:

``` TypeScript
import {UserService} from "./shared/user/user.service";
```

After that, add a new `providers` property to the existing `@Component` decorator. The full `@Component` declaration should now look like this:

``` TypeScript
@Component({
  selector: "my-app",
  providers: [UserService],
  templateUrl: "pages/login/login.html",
  styleUrls: ["pages/login/login-common.css", "pages/login/login.css"],
})
```

The `providers` array is a simple list of all the Angular 2 services that you need to use in your component. At the moment you only have one service, so your `providers` array only has one entry.

Next, replace `AppComponent`’s existing `constructor` with the code below:

``` TypeScript
constructor(private _userService: UserService) {
  this.user = new User();
}
```

We’ll discuss what this `constructor` syntax is doing momentarily, as it can be confusing if you’ve never worked with Angular 2 before, but first let’s make the final change to get this example running. Find the existing `submit()` function in `AppComponent`, and replace it with the following three functions:

``` TypeScript
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
  this._userService.register(this.user);
}
```

<div class="exercise-end"></div>

Now, in your app, tap the “Sign Up” button, type an email address, and tap the “Sign Up” button again. If all went well, you should see the alert below:

![Alert on Android](images/chapter3/android/5.png)
![Alert on iOS](images/chapter3/ios/5.png)

How does this code work? Let’s return to this bit of code:

``` TypeScript
constructor(private _userService: UserService) {
  this.user = new User();
}
```

This is Angular 2’s dependency injection implementation in action. Because you registered `UserService` as a provider in this component’s `providers` array, when Angular sees this syntax it creates an instance of the `UserService` class, and passes that instance into the component’s constructor.

This begs a bigger question though: why bother with all of this? Why not run `this._userService = new UserService()` in the component’s constructor and forget the complexity of `@Injectable` and `providers`?

The short answer is a dependency-injection-based approach to coding keeps your classes less coupled, and therefore more maintainable and testable as your application evolves over time. For a longer answer, head over to the Angular for a [more thorough discussion of the benefits of dependency injection](https://angular.io/docs/ts/latest/guide/dependency-injection.html).

Let’s return to our example and make the registration process actually work.

<h4 class="exercise-start">
    <b>Exercise</b>: Use an Angular 2 service
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

Next, in `app/app.component.ts`, replace the existing `signUp()` function with the following code:

``` TypeScript
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

<div class="exercise-end"></div>

The first thing to note here is the new `constructor` code in `user.service.ts`:

``` TypeScript
constructor(private _http: Http) {}
```

The `UserService` class is using the same dependency injection technique to bring in a service that it needs, in this case the Http class, which is Angular 2’s way of letting you perform HTTP calls. And thanks to NativeScript, those same HTTP APIs work on iOS and Android without any extra work.

> **TIP**: Refer to [Angular 2’s docs on Http](https://angular.io/docs/ts/latest/api/http/Http-class.html) for specifics on what HTTP APIs are available.

The other new bit of code is the return value of this new `register()` method. `register()` returns `this._http.post()`, which is an RxJS `Observable`. You can refer to docs for a [full tutorial on how RxJS observables work](https://angular.io/docs/ts/latest/guide/server-communication.html), but for now just know that the most common thing you’ll need to do with observables is subscribe to them, which is what the new code you added to `app.component.ts` does:

``` TypeScript
this._userService.register(this.user)
  .subscribe(
    () => {
      alert("Your account was successfully created.");
      this.toggleDisplay();
    },
    () => alert("Unfortunately we were unable to create your account.")
  );
```

> **NOTE**: The `() => {}` syntax defines an [ES2015 arrow function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions), which TypeScript supports out of the box. In this example, the arrow functions are the equivalent of writing `function() {}`.

The two functions you provide are success and failure handlers. If The call to `register()` succeeds, your first alert will fire, and if the call to `register()` fails, your second alert will fire. Now that your service code is setup and ready to go, let’s create make the final few changes and create an account.

<h4 class="exercise-start">
    <b>Exercise</b>: Create an account
</h4>

Because the `UserService` makes use of the Http service, your final step is registering the Http provider in `AppComponent`. Start by opening `app/app.component.ts` and adding the following import to the top of the file:

``` TypeScript
import {HTTP_PROVIDERS} from "angular2/http";
```

Next, in the same file, replace the current `providers` array with the following code:

``` TypeScript
providers: [UserService, HTTP_PROVIDERS],
```

At this point you should be ready to create an account to verify this whole setup worked.

After the provider changes have livesync’d, click the “Sign Up” button in your app, type in an email address and password (fake credentials are fine, just make up something you can remember), and then click the orange “Sign Up” button.

<div class="exercise-end"></div>

If all went well, you should see a confirmation dialog that looks like this:

![Alert on Android](images/chapter3/android/6.png)
![Alert on iOS](images/chapter3/ios/6.png)

> **TIP**:  After creating your account, you may wish to hardcore your credentials in your `AppComponent`’s `constructor` to make development faster for the rest of this guide.
> ``` TypeScript
constructor(private _userService: UserService) {
  this.user = new User();
  this.user.email = "user@nativescript.org";
  this.user.password = "password";
}
```

Your app now has a fully functional registration process, but users can’t do anything with the accounts they create. Our next step is to allow the users to login and navigate to a new list page. And to do that we need to introduce the concept of routing.

### Routing

Most of the Angular 2 concepts you’ve used in the last few sections are the same regardless of whether you’re building for the web or native apps. You are even able to share model objects and services directly between the two environments.

However, routing is different, as there are some fundamental differences between how the concept of routing works on the web and in apps. Routing on the web revolves around the concept of a URL, but in a native app there is no browser. Likewise, native apps have concepts that aren’t present in the browser, such as Android’s hardware back button, or iOS’s swipe left-to-right gesture to go back.

Because of that, routing in NativeScript needs to work a bit differently. The main difference is that you must designate certain Angular components in your app as “pages”, which in Groceries are stored in the `pages` folder. Let’s look at the code needed to make this work, and then step by and discuss why NativeScript takes the approach that it does.

<h4 class="exercise-start">
    <b>Exercise</b>: Setting up routing
</h4>

To this point you’ve been putting your login page code in `app.component.ts`. Let’s move that logic into the `pages/login` folder to make room for additional pages.

First, open `app/app.component.ts` and copy its contents into `app/pages/login/login.component.ts`.

Next, in `login.component.ts`, change the name of the class from “AppComponent” to “LoginPage, and update the two paths below accordingly:

``` TypeScript
import {User} from "../../shared/user/user";
import {UserService} from "../../shared/user/user.service";
```

Now that `app.component.ts` is empty, let’s add in the appropriate Angular 2 routing code. Open app/app.component.ts back up and paste in the following code:

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

<div class="exercise-end"></div>

The main new concept here is the `@RouteConfig` decorator, which you use to provide a list of all routes that are present in your app. Currently you only have one route, which is why `@RouteConfig` contains a single entry.

> **TIP**: If you haven’t played with Angular 2 routing before, you can refer to [Angular’s tutorial on the topic](https://angular.io/docs/ts/latest/tutorial/toh-pt5.html) for some background.

The other new concept in this example is `<page-router-outlet>`, which is your app’s first directive. You can check out Angular’s docs for [details on what directives do](https://angular.io/docs/ts/latest/api/core/Directive-decorator.html), but the simplest way to think of them is as something that can affect the markup you put in your `template`—in this case `<page-router-outlet>`.

Angular 2 provides a `<router-outlet>` directive for web apps, and NativeScript extends that directive with its own `<page-router-outlet>` directive that handles the unique environment of iOS and Android apps. To see how it works lets add another page.

<h4 class="exercise-start">
    <b>Exercise</b>: Create the list page
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

You can login now! And navigate! 

<img alt="Login with basic information" src="images/chapter3/login_router.gif">

Talk about how in NativeScript you get native behavior automatically—aka a back button on iOS and a hardware back button on Android.

Then transition to modules.
