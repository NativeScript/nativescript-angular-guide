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

### Customizing the status bar