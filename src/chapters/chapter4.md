## NativeScript modules

In this chapter you'll learn about NativeScript modules, which are the JavaScript modules in your app's `node_modules/tns-core-modules` folder. Whether you've realized it or not, you've already used several NativeScript modules, as all of the NativeScript UI elements are actually implemented with JavaScript code.

If you dig into `node_modules/tns-core-modules` you can get an idea of how these modules work. Start by finding the `node_modules/tns-core-modules/camera` folder, which includes the implementation of the camera module. It includes:

- a `package.json` file that sets the name of the module;
- a file containing the module's Android implementation (`camera.android.js`);
- a file containing the module's iOS implementation (`camera.ios.js`);
- a file containing code shared by the Android and iOS implementations (`camera-common.js`)

> **NOTE**: You can refer to the [Node.js documentation on folders as modules](https://nodejs.org/api/modules.html#modules_folders_as_modules) for more detailed information on how NativeScript organizes its modules.

The \*.ios.\* and \*.android.\* naming convention should look familiar, as it’s the exact same convention we used to include Android- and iOS-specific styling in [chapter 2.3](#css). NativeScript uses this same convention to implement its modules on iOS and Android. Now that you know where these modules are, let's take a closer look at what else they can do for your app, starting with a closer looks at what you can do with NativeScript’s UI elements.

### UI elements

So far, you’ve only used NativeScript UI elements by including them in an Angular 2’s components `template`.

Explain that UI elements are actually NativeScript modules. Say that we’re going to make this app look nice—it’s a native app after all.

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

In app/pages/login/login.component.ts, first add these imports:

``` TypeScript
import {topmost} from "ui/frame";
import {Page} from "ui/page";
```

Then include `OnInit` in the existing `"angular2/core"` import:

```TypeScript
import {Component, OnInit} from "angular2/core";
```

Add the following as a new property in the `LoginPage` class:

``` TypeScript
page: Page;
```

Then add the following function to the `LoginPage` class:

``` TypeScript
ngOnInit() {
  this.page = <Page>topmost().currentPage;
  this.page.actionBarHidden = true;
  this.page.backgroundImage = this.page.ios ? "res://bg_login.jpg" : "res://bg_login";
}
```

> **NOTE**: The `this.page.ios` check can go away in 2.0. It’s there because of [this bug](https://github.com/NativeScript/NativeScript/issues/1788).

<div class="exercise-end"></div>

Explain what `OnInit` is. Explain how to look up {N} modules on the docs. Show images of what the app looks like now.

Transition to animations

### Animations

Talk about animations and the animation module.

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open `app/pages/login/login.html` and add an `id` to the `<StackLayout>`:

``` XML
<StackLayout id="container">
```

Open `app/pages/login/login.component.ts` and add the following line at the top:

``` TypeScript
import {Color} from "color";
```

Change the `toggleDisplay()` function in the same file to look like this:

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

Talk about the color module and animation module. Have gifs. Mention that the hint color looks bad, but that we’ll address that later. Transition to talking about the list page.

### ListView

Talk about what list views are.

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open `app/pages/list/list.html` and paste in the following code:

``` XML
<ListView [items]="groceryList" id="grocery-list" class="small-spacing">
  <template #item="item">
    <Label [text]="item.name" class="medium-spacing"></Label>
  </template>
</ListView>
```

We’ll talk about the syntax in a moment, for now concentrate on the new `class` attribute. Open `app/app.css` and paste the following code at the bottom of the file:

``` CSS
.small-spacing {
  margin: 5;
}
.medium-spacing {
  margin: 10;
}
```

Next, open `app/pages/list/list.component.ts` and paste in the following code:

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

Break down the new XML and TypeScript code individually.

This list is hardcoded which isn’t a whole lot of fun.

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open `app/shared/grocery/grocery.ts` and paste in the following code:

``` TypeScript
export class Grocery {
  constructor(public id: string, public name: string) {}
}
```

Open `app/shared/grocery/grocery-list.service.ts` and paste in the following code:

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

Open `app/pages/list/list.component.ts` and add the following two lines to the top of the file:

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

Because we’re injecting a service we must also add it as a provider within our component decorator:

``` TypeScript
@Component({
  selector: "list",
  templateUrl: "pages/list/list.html",
  styleUrls: ["pages/list/list-common.css", "pages/list/list.css"],
  providers: [GroceryListService]
})
```

Finally, replace the existing `ngOnInit()` function with the code below:

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

Talk about the code you just added. But then note that if you try the code, you’ll get an exception about no provider for Http. Talk about best practices of where to declare shared providers. Let’s fix the problem now.

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open `app/pages/login/login.component.ts` and _remove_ the following line from the top of the file:

``` TypeScript
import {HTTP_PROVIDERS} from "angular2/http";
```

Next, in the same file, remove `HTTP_PROVIDERS` from the component decorator’s `providers` array. The array should now look this like:

``` TypeScript
providers: [UserService]
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

Show an image of the list with backend-driven data. Talk about how the next step is letting users add to the list.

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
