## Accessing native APIs

The beauty of NativeScript is that you can write a native iOS or Android app in JavaScript, XML, and CSS without touching Swift, Objective-C, or Java, if you choose. But what if you want to present a different, more platform-specific UI to your users? Or if you want to access an iOS or Android API that NativeScript doesn't expose through a NativeScript module or plugin?

NativeScript gives you the option to dig into native code as needed, and to do so without leaving JavaScript. To show how this works in action, let's tweak a few of the UI elements in your app using native code.

### Accessing iOS APIs

Ask the reader to remember that the hint text color in the app doesn’t look good.

Show images.

Explain how NativeScript doesn’t currently expose a way to style a text field’s hint color (although [there is an open issue requesting the feature](https://github.com/NativeScript/NativeScript/issues/712)).

Show how you can Google something like “style UITextField hint text” and find iOS code that accomplishes that task.

<h4 class="exercise-start">
    <b>Exercise</b>: ???
</h4>

Open `app/utils/hint-util.ts` and paste in the following code:

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

Next, open up `app/pages/login/login.component.ts` and paste in the following imports at the top of the file:

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

Finally, add a call to the new `setTextFieldColors()` in `toggleDisplay()`, immediately after the existing `this.isLoggingIn = !this.isLoggingIn` line:

``` TypeScript
this.setTextFieldColors();
```

<div class="exercise-end"></div>

Talk about what just happened.

TODO: Insert gifs

Transition to Android API discussion.

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

Open `app.main.ts` and replace the contents of the file with the following code:

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
