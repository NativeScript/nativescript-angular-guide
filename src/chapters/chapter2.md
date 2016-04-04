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
    ├── hooks
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
    ├── references.d.ts
    └── tsconfig.json

```

Here's what these various files and folders do:

- **app**: This folder contains all the development resources you need to build your app. You'll be spending most of your time editing the files in here.
- **hooks**: This folder contains a series of files the NativeScript CLI uses to preprocess TypeScript code into JavaScript code. You can ignore this folder for now, but after you complete this tutorial you may want to experiment with other preprocessors that NativeScript provides out of the box, such as [Babel for JavaScript](http://docs.nativescript.org/core-concepts/transpilers#installing-babel) or [SASS for CSS](http://www.nativescriptsnacks.com/more/2016/03/14/less-sass.html).
- **node_modules**: This folder contains your app's npm module dependencies, including Angular 2, TypeScript, and the other modules NativeScript needs to build your app.
- **node_modules/angular2**: This folder contains the Angular 2 source code. NativeScript does not alter the core Angular 2 source code in any way, instead, NativeScript builds on top of Angular 2 with the nativescript-angular npm module.
- **node_modules/nativescript-angular**: This folder contains the module that integrates NativeScript-specific functionality into Angular 2. The source code for this module lives at <https://github.com/NativeScript/nativescript-angular>.
- **node_modules/tns-core-modules**: This folder contains your app's NativeScript modules, which are a series of NativeScript-provided JavaScript modules you'll use to build your app. Each module contains the platform-specific code needed to implement some feature—the camera, http calls, the file system, and so forth—exposed through a platform-agnostic API (e.g. `camera.takePicture()`). We'll look at some examples in [chapter 4](#chapter4). The source code for these modules lives at <https://github.com/NativeScript/nativescript>.
- **platforms**: This folder contains the platform-specific code NativeScript needs to build native iOS and Android apps. For example in the `android` folder you'll find things like your project's `AndroidManifest.xml` and .apk executable files. Similarly, the `ios` folder contains the Groceries' Xcode project and .ipa executables. Note, users on Windows and Linux machines will not have an `ios` folder.
- **package.json**: This file contains your app's configuration details, such as your app id, the version of NativeScript you're using, and also which npm modules your app uses. We'll take a closer look at how to use this file when we talk about using npm modules in [chapter 5](#plugins-and-npm-modules).
- **references.d.ts**: This file lists all [TypeScript declaration files](http://www.typescriptlang.org/docs/handbook/writing-definition-files.html) that your app uses. We’ll dig into what declaration files are, and how to use them in chapter 4 when we discuss NativeScript modules.
- **tsconfig.json**: This file contains your app’s TypeScript configuration. Unless you have existing TypeScript expertise, you’ll probably want to leave this file alone for now. If you do have existing experience you may want to tweak these values to suit your personal preferences. However, note that the `"experimentalDecorators"` and `"emitDecoratorMetadata"` flags are essential to making NativeScript and Angular 2 work, so don’t remove those. You can refer to the official TypeScript wiki for [detailed documentation on what you can do in a `tsconfig.json` file](https://github.com/Microsoft/TypeScript/wiki/tsconfig.json).

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
    │   │   ├── login
    │   │   │   ├── login.html
    │   │   │   └── ...
    │   │   └── ...
    │   ├── shared
    │   │   └── ...
    │   ├── utils
    │   │   └── ...
    │   ├── app.css
    │   ├── app.component.ts
    │   ├── main.ts
    │   └── ...
    └── ...
```
Here's what these various files and folders do:

