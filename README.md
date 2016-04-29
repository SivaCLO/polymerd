## Polymerized Electron

> Boilerplate to build rich cross-platform desktop apps with reusable web components using Electron & Polymer.

### Built from

* https://github.com/szwacz/electron-boilerplate - Comprehensive boilerplate application for [Electron runtime](http://electron.atom.io).  
* https://github.com/PolymerElements/polymer-starter-kit - A starting point for building web applications with [Polymer 1.0](https://www.polymer-project.org/1.0/).

### Included out of the box

* Basic structure of a desktop application built with web components
* Cross-platform development environment, which works the same way on OSX, Windows and Linux.
* Native Installers for all three operating systems.
* [Polymer](https://www.polymer-project.org/), [Paper](https://elements.polymer-project.org/browse?package=paper-elements), [Iron](https://elements.polymer-project.org/browse?package=iron-elements) and [Neon](https://elements.polymer-project.org/browse?package=neon-elements) elements
* [Material Design](http://www.google.com/design/spec/material-design/introduction.html) layout
* Routing with [Page.js](https://visionmedia.github.io/page.js/)
* Unit testing with [Web Component Tester](https://github.com/Polymer/web-component-tester)
* Optional offline setup through [Platinum](https://elements.polymer-project.org/browse?package=platinum-elements) Service Worker elements
* End-to-end Build Tooling (including [Vulcanize](https://github.com/Polymer/vulcanize))
* [Recipes](/docs/README.md/) for ES2015 support, Polymer performance, using Chrome Dev Editor, Deploying to GitHub Pages, Deploying to Firebase, Mobile Chrome Apps and lint tools.

### More Information

* Check out the Polymer Starter Kit tutorials on [polymer-project.org](https://www.polymer-project.org):

# Start Here
```
git clone https://github.com/sivragav/polymerized-electron.git
cd polymerized-electron
npm install
npm start
```

-----------------------------

> Original Documentation from szwacz/electron-boilerplate

## Structure of the project

There are **two** `package.json` files:  

#### 1. For development
Sits on path: `polymerized-electron/package.json`. Here you declare dependencies for your development environment and build scripts. **This file is not distributed with real application!**

Also here you declare the version of Electron runtime you want to use:
```json
"devDependencies": {
  "electron-prebuilt": "^0.34.0"
}
```

#### 2. For your application
Sits on path: `polymerized-electron/app/package.json`. This is **real** manifest of your application. Declare your app dependencies here.

#### OMG, but seriously why there are two `package.json`?
1. Native npm modules (those written in C, not JavaScript) need to be compiled, and here we have two different compilation targets for them. Those used in application need to be compiled against electron runtime, and all `devDependencies` need to be compiled against your locally installed node.js. Thanks to having two files this is trivial.
2. When you package the app for distribution there is no need to add up to size of the app with your `devDependencies`. Here those are always not included (because reside outside the `app` directory).

### Project's folders

- `app` - includes polymer-starter-kit
- `config` - place where you can declare environment specific stuff for your app.
- `build` - in this folder lands built, runnable application.
- `releases` - ready for distribution installers will land here.
- `resources` - resources needed for particular operating system.
- `tasks` - build and development environment scripts.


# Development

#### Installation

```
npm install
```
It will also download Electron runtime, and install dependencies for second `package.json` file inside `app` folder.

#### Starting the app

```
npm start
```

#### Adding npm modules to your app

Remember to add your dependency to `app/package.json` file, so do:
```
cd app
npm install name_of_npm_module --save
```

#### Native npm modules

Want to use [native modules](https://github.com/atom/electron/blob/master/docs/tutorial/using-native-node-modules.md)? This objective needs some extra work (rebuilding module for Electron). In this boilerplate it's fully automated, just use special command instead of standard `npm install something` when want to install native module.
```
npm run install-native -- name_of_native_module
```
This script when run first time will add [electron-rebuild](https://github.com/electronjs/electron-rebuild) to your project. After that everything is wired and no further maintenance is necessary.

#### Working with modules

How about being future proof and using ES6 modules all the time in your app? Thanks to [rollup](https://github.com/rollup/rollup) you can do that. It will transpile the imports to proper `require()` statements, so even though ES6 modules aren't natively supported yet you can start using them today.

You can use it on those kinds of modules:
```js
// Modules authored by you
import { myStuff } from './my_lib/my_stuff';
// Node.js native
import fs from 'fs';
// Electron native
import { app } from 'electron';
// Loaded from npm
import moment from 'moment';
```

#### Including files to your project

The build script copies files from `app` to `build` directory and the application is started from `build`. Therefore if you want to use any special file/folder in your app make sure it will be copied via some of glob patterns in `tasks/build/build.js`:

```js
var paths = {
    copyFromAppDir: [
        './node_modules/**',
        './vendor/**',
        './**/*.html',
        './**/*.+(jpg|png|svg)'
    ],
}
```

#### Unit tests

This project has preconfigured [mocha](https://mochajs.org/) test runner with the [chai](http://chaijs.com/api/assert/) assertion library. To run the tests go with standard:
```
npm test
```
You don't have to declare paths to spec files in any particular place. The runner will search through the project for all `*.spec.js` files and include them automatically.

Those tests can be plugged into [continuous integration system](https://github.com/atom/electron/blob/master/docs/tutorial/testing-on-headless-ci.md).

# Making a release

**Note:** There are various icon and bitmap files in `resources` directory. Those are used in installers and are intended to be replaced by your own graphics.

To make ready for distribution installer use command:
```
npm run release
```
It will start the packaging process for operating system you are running this command on. Ready for distribution file will be outputted to `releases` directory.

You can create Windows installer only when running on Windows, the same is true for Linux and OSX. So to generate all three installers you need all three operating systems.

## Mac only

#### App signing

The Mac release supports [code signing](https://developer.apple.com/library/mac/documentation/Security/Conceptual/CodeSigningGuide/Procedures/Procedures.html). To sign the `.app` in the release image, include the certificate ID in the command as so,
```shell
npm run release -- --sign A123456789
```

#### Mac App Store
**CAUTION**: until [atom/electron/issues#3871](https://github.com/atom/electron/issues/3871) isn't resolved, the signing procedure probably will make your application crash right after run.

You should install the Electron build for MAS
```
export npm_config_platform=mas
rm -rf node_modules
npm install
```

To sign your app for Mac App Store
```shell
npm run release -- --mas --mas-sign "3rd Party Mac Developer Application: Company Name (APPIDENTITY)" --mas-installer-sign "3rd Party Mac Developer Installer: Company Name (APPIDENTITY)"
```

Or edit the `app/package.json`, remove the `//` from `//codeSignIdentitiy` and update the values with your sign indentities
```json
  "//codeSignIdentitiy": {
    "dmg": "Developer ID Application: Company Name (APPIDENTITY)",
    "MAS": "3rd Party Mac Developer Application: Company Name (APPIDENTITY)",
    "MASInstaller": "3rd Party Mac Developer Installer: Company Name (APPIDENTITY)"
  }
```

You can change the application category too
```json
  "LSApplicationCategoryType": "public.app-category.productivity"
```

If you insert your indentities in the package.json you can compile for MAS like
```
npm run release -- --mas
```

## Windows only

#### Installer

The installer is built using [NSIS](http://nsis.sourceforge.net). You have to install NSIS version 3.0, and add its folder to PATH in Environment Variables, so it is reachable to scripts in this project. For example, `C:\Program Files (x86)\NSIS`.

#### 32-bit build on 64-bit Windows

There are still a lot of 32-bit Windows installations in use. If you want to support those systems and have 64-bit OS make sure you've installed 32-bit (instead of 64-bit) Node version. There are [versions managers](https://github.com/coreybutler/nvm-windows) if you feel the need for both architectures on the same machine.

# License

The MIT License (MIT)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
