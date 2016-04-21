# Building Apps with NativeScript and Angular 2

This repo contains a guide for getting started building apps with [NativeScript](https://www.nativescript.org/) and [Angular 2](https://angular.io/). You can view the guide live at <https://nativescript.github.io/nativescript-angular-guide/>.

If you’re interested in making changes to the guide, the chapters themselves are stored in this repo’s [src folder](https://github.com/nativescript/nativescript-angular-guide/tree/gh-pages/src/chapters). If you want to learn how to run the full guide on your local machine, check out the instructions below.

## Running locally

Start by running `npm install` to install this guide’s dependencies:

```
$ npm install
```

And then run `gulp` to launch the guide in your browser:

```
$ gulp
```

Gulp starts up a development server and launches the getting started guide in it. Gulp also watches for changes to files in the `src` folder (including the chapter Markdown files), and refreshes the site in your browser automatically.
