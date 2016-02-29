## Building the UI

Before you start coding the Groceries app it's important to understand a NativeScript app's folder structure. It'll help you understand where to place new files, as well as a bit of what's going on with NativeScript under the hood.

Go ahead and open your app's `sample-Groceries` folder in your text editor of choice and let's dig in.

### Directory structure

To keep things simple, let's start by looking at the outer structure of the Groceries app:

```
.
└── sample-Groceries
    ├── app
    │   └── ...
    ├── node_modules
    │   ├── angular2
    │   ├── nativescript-angular
    │   ├── tns-core-modules
    │   └── ...
    ├── platforms
    │   ├── android
    │   └── ios
    ├── package.json
    └── tsconfig.json

```

Here's what these various files and folders do:

- **app**: This folder contains all the development resources you need to build your app. You'll be spending most of your time editing the files in here.
- **node_modules**: This folder contains your app's npm module dependencies, including Angular 2, TypeScript, and the other modules NativeScript needs to build your app.
- **node_modules/angular2**: This folder contains the Angular 2 source code. NativeScript does not alter the core Angular 2 source code in any way, instead, NativeScript builds on top of Angular 2 with the nativescript-angular npm module.
- **node_modules/nativescript-angular**: This folder contains the module that integrates NativeScript-specific functionality into Angular 2. The source code for this module lives at <https://github.com/NativeScript/nativescript-angular>.
- **node_modules/tns-core-modules**: This folder contains your app's NativeScript modules, which are a series of NativeScript-provided JavaScript modules you'll use to build your app. Each module contains the platform-specific code needed to implement some feature—the camera, http calls, the file system, and so forth—exposed through a platform-agnostic API (e.g. `camera.takePicture()`). We'll look at some examples in [chapter 4](#chapter4). The source code for these modules lives at <https://github.com/NativeScript/nativescript>.
- **platforms**: This folder contains the platform-specific code NativeScript needs to build native iOS and Android apps. For example in the `android` folder you'll find things like your project's `AndroidManifest.xml` and .apk executable files. Similarly, the `ios` folder contains the Groceries' Xcode project and .ipa executables. Note, users on Windows machines will not have an `ios` folder.
- **package.json**: This file contains your app's configuration details, such as your app id, the version of NativeScript you're using, and also which npm modules your app uses. We'll take a closer look at how to use this file when we talk about using npm modules in [chapter 5](#plugins-and-npm-modules).
- **tsconfig.json**: This file contains your app’s TypeScript configuration. Unless you have existing TypeScript expertise, you’ll probably want to leave this file alone for now. If you do have existing experience you may want to tweak these values to suit your personal preferences, however, note that the `"experimentalDecorators"` and `"emitDecoratorMetadata"` flags are essential to making NativeScript and Angular 2 work, so don’t remove those. You can refer to the official TypeScript wiki for [detailed documentation on what you can do in a `tsconfig.json` file](https://github.com/Microsoft/TypeScript/wiki/tsconfig.json).

The NativeScript CLI manages the `platforms` folder for you as you develop and run your app; therefore, it's a best practice to treat the `platforms` folder as generated code. The Groceries app includes the `platforms` folder in its [`.gitignore`](https://github.com/NativeScript/sample-Groceries/blob/master/.gitignore) to exclude its files from source control.

Next, let's dig into the `app` folder, as that's where you'll be spending the majority of your time.

```
.
└── sample-Groceries
    ├── app
    │   ├── App_Resources
    │   │   ├── Android
    │   │   └── iOS
    │   ├── pages
    │   │   ├── login.component.ts
    │   │   └── ...
    │   ├── shared
    │   │   └── ...
    │   ├── app.css
    │   ├── app.component.ts
    │   ├── main.ts
    │   └── ...
    └── ...
```
Here's what these various files and folders do:

- **App_Resources**: This folder contains platform-specific resources such as icons, splash screens, and configuration files. The NativeScript CLI takes care of injecting these resources into the appropriate places in the `platforms` folder when you execute `tns run`.
- **pages**: This folder, specific to the Groceries app, contains the code to build your app's pages. Each page is made up of a TypeScript file and an optional HTML file. The Groceries app starts with three TypeScript files for its three pages—a login page, a registration page, and a list page. In a more complex app you may wish to create a more detailed folder structure within `pages`, but for now, keeping all pages in the root simplifies our demo.
- **shared**: This folder, also specific to the Groceries app, contains any files you need to share across pages in your app. In the Groceries app, you'll find a few service classes for talking to backend services, a few model objects, and a `config.ts` file used to share configuration variables like API keys.
- **app.css**: This file contains global styles for your app. We'll dig into app styling in [section 2.3](#css).
- **app.component.ts**: This primary Angular component that drives your application. Eventually this file will handle routing and application-wide configuration, however for now the file has a simple hello world example that we’ll look at momentarily.
- **main.ts**: The starting point of Angular 2 applications—web and native.

To get a sense of a NativeScript app actually starts up, let’s dig in to the first few files.

### Starting up

The first few files you run in a NativeScript app look almost identical to [the first few files you run in an Angular 2 web app](https://angular.io/docs/ts/latest/quickstart.html). Let’s start with `main.ts` as that’s the first file executed. Open your `app.main.ts` file; you should see the code below:

``` JavaScript
import {nativeScriptBootstrap} from "nativescript-angular/application";
import {AppComponent} from "./app.component";

nativeScriptBootstrap(AppComponent);
```

Here you’re using the TypeScript `import` command to bring in a function—`nativescriptBootstrap()`—and a [TypeScript class](http://www.typescriptlang.org/Handbook#classes)—`AppComponent`—each of which are defined in separate files. The `nativescriptBootstrap()` function comes from the “nativescript-angular” npm module, which you may recall contains the code needed to integrate NativeScript and Angular 2. Whereas [Angular 2’s own `bootstrap()` function](https://angular.io/docs/ts/latest/api/platform/browser/bootstrap-function.html) starts an Angular 2 browser app, NativeScript’s bootstrap function starts an Angular 2 native app.

> **TIP**: If you’re curious about what `nativescriptBootstrap()` function actually has to do to startup native iOS and Android apps, remember that all this code is open source for you to explore at any time. The `nativescriptBootstrap()` function specifically is defined in an [`application.ts` file](https://github.com/NativeScript/nativescript-angular/blob/master/src/nativescript-angular/application.ts) in the [NativeScript/nativescript-angular repository](https://github.com/NativeScript/nativescript-angular) on GitHub.

The bootstrap function, regardless of whether it’s for the web or for native apps, needs to know which Angular component to start the application with. In this case, you’re passing to control to a `AppComponent` component defined in `app.component.ts`.

> **TIP**: In NativeScript we follow Angular 2’s own convention of naming component files with a `.component.ts` suffix.

Next, open your app’s `app/app.component.ts` file; you should see the code below:

``` JavaScript
// TODO: Figure out if this is still necessary. If so, explain why.
// import "reflect-metadata";
import {Component} from "angular2/core";

@Component({
  selector: "my-app",
  template: "<label text='hello NativeScript'></label>"
})
export class AppComponent {}
```

This file contains an Angular 2 component, which is the primary building block of Angular 2 applications, including NativeScript apps. Let’s break down what’s going on in this file.

First, you again use TypeScript’s `import` command to bring in externally defined functionality—in this case, the `Component` class from Angular 2 itself. In Angular 2 a component manages a view, or a piece of the user interface that the user sees. A component be used to define an individual UI element, or an entire page, and eventually we’ll add a bunch of logic to these components and use them to build an entire app. But for now this component is simple for the purpose of demonstration.

Notice the interesting way that the `Component` class is used—with the syntax `@Component`. This is a [TypeScript decorator](https://github.com/Microsoft/TypeScript-Handbook/blob/master/pages/Decorators.md), which allows you to annotate a TypeScript class or method with additional information. For now, you can think of it as a way of adding some metadata configuration to the currently empty `AppComponent` class. Specifically, the `@Component` decorator’s `template` property tells NativeScript how to render this component on the screen. In fact, the `<label text="hello NativeScript"></label>` syntax is why you saw “hello NativeScript” when you ran this app earlier.

However, this syntax may look a bit odd if you come from a web development background. On the web, the `<label>` HTML element doesn’t have a `text` attribute, so what’s going on here. Let’s dive into this by looking at how NativeScript UI elements work.

> **NOTE**: Curious about the `@Component` decorator’s `selector` property? The property defines how a component can be used within another component’s template. For instance a component that defines its `selector` with `selector: "foo-bar"` can be used by another component as `template: "<foo-bar></foo-bar>"`. NativeScript is smart enough to use your first Angular 2 component automatically; therefore, the `selector` property is currently irrelevant. We’ll use the `selector` property later in this guide though.

### Adding UI elements

The primary difference between building an Angular 2 app for the web and an Angular 2 app with NativeScript is in the UI elements that you use. NativeScript apps do not use a browser and do not have a DOM; therefore, elements like `<div>` and `<span>` simply do not work.

No worries though, as NativeScript provides an [extensive suite of UI elements](http://docs.nativescript.org/ui/ui-views), each of which are implemented with native iOS and Android controls. For instance, the [`<label>` control](http://docs.nativescript.org/ui/ui-views#label) our previous example used is actually rendered as a [`UILabel`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/) on iOS and an [`android.widget.TextView`](http://developer.android.com/reference/android/widget/TextView.html) on Android. The great thing about using NativeScript though, is that this native details are transparent to use as a developer. You type `<label>` and let NativeScript handle the rendering details.

Let’s return back to building Groceries. The first screen of Groceries is intended to be a login screen, so let’s replace the current `<label>` with something that resembles your typical login screen in a mobile app.

<h4 class="exercise-start">
    <b>Exercise</b>: Add UI elements to <code>app.component.ts</code>
</h4>

Open `app/app.component.ts` and replace the existing `@Component` with the following code:

``` JavaScript
@Component({
  selector: "my-app",
  template: `
    <text-field hint="Email Address" keyboardType="email"
      autocorrect="false" autocapitalizationType="none"></text-field>
    <text-field hint="Password" secure="true"></text-field>

    <button text="Sign in"></button>
    <button text="Sign up for Groceries"></button>
  `
})
```

<div class="exercise-end"></div>

> **NOTE**: Notice the back-tick character (\`) used with the `template` property. This character is used to define an [ES2015 template literal](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals), which TypeScript supports, and which allows you to write multi-line strings without using messy string concatenation.

This code adds two new NativeScript UI elements: a [text field](http://docs.nativescript.org/ApiReference/ui/text-field/how-to.html) and a [button](http://docs.nativescript.org/ApiReference/ui/button/how-to.html). Much like HTML elements, NativeScript UI elements provide attributes to let you configure their behavior and appearance. The code you just added uses the following attributes:

- `<text-field>`
    - `hint`: Shows placeholder text that tells the user what to type.
    - `keyboardType`: The type of keyboard to present to the user for input. `keyboardType="email"` shows a keyboard optimized for entering email addresses. NativeScript currently supports [five types of keyboards](http://docs.nativescript.org/ui/keyboard.html) for text fields.
    - `autocorrect`: A boolean attribute that determines whether the mobile operating system should autocorrect user input. In the case of email address text fields, the autocorrect behavior is undesirable.
    - `autocapitalizationType`: Determines how the operating system should autocapitalize user input. `autocapitalizationType="none"` turns autocapitalization off altogether. NativeScript supports [four autocapitalization types](http://docs.nativescript.org/ApiReference/ui/enums/AutocapitalizationType/README.html) on text fields.
    - `secure`: A boolean attribute that determines whether the TextField's text should be masked, which is commonly done on password fields.
- `<button>`
    - `text`: Controls the text displayed within the button.

After your app updates with this change, you may expect to see a polished login screen, but instead you will see a single `<button>` element on the screen:

![login 1](images/chapter2/ios/1.png)
![login 1](images/chapter2/android/1.png)

In NativeScript whenever you use more than one UI element, you need to tell NativeScript how to arrange those elements on the screen. Since you’re not doing that currently, NativeScript is incorrectly assuming you want the last element—the `<button>`—to take up the whole screen. To arrange these elements, let’s move onto the NativeScript feature for aligning elements on the screen: NativeScript layouts.

> **TIP**: The NativeScript docs include a [full list of the UI components and attributes](http://docs.nativescript.org/ui-with-xml) with which you can build your apps. You can even [build your own, custom UI components](http://docs.nativescript.org/ui-with-xml#custom-components).

### Layouts

NativeScript provides several different layout containers that allow you to place UI elements precisely where you want them to appear. 

- The [Absolute Layout](http://docs.nativescript.org/ApiReference/ui/layouts/absolute-layout/HOW-TO.html) lets you position elements using explicit x and y coordinates. This is useful when you need to place elements in exact locations, for example showing an activity indicator widget in the top-left corner of your app.
- The [Dock Layout](http://docs.nativescript.org/ApiReference/ui/layouts/dock-layout/HOW-TO.html) is useful for placing UI elements at the outer edges of your app. For example, a container docked at the bottom of the screen would be a good location for an ad.
- The [Grid Layout](http://docs.nativescript.org/ApiReference/ui/layouts/grid-layout/HOW-TO.html) lets you divide your interface into a series of rows and columns, much like a `<table>` in HTML markup.
- The [Stack Layout](http://docs.nativescript.org/ApiReference/ui/layouts/stack-layout/HOW-TO.html) lets you stack child UI elements either vertically or horizontally.
- The [Wrap Layout](http://docs.nativescript.org/ApiReference/ui/layouts/wrap-layout/HOW-TO.html) lets child UI elements flow from one row or column to the next when space is filled.

For your login screen, all you need is a simple `<stack-layout>` to stack the UI elements on top of each other. In later sections, you'll use some of the more advanced layouts.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a stack layout to the login screen
</h4>

In `app.component.ts`, add a `<stack-layout>` element within your component’s `template` property. The full component should now look like this:

``` JavaScript
@Component({
  selector: "my-app",
  template: `
    <stack-layout>
      <text-field hint="Email Address" keyboardType="email"
        autocorrect="false" autocapitalizationType="none"></text-field>
      <text-field hint="Password" secure="true"></text-field>

      <button text="Sign in"></button>
      <button text="Sign up for Groceries"></button>
    </stack-layout>
  `
})
```

<div class="exercise-end"></div>

The stack layout is a UI element, and as such, it has attributes just like the `<text-field>` and `<button>` elements you used in the previous section. Here, the `orientation="vertical"` attribute tells the stack layout to arrange its child elements vertically.

After your app updates with this change, you'll see that your login page’s UI elements stack up:

![login 2](http://docs.nativescript.org/img/cli-getting-started/chapter2/ios/2.png)
![login 2](http://docs.nativescript.org/img/cli-getting-started/chapter2/android/2.png)

Although the UI elements are in the correct order, they could use some spacing and color to make the app look a bit nicer. To do that let's look at another NativeScript feature: CSS.

> **TIP**:
> * Refer to the NativeScript docs for a [more detailed look at how NativeScript layouts work](http://docs.nativescript.org/layouts) and the various things you can do to configure them.
> * Check out Jen Looper's article on [demystifying NativeScript layouts](https://www.nativescript.org/blog/demystifying-nativescript-layouts) for a more thorough look at NativeScript layouts in action.

### CSS

TODO: Write this!

### Images

TODO: Write this!
