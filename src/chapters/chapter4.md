## NativeScript modules

In this chapter you'll learn about NativeScript modules, which are the JavaScript modules in your app's `node_modules/tns-core-modules` folder. Whether you've realized it or not, you've already used several NativeScript modules, as all of the NativeScript UI elements are actually implemented with JavaScript code.

If you dig into `node_modules/tns-core-modules` you can get an idea of how these modules work. Start by finding the `node_modules/tns-core-modules/camera` folder, which includes the implementation of the camera module. It includes:

- a `package.json` file that sets the name of the module;
- a file containing the module's Android implementation (`camera.android.js`);
- a file containing the module's iOS implementation (`camera.ios.js`);
- a file containing code shared by the Android and iOS implementations (`camera-common.js`)

> **NOTE**: You can refer to the [Node.js documentation on folders as modules](https://nodejs.org/api/modules.html#modules_folders_as_modules) for more detailed information on how NativeScript organizes its modules.

The `*.ios.*` and `*.android.*` naming convention should look familiar, as it’s the exact same convention we used to include Android- and iOS-specific styling in [chapter 2.3](#css). NativeScript uses this same convention to implement its modules on iOS and Android. Now that you know where these modules are, let's take a closer look at what else they can do for your app, starting with a closer looks at what you can do with NativeScript’s UI elements.

### UI elements

So far, you’ve only used NativeScript UI elements by including them in an Angular 2 component’s `template`, but you can also programmaticly create and access UI elements, and each UI element has a set of properties and methods you can use to customize your app. To see how this works lets access the `<Page>` UI element and make some changes to it.

<h4 class="exercise-start">
    <b>Exercise</b>: Customize the Page
</h4>

Open `app/pages/login/login.component.ts`, and add the following two imports to the top of the file:

``` TypeScript
import {topmost} from "ui/frame";
import {Page} from "ui/page";
```

Next, alter the same file’s existing `"angular2/core"` import to include the `OnInit` interface:

```TypeScript
import {Component, OnInit} from "angular2/core";
```

`OnInit` is a [TypeScript class interface](http://www.typescriptlang.org/docs/handbook/interfaces.html#class-types). To see how it works, make the following change to the declaration of your `LoginPage` class:

``` TypeScript
export class LoginPage implements OnInit {
```

If you’re using an editor that supports TypeScript, you should see an error that says something like *“Class ‘LoginPage’ incorrectly implements interface ‘OnInit’”*. When you implement a TypeScript class interface, you’re telling the TypeScript compiler that you will implement any of the methods that the interface requires. In the case of `OnInit`, Angular 2 requires you to implement a single `ngOnInit()` method. To implement it, add the following code within the `LoginPage` class:

``` TypeScript
ngOnInit() {
  this.page = <Page>topmost().currentPage;
  this.page.actionBarHidden = true;
  this.page.backgroundImage = this.page.ios ? "res://bg_login.jpg" : "res://bg_login";
}
```

> **NOTE**: The `this.page.ios` in this function can go away after NativeScript 2.0 is released. The check is there because of [this bug](https://github.com/NativeScript/NativeScript/issues/1788).

`ngOnInit` is one of several [component lifecycle hooks](https://angular.io/docs/ts/latest/guide/lifecycle-hooks.html) that Angular 2 provides. As its name implies, `ngOnInit` gets invoked when Angular initializes this component.

We’ll discuss what the code within `ngOnInit()` does momentarily, but finally, to make these changes compile and run, add the following property to the `LoginPage` class. (You can put it right under the `user: User;` line.)

``` TypeScript
page: Page;
```

<div class="exercise-end"></div>

Now that you have this code in place, let’s discuss what happens in these three lines:

``` TypeScript
this.page = <Page>topmost().currentPage;
this.page.actionBarHidden = true;
this.page.backgroundImage = this.page.ios ? "res://bg_login.jpg" : "res://bg_login";
```

This code uses the `topmost()` function from the [NativeScript frame module](http://docs.nativescript.org/ApiReference/ui/frame/Frame), and the [`Page` class](http://docs.nativescript.org/ApiReference/ui/page/Page) from the [NativeScript page module](http://docs.nativescript.org/ApiReference/ui/page/README). The `topmost()` function gives you access to the app’s current page with its `currentPage` property, and you use that reference to set that page’s `actionBarHidden` and `backgroundImage` properties.

Although you can peruse the NativeScript API documentation for a full list of these properties and what they do, if you’re using a TypeScript-friendly IDE, you can get a full list of these properties at any point.

<img alt="TypeScript autocomplete" class="plain" src="images/chapter4/typescript.png" style="border: 1px solid black;">

If you run your app you should see that the action bar—the bar beneath the status bar that had previously displayed on Android—is now hidden, and the page uses a gorgeous new background image:

![Background image on Android](images/chapter4/android/1.png)
![Background image on iOS](images/chapter4/ios/1.png)

Let’s look at a few other NativeScript modules you can use to help improve the look of this app.

### Animations

The ability to run robust and performant animations is the one of the biggest reasons people choose to build native mobile apps, and NativeScript makes running these animations simple. The NativeScript animation modules provides a [series of JavaScript APIs]({{site.baseurl}}/ui/animation) that let you perform a wide variety of animations to elements on the screen, including the following:

- [Opacity]({{site.baseurl}}/ui/animation#opacity)
- [Background Color]({{site.baseurl}}/ui/animation#background-color)
- [Translations]({{site.baseurl}}/ui/animation#translate)
- [Scaling]({{site.baseurl}}/ui/animation#scale)
- [Rotating]({{site.baseurl}}/ui/animation#rotate)

Let’s add a simple animation so you can see how they work.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a color animation
</h4>

Open `app/pages/login/login.html` and add an `id` to the existing `<StackLayout>`:

``` XML
<StackLayout id="container">
```

Next, open `app/pages/login/login.component.ts` and add the following line at the top, which imports the [`Color` class](http://docs.nativescript.org/ApiReference/color/Color.html) from the NativeScript color module:

``` TypeScript
import {Color} from "color";
```

Finally, change the `LoginPage`’s `toggleDisplay()` function in the same file to use this code:

``` TypeScript
toggleDisplay() {
  this.isLoggingIn = !this.isLoggingIn;
  this.page.getViewById("container").animate({
    backgroundColor: this.isLoggingIn ? new Color("white") : new Color("#301217"),
    duration: 200
  });
}
```

<div class="exercise-end"></div>

All NativeScript UI elements inherit from a base [`View` class](http://docs.nativescript.org/ApiReference/ui/core/view/View.html), which contains a number of useful methods—including the `getViewById()` and `animate()` methods used in the previous example. The `getViewById()` method, as its name implies, allows you to get a reference to a child view by its `id` attribute.

One you have a reference to a UI element, you can call any of the methods that element inherits from `View`. In this case, you call the `<StackLayout id="container">` element’s `animate()` method to change its background color over a duration of `200`, or 2/10 of a second. The effect is a subtle color change that helps user differentiate between the “Sign In” and “Sign Up” functionality that your form provides.

![Color animation on Android](images/chapter4/android/2.gif)
![Color animation on iOS](images/chapter4/ios/2.gif)

> **NOTE**: You may notice that the text color is off with the brown background. Don’t worry about that for now; we’ll address that in chapter 6.

The animation module is a lot of fun to play with, and it’s easy to use too. All you need to do is get a reference to an element using `getViewById()`, and then call that element’s `animate()` method. You may want to take a few minutes to look through our [animation samples]({{site.baseurl}}/ui/animation#examples) and try a few of these animations for yourself in Groceries.

For now, let’s move on to another commonly used NativeScript UI element: the `<ListView>`.

### ListView

The ListView element lets you show a list of things on the screen, which is exactly what you need for showing a list of groceries. Before tying the grocery list to a backend API, let's start by seeing how to show a hardcoded list of items on the screen.

<h4 class="exercise-start">
    <b>Exercise</b>: Build a list view
</h4>

Open `app/pages/list/list.html` and replace its contents with the following code:

``` XML
<GridLayout>
  <ListView [items]="groceryList" id="grocery-list" class="small-spacing">
    <template #item="item">
      <Label [text]="item.name" class="medium-spacing"></Label>
    </template>
  </ListView>
</GridLayout>
```

We’ll talk about the new syntax in a moment, but first let’s define the class names used in the previous example. Open `app/app.css` and paste the following code at the bottom of the file, which defines a few utility class names you can use throughout your app:

``` CSS
.small-spacing {
  margin: 5;
}
.medium-spacing {
  margin: 10;
}
```

Next, open `app/pages/list/list.component.ts` and replace its contents with the code below:

``` TypeScript
import {Component, OnInit} from "angular2/core";

@Component({
  selector: "list",
  templateUrl: "pages/list/list.html",
  styleUrls: ["pages/list/list-common.css", "pages/list/list.css"]
})
export class ListPage implements OnInit {
  groceryList: Array<Object> = [];

  ngOnInit() {
    this.groceryList.push({ name: "Apples" });
    this.groceryList.push({ name: "Bananas" });
    this.groceryList.push({ name: "Oranges" });
  }
}
```

<div class="exercise-end"></div>

Your `ListPage` class now has a single `groceryList` property, that you fill with three objects in an `ngOnInit` handler. If you run your app and login, you should see the same list of groceries on the screen:

![List view on Android](images/chapter4/android/3.png)
![List view on iOS](images/chapter4/ios/3.png)

How does this work? Let’s return to this chunk of code:

``` XML
<ListView [items]="groceryList" id="grocery-list" class="small-spacing">
  <template #item="item">
    <Label [text]="item.name" class="medium-spacing"></Label>
  </template>
</ListView>
```

The [`<ListView>` UI element](http://docs.nativescript.org/ApiReference/ui/list-view/ListView) requires an `items` property that points at an array of data—in this case, the `groceryList` array you added to your `ListPage` class. The list view element requires a child `<template>` element that specifies how to render each item in the `items` array.

The `#item` syntax is Angular 2’s for syntax for [creating local template variables](https://angular.io/docs/ts/latest/guide/template-syntax.html#!#star-template). This gives you the ability to refer to each item in the array as `item` within the template. For this template, you render each item in the array with a single `<Label>` UI element, and because of the `[text]="item.name"` binding, those labels contain the text from the `name` property of each of the items in `groceryList` TypeScript array.

Now that you have a hardcoded list displaying, let’s see how to swap that out with live data.

<h4 class="exercise-start">
    <b>Exercise</b>: Populate the list view
</h4>

Open `app/shared/grocery/grocery.ts` and paste in the following code:

``` TypeScript
export class Grocery {
  constructor(public id: string, public name: string) {}
}
```

This creates a simple `Grocery` model object that you can use throughout your app. Next, let’s create a simple service that reads grocery lists from our backend.  Open `app/shared/grocery/grocery-list.service.ts` and paste in the following code:

``` TypeScript
import {Injectable} from "angular2/core";
import {Http, Headers} from "angular2/http";
import {Config} from "../config";
import {Grocery} from "./grocery";
import {Observable} from "rxjs/Rx";
import "rxjs/add/operator/map";

@Injectable()
export class GroceryListService {
  constructor(private _http: Http) {}

  load() {
    var headers = new Headers();
    headers.append("Authorization", "Bearer " + Config.token);

    return this._http.get(Config.apiUrl + "Groceries", {
      headers: headers
    })
    .map(res => res.json())
    .map(data => {
      var groceryList = [];
      data.Result.forEach((grocery) => {
        groceryList.push(new Grocery(grocery.Id, grocery.Name));
      });
      return groceryList;
    })
    .catch(this.handleErrors);
  }

  handleErrors(error: Response) {
    console.log(JSON.stringify(error.json()));
    return Observable.throw(error);
  }
}
```

The code here is very similar to the code you used in the `UserService` earlier in this guide. You use the `Http` service’s `get()` method to load JSON data, and RxJS’s `map()` function to format the data into an array of `Grocery` objects.

To use this service, open `app/pages/list/list.component.ts` and add the following two lines to the top of the file:

``` TypeScript
import {Grocery} from "../../shared/grocery/grocery";
import {GroceryListService} from "../../shared/grocery/grocery-list.service";
```

Next, change the existing `groceryList` declaration to use the newly imported `Grocery` class instead of a generic `Object`:

``` TypeScript
groceryList: Array<Grocery> = [];
```

Then, add the following `constructor` function within the `ListPage` class:

``` TypeScript
constructor(private _groceryListService: GroceryListService) {}
```

Next, because you’re injecting a service into your constructor you must also include it as a provider within your component decorator. To do so, replace the existing `@Component` decorator with the code below:

``` TypeScript
@Component({
  selector: "list",
  templateUrl: "pages/list/list.html",
  styleUrls: ["pages/list/list-common.css", "pages/list/list.css"],
  providers: [GroceryListService]
})
```

Finally, to kick off the call to `load()` when this page initializes, replace the existing `ngOnInit()` function with the code below:

``` TypeScript
ngOnInit() {
  this._groceryListService.load()
    .subscribe(loadedGroceries => {
      loadedGroceries.forEach((groceryObject) => {
        this.groceryList.unshift(groceryObject);
      });
    });
}
```

The full version of your `app/pages/list/list.component.ts` file should now look like this:

``` TypeScript
import {Component, OnInit} from "angular2/core";
import {Grocery} from "../../shared/grocery/grocery";
import {GroceryListService} from "../../shared/grocery/grocery-list.service";

@Component({
  selector: "list",
  templateUrl: "pages/list/list.html",
  styleUrls: ["pages/list/list-common.css", "pages/list/list.css"],
  providers: [GroceryListService]
})
export class ListPage implements OnInit {
  groceryList: Array<Grocery> = [];

  constructor(private _groceryListService: GroceryListService) {}

  ngOnInit() {
    this._groceryListService.load()
      .subscribe(loadedGroceries => {
        loadedGroceries.forEach((groceryObject) => {
          this.groceryList.unshift(groceryObject);
        });
      });
  }
}
```

<div class="exercise-end"></div>

Once you run this code you may expect to see a list of groceries, but instead you’ll see this error in your terminal:

```
JS: EXCEPTION: No provider for Http! (ListPage -> GroceryListService -> Http)
JS: STACKTRACE:
JS: Error: DI Exception
JS:     at NoProviderError.BaseException [as constructor] (/data/data/org.nativescript.groceries/files/app/tns_modules/angular2/src/facade/exceptions.js:16:23)
JS:     at NoProviderError.AbstractProviderError [as constructor] (/data/data/org.nativescript.groceries/files/app/tns_modules/angular2/src/core/di/exceptions.js:38:16)
```

The problem here is your new `GroceryListService` uses the `Http` service, but that `Http` service is never included in your component’s `providers` array. You could add `HTTP_PROVIDERS` to this array, as you did in `login.component.ts`, but it seems a little silly to add `HTTP_PROVIDERS` to every page that you build. And as it turns out, Angular 2 providers a simpler way of handling this, by allowing you to add common providers to parent components.

<h4 class="exercise-start">
    <b>Exercise</b>: Declaring providers
</h4>

Open `app/pages/login/login.component.ts` and _remove_ the following line from the top of the file:

<div class="no-copy-button"></div>

``` TypeScript
import {HTTP_PROVIDERS} from "angular2/http";
```

Next, in the same file, remove `HTTP_PROVIDERS` from the component decorator’s `providers` array. The array should now look this like:

``` TypeScript
providers: [UserService],
```

After that, open `app/app.component.ts` and _add_ the following import to the top of the file:

``` TypeScript
import {HTTP_PROVIDERS} from "angular2/http";
```

Finally, add `HTTP_PROVIDERS` to the `AppComponent` decorator’s `providers` array so that it looks like this:

``` TypeScript
providers: [HTTP_PROVIDERS, NS_ROUTER_PROVIDERS],
```

<div class="exercise-end"></div>

Generally, it’s only a good idea to declare providers in parent components if all of the component’s children actually use that provider. Although you _could_ declare all your providers in `AppComponent`, your `providers` would become unwieldy as your app grows, and difficult to refactor as your app changes.

If you load the list page with the account you created earlier you’ll see a blank page, as your account is newly created, and therefore your grocery list is empty. If you want to see some data to verify your changes worked, try logging in with the credentials “user@nativescript.org” and “password”. You should some data that looks something like this:

![Grocery data on Android](images/chapter4/android/4.png)
![Grocery data on iOS](images/chapter4/ios/4.png)

At this point you have a list of data associated with each account that you display in a list view control, but a grocery list isn’t very useful if you can’t add to the list. Let’s look at how to do that next.

### GridLayout

Introduce what a grid layout actually is. Should be able to copy from the existing guide liberally.

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open `app/pages/list/list.html` and paste in the following code:

``` XML
<GridLayout rows="auto, *">

  <GridLayout row="0" columns="*, auto" class="add-bar">
    <TextField id="grocery" hint="Enter a grocery item" col="0"></TextField>
    <Image src="res://add" col="1"></Image>
  </GridLayout>

  <ListView [items]="groceryList" id="grocery-list" row="1" class="small-spacing">
    <template #item="item">
      <Label [text]="item.name" class="medium-spacing"></Label>
    </template>
  </ListView>

</GridLayout>
```

<div class="exercise-end"></div>

Show an image of what this looks like first and then break down the syntax in detail.

Now let’s make the add button actually work.

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open `app/pages/list/list.html` and give the existing `<TextField>` a new `[(ngModel)]` attribute so that it looks like this:

``` XML
<TextField id="grocery" [(ngModel)]="grocery" hint="Enter a grocery item" col="0"></TextField>
```

Next, give image a new tap attribute binding, so that the full `<Image>` looks like this:

``` XML
<Image src="res://add" id="add-image" (tap)="add()" col="1"></Image>
```

Next, open `app/pages/list/list.component.ts` and add the following property to the `ListPage` class (right below `groceryList`):

``` TypeScript
grocery: string;
```

Next, add the following two inputs to the top of the `list.component.ts` file:

``` TypeScript
import {TextField} from "ui/text-field";
import {topmost} from "ui/frame";
```

Then, add the following `add()` function to the existing `ListPage` class:

``` TypeScript
add() {
  if (this.grocery.trim() === "") {
    alert("Enter a grocery item");
    return;
  }

  // Dismiss the keyboard
  var groceryTextField = <TextField>topmost().currentPage.getViewById("grocery");
  groceryTextField.dismissSoftInput();

  this._groceryListService.add(this.grocery)
    .subscribe(
      groceryObject => {
        this.groceryList.unshift(groceryObject);
        this.grocery = "";
      },
      () => {
        alert({
          message: "An error occurred while adding an item to your list.",
          okButtonText: "OK"
        });
        this.grocery = "";
      }
    )
}
```

Finally, open `app/shared/grocery/grocery-list.service.ts` and paste the following function into the `GroceryService` class:

``` TypeScript
add(name: string) {
  var headers = new Headers();
  headers.append("Authorization", "Bearer " + Config.token);
  headers.append("Content-Type", "application/json");

  return this._http.post(
    Config.apiUrl + "Groceries",
    JSON.stringify({ Name: name }),
    { headers: headers }
  )
  .map(res => res.json())
  .map(data => {
    return new Grocery(data.Result.Id, name);
  })
  .catch(this.handleErrors);
}
```

<div class="exercise-end"></div>

Remind people that the final code for the tutorial is up on GitHub.

Talk about the code you just wrote.

Show a gif of the page in action.

Let’s make the page look a little nicer.

### ActivityIndicator

Introduce the activity indicator.

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open up `app/pages/list/list.html` and paste the following line immediately before the final `</GridLayout>`:

``` XML
<ActivityIndicator [busy]="isLoading" row="1"></ActivityIndicator>
```

Next, open `app/pages/list/list.component.ts` and add the following property to the `ListPage` class (immediately under `grocery: string`):

``` TypeScript
isLoading = false;
```

Then, change the existing `ngOnInit()` function to use the code below:

``` TypeScript
ngOnInit() {
  this.isLoading = true;
  this._groceryListService.load()
    .subscribe(loadedGroceries => {
      loadedGroceries.forEach((groceryObject) => {
        this.groceryList.unshift(groceryObject);
      });
      this.isLoading = false;
    });
}
```

<div class="exercise-end"></div>

Talk about how that works, then introduce how an animation can add a little polish.

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open `app/pages/list/list-common.css` and paste the following CSS at the top of the file:

``` CSS
ListView {
  opacity: 0;
}
```

Next, open `app/pages/list/list-component.ts` and add replace the existing `ngOnInit()` function with the following code:

``` TypeScript
ngOnInit() {
  this.isLoading = true;
  this._groceryListService.load()
    .subscribe(loadedGroceries => {
      loadedGroceries.forEach((groceryObject) => {
        this.groceryList.unshift(groceryObject);
      });
      this.isLoading = false;
      var groceryList = topmost().currentPage.getViewById("grocery-list");
      groceryList.animate({
        opacity: 1,
        duration: 1000
      });
    });
}
```

<div class="exercise-end"></div>

Talk about what happened. Include a gif. Transition to talking about npm modules and {N} plugins.
