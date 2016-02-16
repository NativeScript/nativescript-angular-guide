## Building Apps with NativeScript and Angular 2

<img src="images/banner.png" class="banner-image">

NativeScript’s first-class Angular support extends the Angular 2 runtime, making it possible for you to build _native_ iOS and Android applications using the same framework—and in some cases the same code—that you use to build Angular 2 web apps.

Let’s look at how it all works by building an app.

> **WARNING**: Although NativeScript itself is a production-ready framework that drives many apps in the app stores today, NativeScript’s Angular integration is still in an alpha stage. If you run into issues when using the Angular integration, please report them on https://github.com/NativeScript/nativescript-angular.

### What you're building

This guide will walk you through building [Groceries](https://github.com/NativeScript/sample-Groceries), a groceries management app that does the following things:

- Connects to an existing RESTful service.
- Provides user registration and login.
- Lets authenticated users add and delete groceries from a list.
- Runs cross-platform (iOS and Android).

If you follow along to the end, here's what the finished app will look like on iOS:

![login](/images/chapter0/ios/1.png)
![register](/images/chapter0/ios/2.png)
![list](/images/chapter0/ios/3.png)

And here's what the app will look like on Android:

![](/images/chapter0/android/1.png)
![](/images/chapter0/android/2.png)
![](/images/chapter0/android/3.png)

### Prerequisites

This guide assumes that you have some basic knowledge of JavaScript, CSS, and your development machine’s terminal. More specifically:

* **JavaScript**: You should know basic JavaScript concepts, such as how functions, if statements, and loops work.
* **CSS**: You should know how to write simple CSS selectors, and know how to apply CSS rules as name/value pairs.
* **The terminal**: You should know how to open a terminal or command-line prompt on your development machine, how to change directories, and how to execute commands.

This guide will _not_, however, assume you have any knowledge of Angular 2. When background Angular 2 expertise will help you understand a concept, this guide will link you to the appropriate places in the [angular.io](https://angular.io/docs/ts/latest/) documentation.

With that out of the way, let’s get started!
