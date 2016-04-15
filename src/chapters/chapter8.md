## Appendix

### Known issues

As NativeScript’s Angular integration is still in beta, there are a few outstanding issues you may hit while completing this guide. Here are the major ones:

* **1**) You may get a “Service exited due to signal: Killed: 9” error when deploying an app to an iOS simulator. If you hit this issue, [refer to the GitHub issue](https://github.com/NativeScript/nativescript-angular/issues/101) for details on the problem and workarounds.
* **2**) The `tns livesync` command currently does not work on iOS apps. Because of this, for now, you must run `tns run ios --emulator` after each change you make to your app in order to see the changes rendered. [Follow this problem’s GitHub issue](https://github.com/NativeScript/nativescript-angular/issues/167) for updates.

You can refer to the [nativescript-angular](https://github.com/NativeScript/nativescript-angular/issues) repo on GitHub for a [full list of issues](https://github.com/NativeScript/nativescript-angular/issues), and to [report any problems you have](https://github.com/NativeScript/nativescript-angular/issues/new).