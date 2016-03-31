## NativeScript modules

Explain what modules are. Should be able to copy content almost verbatim from the current guide.

### UI elements

Explain that UI elements are actually NativeScript modules.

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

Explain what `OnInit` is. Explain how to look up {N} modules on the docs. Show images of what the app looks like now. Transition to talking about the list page.

### ListView

### GridLayout

### ActivityIndicator

### Animations
