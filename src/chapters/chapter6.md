## Accessing native APIs

The beauty of NativeScript is that you can write a native iOS or Android app in JavaScript, XML, and CSS without touching Swift, Objective-C, or Java, if you choose. But what if you want to present a different, more platform-specific UI to your users? Or if you want to access an iOS or Android API that NativeScript doesn't expose through a NativeScript module or plugin?

NativeScript gives you the option to dig into native code as needed, and to do so without leaving JavaScript. To show how this works in action, let's tweak a few of the UI elements in your app using native code.

### Accessing iOS APIs

You may recall from earlier chapters that the hint color on your sign up screen could use a little more contrast. Notice the unappealing black on brown color of the text in the images below (if you can see the text at all).

![Bad contrast on Android](images/chapter6/android/1.png)
![Bad contrast on iOS](images/chapter6/ios/1.png)

At the time of this writing, NativeScript doesn’t expose a way to style a text field’s hint color through CSS—although [there is an open issue requesting the feature](https://github.com/NativeScript/NativeScript/issues/712)—however, both iOS and Android have ways to accomplish this task, and with NativeScript you have direct access to these native APIs.

Let’s start with iOS. If you run a [generic search for “style iOS text field hint text”](https://www.google.com/#q=style%20ios%20text%20field%20hint%20text), the first result is a [Stack Overflow post](http://stackoverflow.com/questions/1340224/iphone-uitextfield-change-placeholder-text-color) that recommends setting a `UITextField`’s `attributedPlaceholder` property. Let’s look at how to do that.

<h4 class="exercise-start">
    <b>Exercise</b>: Change hint colors on iOS
</h4>

Because there are multiple text fields in Groceries, we’ll write the functionality to change hint colors as a utility that lives in your app’s `utils` folder. Open `app/utils/hint-util.ts` and paste in the following code:

``` TypeScript
import {Color} from "color";
import {TextField} from "ui/text-field";

declare var NSAttributedString: any;
declare var NSDictionary: any;
declare var NSForegroundColorAttributeName: any;

export function setHintColor(args: { view: TextField, color: Color }) {
  var dictionary = new NSDictionary(
    [args.color.ios],
    [NSForegroundColorAttributeName]
  );
  args.view.ios.attributedPlaceholder = NSAttributedString.alloc().initWithStringAttributes(
    args.view.hint, dictionary);
}
```

This creates a function called `setHintColor()` that accepts a `<TextField>` and `Color`. We’ll talk about the contents of this function momentarily; first let’s look at how to use it.

Open up `app/pages/login/login.component.ts` and add the following two imports to the top of the file:

``` TypeScript
import {setHintColor} from "../../utils/hint-util";
import {TextField} from "ui/text-field";
```

After that, add the following function to the file’s `LoginPage` class:

``` TypeScript
setTextFieldColors() {
  var email = <TextField>this.page.getViewById("email");
  var password = <TextField>this.page.getViewById("password");

  var mainTextColor = new Color(this.isLoggingIn ? "black" : "#C4AFB4");
  email.color = mainTextColor;
  password.color = mainTextColor;

  var hintColor = new Color(this.isLoggingIn ? "#ACA6A7" : "#C4AFB4");
  setHintColor({ view: email, color: hintColor });
  setHintColor({ view: password, color: hintColor });
}
```

Finally, add a call to the new `setTextFieldColors()` in your `LoginPage`’s existing `toggleDisplay()` method—ideally immediately after the existing `this.isLoggingIn = !this.isLoggingIn` line:

``` TypeScript
this.setTextFieldColors();
```

<div class="exercise-end"></div>

After your app refreshes with this change, you should now see a far more readable hint color:

![Better contrast on iOS](images/chapter6/ios/2.png)

Let’s back up to the contents of the `setHintColor()` function so we can discuss what’s going on here.

``` TypeScript
var dictionary = new NSDictionary(
  [args.color.ios],
  [NSForegroundColorAttributeName]
);
args.view.ios.attributedPlaceholder = NSAttributedString.alloc().initWithStringAttributes(
  args.view.hint, dictionary);
```

By convention, NativeScript controls make their iOS and Android native implementations available via `ios` and `android` properties, respectively. In this code that means that `args.color.ios` resolves to a [`UIColor`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIColor_Class/), and `args.view.ios` resolves to a [`UITextField`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextField_Class/). Once you have a reference to these controls you can set native properties on them directly in TypeScript, which this code does with the `UITextField`’s [`attributedPlaceholder` property](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextField_Class/#//apple_ref/occ/instp/UITextField/attributedPlaceholder).

The power with NativeScript is you can perform these customizations in TypeScript—there’s no need to jump into Xcode and write Objective-C or Swift. And this doesn’t apply just to attributes. Notice the global `NSDictionary`, `NSAttributedString`, and `NSForegroundColorAttributeName` attributes. In NativeScript, all iOS and Android APIs are globally available to use—again, directly in TypeScript code.

Admittedly, this code can seem a bit arcane if you’ve never written an iOS app before, but the key here is that you’re never limited by the APIs that NativeScript provides out of the box. Most of the time you’ll be able to solve problems using the NativeScript module APIs, but if you hit a scenario your app needs that NativeScript doesn’t provide a module for, you can always hit the native APIs directly.

> **TIP**:
> * NativeScript provides TypeScript declaration files (`.d.ts` files) for all iOS and Android APIs. You can download the files using the links below. One word of warning though: because the declaration files include the entirety of the iOS and Android SDKs, they’re quite large, and can slow TypeScript builds to a crawl because of their sheer size. Nevertheless, the files can be invaluable during development, as they make accessing native APIs a whole lot easier.
>     * [iOS TypeScript declaration file](https://raw.githubusercontent.com/NativeScript/NativeScript/master/ios.d.ts)
>     * [Android TypeScript declaration file](https://raw.githubusercontent.com/NativeScript/NativeScript/master/android17.d.ts)
> * For detailed information on how NativeScript makes native APIs globally available, read about [“How NativeScript Works”](http://developer.telerik.com/featured/nativescript-works/) on our blog, and [“Accessing Native APIs with JavaScript”](http://docs.nativescript.org/core-concepts/accessing-native-apis-with-javascript) on our documentation.

Let’s move onto how to accomplish this same hint color task on Android.

### Accessing Android APIs

Talk about Googling the same sort of thing for Android APIs (setting the hint color).

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open `app/utils/hint-util.ts` and replace the existing contents of the file with the following code:

``` TypeScript
import {Color} from "color";
import {TextField} from "ui/text-field";

declare var android: any;
declare var NSAttributedString: any;
declare var NSDictionary: any;
declare var NSForegroundColorAttributeName: any;

export function setHintColor(args: { view: TextField, color: Color }) {
  if (args.view.android) {
    args.view.android.setHintTextColor(args.color.android);
  }
  if (args.view.ios) {
    var dictionary = new NSDictionary(
      [args.color.ios],
      [NSForegroundColorAttributeName]
    );
    args.view.ios.attributedPlaceholder = NSAttributedString.alloc().initWithStringAttributes(
      args.view.hint, dictionary);
  }
}
```

<div class="exercise-end"></div>

Talk about what just happened.

TODO: Insert gifs

Transition to status bar discussion

### Customizing the status bar

Talk about status bars.

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open `app/main.ts` and replace the contents of the file with the following code:

``` TypeScript
import {nativeScriptBootstrap} from "nativescript-angular/application";
import {AppComponent} from "./app.component";
import {setStatusBarColors} from "./utils/status-bar-util";

setStatusBarColors();
nativeScriptBootstrap(AppComponent);
```

Next, open `app/platform.ios.css` and paste in the following code:

``` CSS
Page {
  margin-top: -20;
}
```

<div class="exercise-end"></div>

Talk about what just happened.

TODO: Insert images

And... that's it! You've created a functional, cross-platform, backend-driven app to manage your grocery list. In the process you've created a unique UI for Android and iOS, leveraged NativeScript plugins and npm modules, learned how to log in and register, managed backend services, created a list with add and delete functionality, and more. 

Congratulations! Feel free to [share your accomplishment on Twitter](https://twitter.com/intent/tweet?text=I%20just%20built%20an%20iOS%20and%20Android%20app%20using%20@NativeScript%20%F0%9F%8E%89.%20You%20can%20too!%20http://docs.nativescript.org/start/getting-started%20%23opensource) or [Facebook](https://www.facebook.com/sharer/sharer.php?u=http%3A%2F%2Fdocs.nativescript.org%2Fstart%2Fgetting-started&p%5B) to impress your friends 😀.

> **TIP**:
> * If you're curious about how NativeScript makes it possible to directly invoke iOS and Android APIs, you can read about [“How NativeScript Works”](http://developer.telerik.com/featured/nativescript-works/) on our blog.
> * Remember that the [Groceries app's “angular-end” branch](https://github.com/NativeScript/sample-Groceries/tree/angular-end) has the final state of this tutorial. Feel free to refer back to it at any time.