- **App_Resources**: This folder contains platform-specific resources such as icons, splash screens, and configuration files. The NativeScript CLI takes care of injecting these resources into the appropriate places in the `platforms` folder when you execute `tns run`.
- **pages**: This folder, specific to the Groceries app, contains the code to build your app's pages. Each page is made up of a TypeScript file, an optional HTML file, and an optional set of CSS files. The Groceries app starts with two folders for its two pages, a login page, and a list page
- **shared**: This folder, also specific to the Groceries app, contains any files you need to share between NativeScript apps and Angular-2-built web apps. For Groceries this includes a few classes for talking to backend services, some model objects, and a `config.ts` file used to share configuration variables like API keys. We’ll discuss the `shared` folder, as well as code sharing between native apps and web apps, in detail in section 3.2.
- **app.css**: This file contains global styles for your app. We'll dig into app styling in [section 2.3](#css).
- **app.component.ts**: This primary Angular component that drives your application. Eventually this file will handle routing and application-wide configuration, however for now the file has a simple hello world example that we’ll look at momentarily.
- **main.ts**: The starting point of Angular 2 applications—web and native.

To get a sense of a NativeScript app actually starts up, let’s explore the first few files.

### Starting up

The first few files you run in a NativeScript app look almost identical to [the first few files you run in an Angular 2 web app](https://angular.io/docs/ts/latest/quickstart.html). Let’s start with `main.ts` as that’s the first file executed. Open your `app.main.ts` file; you should see the code below:

``` JavaScript
import {nativeScriptBootstrap} from "nativescript-angular/application";
import {AppComponent} from "./app.component";

nativeScriptBootstrap(AppComponent);
```

Here you’re using the TypeScript `import` command to bring in a function—`nativescriptBootstrap()`—and a [TypeScript class](http://www.typescriptlang.org/Handbook#classes)—`AppComponent`—each of which are defined in separate files. The `nativescriptBootstrap()` function comes from the “nativescript-angular” npm module, which you may recall contains the code needed to integrate NativeScript and Angular 2. Whereas [Angular 2’s own `bootstrap()` function](https://angular.io/docs/ts/latest/api/platform/browser/bootstrap-function.html) starts an Angular 2 browser app, NativeScript’s bootstrap function starts an Angular 2 native app.

> **TIP**: If you’re curious about what `nativescriptBootstrap()` function actually has to do to startup native iOS and Android apps, remember that all this code is open source for you to explore at any time. The `nativescriptBootstrap()` function specifically is defined in an [`application.ts` file](https://github.com/NativeScript/nativescript-angular/blob/master/src/nativescript-angular/application.ts) in the [NativeScript/nativescript-angular repository](https://github.com/NativeScript/nativescript-angular) on GitHub.

The bootstrap function, regardless of whether it’s for the web or for native apps, needs to know which Angular component to start the application with. In this case, you’re passing control to a `AppComponent` component defined in `app.component.ts`.

> **TIP**: In NativeScript we follow Angular 2’s own convention of naming component files with a `.component.ts` suffix.

Next, open your app’s `app/app.component.ts` file; you should see the code below:

``` JavaScript
import {Component} from "angular2/core";

@Component({
  selector: "my-app",
  template: "<Label text='hello NativeScript'></Label>"
})
export class AppComponent {}
```

This file contains an Angular 2 component, which is the primary building block of Angular 2 applications, including NativeScript apps. Let’s break down what’s going on in this file.

First, you again use TypeScript’s `import` command to bring in externally defined functionality—in this case, the `Component` class from Angular 2 itself. In Angular 2 a component manages a view, or a piece of the user interface that the user sees. A component can be used to define an individual UI element, or an entire page, and eventually we’ll add a bunch of logic to these components and use them to build an entire app. But for now this component is simple for the purpose of demonstration.

Notice the interesting way that the `Component` class is used—with the syntax `@Component`. This is a [TypeScript decorator](https://github.com/Microsoft/TypeScript-Handbook/blob/master/pages/Decorators.md), which allows you to annotate a TypeScript class or method with additional information. For now, you can think of it as a way of adding some metadata configuration to the currently empty `AppComponent` class. Specifically, the `@Component` decorator’s `template` property tells NativeScript how to render this component on the screen. In fact, the `<Label text="hello NativeScript"></Label>` syntax is why you saw “hello NativeScript” when you ran this app earlier.

However, this syntax may look a bit odd if you come from a web development background. On the web, the `<label>` HTML element doesn’t have a `text` attribute, so why do we see it here? Let’s dive into this by looking at how NativeScript UI elements work.

> **NOTE**: Curious about the `@Component` decorator’s `selector` property? The property defines how a component can be used within another component’s template. For instance a component that defines its `selector` with `selector: "foo-bar"` can be used by another component as `template: "<foo-bar></foo-bar>"`. NativeScript is smart enough to use your first Angular 2 component automatically; therefore, the `selector` property is currently irrelevant. We’ll use the `selector` property later in this guide though.

### Adding UI elements

The primary difference between building an Angular 2 app for the web and an Angular 2 app with NativeScript is in the UI elements that you use. NativeScript apps do not use a browser and do not have a DOM; therefore, elements like `<div>` and `<span>` simply do not work.

No worries though, as NativeScript provides an [extensive suite of UI elements](http://docs.nativescript.org/ui/ui-views), each of which are implemented with native iOS and Android controls. For instance, the [`<label>` control](http://docs.nativescript.org/ui/ui-views#label) our previous example used is actually rendered as a [`UILabel`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/) on iOS and an [`android.widget.TextView`](http://developer.android.com/reference/android/widget/TextView.html) on Android. The great thing about using NativeScript though, is that this native details are transparent to use as a developer. You type `<label>` and let NativeScript handle the rendering details.

Let’s return back to building Groceries. The first screen of Groceries is intended to be a login screen, so let’s replace the current `<Label>` with something that resembles a typical login screen in a mobile app.

<h4 class="exercise-start">
    <b>Exercise</b>: Add UI elements to <code>app.component.ts</code>
</h4>

Open `app/app.component.ts` and replace the existing `@Component` with the following code:

``` JavaScript
@Component({
  selector: "my-app",
  template: `
    <TextField hint="Email Address" id="email" keyboardType="email"
      autocorrect="false" autocapitalizationType="none"></TextField>
    <TextField hint="Password" id="password" secure="true"></TextField>

    <Button text="Sign in"></Button>
    <Button text="Sign up for Groceries"></Button>
  `
})
```

<div class="exercise-end"></div>

> **NOTE**: Notice the back-tick character (\`) used with the `template` property. This character is used to define an [ES2015 template literal](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals), which TypeScript supports, and which allows you to write multi-line strings without using messy string concatenation.

This code adds two new NativeScript UI elements: a [text field](http://docs.nativescript.org/ApiReference/ui/text-field/how-to.html) and a [button](http://docs.nativescript.org/ApiReference/ui/button/how-to.html). Much like HTML elements, NativeScript UI elements provide attributes to let you configure their behavior and appearance. The code you just added uses the following attributes:

- `<TextField>`
    - `hint`: Shows placeholder text that tells the user what to type.
    - `keyboardType`: The type of keyboard to present to the user for input. `keyboardType="email"` shows a keyboard optimized for entering email addresses. NativeScript currently supports [five types of keyboards](http://docs.nativescript.org/ui/keyboard.html) for text fields.
    - `autocorrect`: A boolean attribute that determines whether the mobile operating system should autocorrect user input. In the case of email address text fields, the autocorrect behavior is undesirable.
    - `autocapitalizationType`: Determines how the operating system should autocapitalize user input. `autocapitalizationType="none"` turns autocapitalization off altogether. NativeScript supports [four autocapitalization types](http://docs.nativescript.org/ApiReference/ui/enums/AutocapitalizationType/README.html) on text fields.
    - `secure`: A boolean attribute that determines whether the TextField's text should be masked, which is commonly done on password fields.
- `<Button>`
    - `text`: Controls the text displayed within the button.

After your app updates with this change, you may expect to see a polished login screen, but instead you will see a single `<Button>` element on the screen:

![login 1](images/chapter2/ios/1.png)
![login 1](images/chapter2/android/1.png)

What went wrong? In NativeScript whenever you use more than one UI element, you need to tell NativeScript how to arrange those elements on the screen. Since you’re not doing that currently, NativeScript is incorrectly assuming you want the last element—the `<Button>`—to take up the whole screen. To arrange these elements, let’s move onto the NativeScript feature for aligning elements on the screen: NativeScript layouts.

> **TIP**: The NativeScript docs include a [full list of the UI components and attributes](http://docs.nativescript.org/ui-with-xml) with which you can build your apps. You can even [build your own, custom UI components](http://docs.nativescript.org/ui-with-xml#custom-components).

### Layouts

NativeScript provides several different layout containers that allow you to place UI elements precisely where you want them to appear. 

- The [Absolute Layout](http://docs.nativescript.org/ApiReference/ui/layouts/absolute-layout/HOW-TO.html) lets you position elements using explicit x and y coordinates. This is useful when you need to place elements in exact locations, for example showing an activity indicator widget in the top-left corner of your app.
- The [Dock Layout](http://docs.nativescript.org/ApiReference/ui/layouts/dock-layout/HOW-TO.html) is useful for placing UI elements at the outer edges of your app. For example, a container docked at the bottom of the screen would be a good location for an ad.
- The [Grid Layout](http://docs.nativescript.org/ApiReference/ui/layouts/grid-layout/HOW-TO.html) lets you divide your interface into a series of rows and columns, much like a `<table>` in HTML markup.
- The [Stack Layout](http://docs.nativescript.org/ApiReference/ui/layouts/stack-layout/HOW-TO.html) lets you stack child UI elements either vertically or horizontally.
- The [Wrap Layout](http://docs.nativescript.org/ApiReference/ui/layouts/wrap-layout/HOW-TO.html) lets child UI elements flow from one row or column to the next when space is filled.

For your login screen, all you need is a simple `<StackLayout>` for stacking the UI elements on top of each other. In later sections, you'll use some of the more advanced layouts.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a stack layout to the login screen
</h4>

In `app.component.ts`, add a `<StackLayout>` element within your component’s `template` property. The full component should now look like this:

``` JavaScript
@Component({
  selector: "my-app",
  template: `
    <StackLayout>
      <TextField hint="Email Address" id="email" keyboardType="email"
        autocorrect="false" autocapitalizationType="none"></TextField>
      <TextField hint="Password" id="password" secure="true"></TextField>

      <Button text="Sign in"></Button>
      <Button text="Sign up for Groceries"></Button>
    </StackLayout>
  `
})
```

<div class="exercise-end"></div>

The stack layout is a UI element, and as such, it has attributes just like the `<TextField>` and `<Button>` elements you used in the previous section. After your app updates with this change, you'll see that your login page’s UI elements stack up:

![login 2](images/chapter2/ios/2.png)
![login 2](images/chapter2/android/2.png)

Although the UI elements are in the correct order, they could use some spacing and color to make the app look a bit nicer. To do that let's look at another NativeScript feature: CSS.

> **TIP**:
> * Refer to the NativeScript docs for a [more detailed look at how NativeScript layouts work](http://docs.nativescript.org/layouts) and the various things you can do to configure them.
> * Check out Jen Looper's article on [demystifying NativeScript layouts](https://www.nativescript.org/blog/demystifying-nativescript-layouts) for a more thorough look at NativeScript layouts in action.

### Global CSS

NativeScript uses a [subset of CSS](http://docs.nativescript.org/styling) to change the visual appearance of your app. You can use three mechanisms to add CSS properties to UI components: [application-wide CSS](http://docs.nativescript.org/styling#application-wide-css) (`app.css`), component-specific CSS, and an [inline `style` attribute](http://docs.nativescript.org/styling#inline-css). In this section we’ll cover application-wide, or global CSS, and in the next section we’ll look at how to apply CSS rules to individual components.

> **TIP**: Although inline styles are great for quick testing—e.g. `<StackLayout style="background-color: green;">`—you should avoid them in general because the `style` attributes tend to clutter up your templates, especially if you need to apply multiple rules.

<h4 class="exercise-start">
    <b>Exercise</b>: Create global styles
</h4>

Open your app’s `app/app.css` file and paste in the following code:

``` CSS
Page {
  background-color: white;
  font-size: 15;
}
TextField {
  padding: 10;
  font-size: 13;
}
```

<div class="exercise-end"></div>

If you've done any web development before, the syntax should feel familiar here. You select two UI components by their tag names (Page and TextField), and then apply a handful of CSS rules as name/value pairs. NativeScript does not support all CSS properties because it is not possible to replicate some of them in native apps without causing performance issues. A [full list of the CSS properties that are supported](http://docs.nativescript.org/styling#supported-properties) are listed in the NativeScript docs.

Although often you want CSS rules to apply equally to your iOS and Android app, occasionally it makes sense to apply a CSS rule to only one platform. For example, iOS text fields frequently have borders around them, but Android text fields do not. Let's look at how to make platform-specific style changes in NativeScript.

<h4 class="exercise-start">
    <b>Exercise</b>: Add platform-specific CSS
</h4>

Add the following as the first line of your app's `app/app.css` file:

``` CSS
@import url("~/platform.css");
```
> **WARNING**: NativeScript is consistent with browser implementations in that `@import` statements must precede all other CSS rules in a file.

Next, open your app’s `app/platform.ios.css` file and paste in the following code:

``` CSS
TextField {
  border-width: 1;
  border-color: black;
}
```

> **NOTE**: We’ll leave the `platform.android.css` file empty as we have no Android-specific changes to make yet.

<div class="exercise-end"></div>

NativeScript supports CSS's `@import` statement for importing one CSS file into another. So this new line of code imports the CSS rules from `platform.css` into `app.css`. But, you might have noticed that Groceries does not have a file named `platform.css`—only `app/platform.android.css` and `app/platform.ios.css` exist. What's going on here?

<a id="platform-specific-files"></a>When you execute `tns run`, or `tns livesync`, the NativeScript CLI takes your code from the `app` folder and places it in the native projects located in the `platforms/ios` and `platforms/android` folders. Here the naming convention comes in: while moving files, the CLI intelligently selects `.android.*` and `.ios.*` files. To give a specific example, the CLI moves `platform.ios.css` into `platforms/ios` and renames it to `platform.css`; similarly, the CLI moves `platform.android.css` into `platforms/android`, and again renames it to `platform.css`. This convention provides a convenient way to branch your code to handle iOS and Android separately, and it's supported for any type of file in NativeScript—not just CSS files. You'll see a few more examples of this convention later in this guide.

With these changes in place, you'll notice that the app has a bit more spacing, and also that the text fields have borders on iOS but that Android:

![login 3](images/chapter2/ios/3.png)
![login 3](images/chapter2/android/3.png)

Despite our changes the app still looks pretty ugly, and that’s because we’re going to apply another batch of styles at the component level. Let’s look at how that works.

### Component-specific CSS

Much like on the web, sometimes in your NativeScript apps you want to write CSS rules that apply to your entire application, and sometimes you want to write CSS rules that apply to a specific portion of the interface. In the previous section you saw how to use NativeScript’s `app.css` file to write global rules, and in this section you’ll learn how to use a component’s `styleUrls` property to apply rules that are scoped to individual components.

<h4 class="exercise-start">
    <b>Exercise</b>: Add component-specific CSS
</h4>

Open your app’s `app/app.component.ts` file and add a `styleUrls` property such that that full `@Component` declaration now looks like this:

``` JavaScript
@Component({
  selector: "my-app",
  template: `
    <StackLayout>
      <TextField hint="Email Address" id="email" keyboardType="email"
        autocorrect="false" autocapitalizationType="none"></TextField>
      <TextField hint="Password" id="password" secure="true"></TextField>

      <Button text="Sign in"></Button>
      <Button text="Sign up for Groceries"></Button>
    </StackLayout>
  `,
  styleUrls: ["pages/login/login-common.css", "pages/login/login.css"]
})
```

Next, open your app’s `app/pages/login/login-common.css` file and paste in the following code:

``` CSS
StackLayout {
  margin-left: 30;
  margin-right: 30;
  padding-bottom: 15;
  background-color: white;
}
Image {
  margin-top: 5;
  margin-bottom: 20;
}
Button, TextField {
  margin-left: 16;
  margin-right: 16;
  margin-bottom: 10;
}
#submit-button {
  background-color: #CB1D00;
  color: white;
  margin-top: 20;
}
```

<div class="exercise-end"></div>

In Angular 2, `styleUrls` points at an array of stylesheets that should be used to style a component. In this case, you’re telling Angular to use two stylesheets, `login-common.css`, and `login.css`, which is actually implemented as `login.ios.css` and `login.android.css` using the same naming convention we introduced in the previous section.

Why three files? Much like you divided your global files into `app.css`, `platform.ios.css`, and `platform.android.css`, this structure gives you a similar ability to place common login styling in `login-common.css`, iOS-specific login styling `login.ios.css`, and Android-specific login styling in `login.android.css`.

The great thing about placing CSS rules at the component level is you can use concise CSS selectors such as `Button` and `TextField`, and not worry about those rules applying to all buttons and text fields in your application, as Angular 2 ensures those rules remain scoped to your component.

Before we see what your app looks like now, there’s one small change you need to make. Notice that the last selector used in `login-common.css` is `#submit-button`. Much like using CSS on the web, in NativeScript you can both `id` and `class` attributes to target specific user interface element, but at the moment there’s no UI element in your app with an `id` of `"submit-button"`. Let’s change that.

<h4 class="exercise-start">
    <b>Exercise</b>: Add an `id` attribute
</h4>

Open your app’s `app/app.component.ts` file, find `<Button text="Sign in"></Button>` in your component’s `template`, and replace it with the code below:

``` XML
<Button text="Sign in" id="submit-button"></Button>
```

<div class="exercise-end"></div>

With this last `id` change in place your app is starting to look a little nicer:

![login 4](images/chapter2/ios/4.png)
![login 4](images/chapter2/android/4.png)

As you can see, in NativeScript you have a lot of options for how you can apply CSS rules. You can apply rules globally either for both platforms in `app.css`, for iOS in `platform.ios.css`, or for Android in `platform.android.css`. And you can also apply rules at the component level, while maintaining the same flexibility to target different platforms if required.

To continue polishing the visuals of this login screen, let’s look at how we can add an image of this app’s logo.

### Images

In NativeScript you use the `<Image>` UI element and its `src` attribute to add images to your pages. The `src` attribute lets you specify your image in three ways. The first (and simplest) way is to point at the URL of an image:

``` XML
<Image src="https://www.nativescript.org/images/default-source/landingpages/logo.png"></Image>
```

The second way is to point at an image that lives within your app's `app` folder. For example if you have an image at `app/images/logo.png`, you can use it with:

``` XML
<Image src="~/images/logo.png"></Image>
```

The third way, and the one Groceries uses, is to use platform-specific image resources. Let's add an image to the login screen and then discuss exactly what's happening.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a logo
</h4>

In `app.component.ts`, add the `<Image>` below as the first child of the existing `<StackLayout>` tag:

``` XML
<Image src="res://logo_login" stretch="none" horizontalAlignment="center"></Image>
```

<div class="exercise-end"></div>

The `res://` syntax tells NativeScript to use a platform-specific resource, in this case an image. Platform-specific resources go in your app's `app/App_Resources` folder. If you look there you'll find a few different image files, several of which are named `logo_login.png`.

Although more complex than putting an image directly in the `app` folder, using platform-specific images gives you more control over image display on different device dimensions. For example iOS lets you provide three different image files for devices with different pixel densities. As such you'll find logos named `logo_login.png`, `logo_login@2x.png`, and `logo_login@3x.png` in your `App_Resources/iOS` folder. For Android you'll find similar image files in `App_Resources/Android/drawable-hdpi` (for "high" dpi, or high dots-per-inch), `App_Resources/Android/drawable-mdpi` (for medium-dpi), and `App_Resources/Android/drawable-ldpi` (for low-dpi).

Once these files are in place the NativeScript framework knows how to pick the correct file; all you have to do is reference the image using `res://` and its base file name—i.e. `res://logo_login`. Here's what your login screen should look like on iOS and Android:

![login 5](images/chapter2/ios/5.png)
![login 5](images/chapter2/android/5.png)

At this point your UI looks good, but the app still doesn't actually do anything. Let's look at how you can use JavaScript to add some functionality.

> **TIP**: The community-written [NativeScript Image Builder](http://nsimage.brosteins.com/) can help you generate images in the appropriate resolutions for iOS and Android.
