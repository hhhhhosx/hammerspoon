# Contributing to Hammerspoon

Hammerspoon is really two things:
* The app itself
* The extensions

## How is everything built?

The app itself is built using Xcode. You must open `Hammerspoon.xcworkspace` rather than `Hammerspoon.xcodeproj`. If you open the latter, your build will fail because it will not know about the Cocoapods that Hammerspoon depends on (see `Podfile` in the git repository, for the current list of required pods).

The extensions are built at the same time as the app, but are not directly known to Xcode. Instead, they are built by `extensions/build_extensions.sh` which will either trigger a Makefile supplied by the extension, or drop in a generic Makefile. The resulting `.lua`/`.so` files are collected and included in the final Hammerspoon.app.

## Contributing to the app itself

There is not much to say here really:
* Grab the source
* Open Hammerspoon.xcworkspace in Xcode (not the xcodeproj)
* Make the changes you want
* Push them up to a fork on GitHub
* Propose a Pull Request on GitHub
* Talk to us in #hammerspoon on Freenode

## Contributing to the extensions

This is really where the meat of Hammerspoon is. Extensions can either be pure Lua or a mixture of Lua and Objective-C.

### Writing a pure Lua extension ###

These extensions generally provide useful helper functionality for users (e.g. abstracting other extensions).

To create such an extension:
* Clone the Hammerspoon git repository
* cd into the `extensions` directory
* Make a directory for your extension
* Create an `init.lua` to contain your code
* Follow the patterns used in `sample-extensions/pure-lua`

### Writing a mixed Lua/Objective-C extension ###

These extensions generally expose a system API for users to automate (e.g. adjusting screen brightness using IOKit).

To create such an extension:
* Clone the Hammerspoon git repository
* cd into the `extensions` directory
* Make a directory for your extension
* Create an `init.lua` to load your Objective-C code and contain any additional Lua code
* Create an `internal.m` to contain your Objective-C code
* Follow the patterns used in `sample-extensions/mixed-lua-objc`

### Writing some other kind of extension

Ultimately, you can do whatever you like, so long as you output something that the Lua interpreter in Hammerspoon can load, when a user issues a `require` call for your module.

To do anything beyond what is described above, you will need to provide your own Makefile that responds to the following targets:
* install
* clean

The `clean` target should remove any build artifacts (e.g. `.so`. files). The `install` target should copy the output of your build into `$(PREFIX)` (which will be supplied via an environment variable).

There are three options for how your `install` target should place the files:
* `$(PREFIX)/YOUR_MODULE_NAME/init.lua` - If you hae a lua-only extension, or a mixed extension
* `$(PREFIX)/YOUR_MODULE_NAME.lua` - If you have a lua-only extension, or a mixed extension with complex `.so` layouts
* `$(PREFIX)/YOUR_MODULE_NAME.so` - If you refuse to have a simple `init.lua` loader (described above)

It is recommended that you follow the first convention and retain all of your `.lua` and `.so` files in `$(PREFIX)/YOUR_MODULE_NAME/`