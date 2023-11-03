---
suppress_logo: true
title: Building
---

# Building OmenMon

* [Build Instructions](#instructions) -- [Using Make](#make)
* [Project Structure](#structure) -- [Source Layout](#layout) / [Binaries](#binaries)

**OmenMon** is written in _C# 11_ targeting _.NET Framework 4.8.1_. Earlier framework versions should work as well but the choice to go with the latest was made due to the possible DPI awareness handling (user interface scaling) improvements.

Reliance on the _.NET Framework_ instead of the newer _.NET Core_ means there should be no dependencies to be resolved by users at runtime, since _.NET Framework 4.8.1_ is essentially bundled with any recent _Windows_ release from version 10 onwards since September 2022 (this also includes LTSC 2021 with the latest updates).

To build **OmenMon**, you need a _Windows_ system with _Microsoft Build Tools_ (or _Visual Studio_) version 17 (2022) installed. Compiling with the previous version 16 (2019) would require code changes due to the _C# 11_ not being supported.

Earlier _C#_ versions would not work without some code having to be rewritten. This almost entirely relates to the definition syntax using features not available in the earlier language versions.

<u>Note</u>: the application has not been designed with the _Visual Studio GUI_. All of the code is hand-written and very different from what Visual Studio would automatically generate. As a result, it might not be possible to use GUI tools such as the _Forms Designer_ or _Resource Manager_ while working with the project.

## Build Instructions {#instructions}

* Download [Microsoft Build Tools for VS2022](https://aka.ms/vs/17/release/vs_BuildTools.exe) 
* Install: `vs_buildtools.exe --add Microsoft.VisualStudio.Workload.MSBuildTools --quiet`

### Using Make {#make}

* ~~Download packages with~~ `make prepare`. ~~This only has to be done once.~~
  * There are currently no external package dependencies, so this step can be skipped
* Build with `make build` started from the project root directory
  * Depending on the build environment, you might need to edit the path to `msbuild.exe` inside `make.cmd`
* To test some of the functionality, run `make test`
  * Note: this will change the keyboard backlight color for testing purposes
* To tidy up, use `make clean`. This does not remove the downloaded packages, if any.
  * If the `OmenMon.exe` file remains in use, run `make kill` to terminate any running application instances first
  * If the `OmenMon.sys` file remains in use (which could happen due to an unhandled crash), run the application again so that it attempts to access the Embedded Controller, and then close it (e.g. `OmenMon -Ec` or GUI mode)

## Project Structure {#structure}

* File names generally reflect class names, and the directory tree reflects namespace hierarchy
  * There is one namespace per file, usually one class per file, and, with only a couple exceptions, one file per class
* As much underlying code as possible is shared between the CLI and GUI
* `Bios*` and `EmbeddedController*` hardware routines are referenced only from within the `Hw` class in `Library\Hw.cs`
* `*Data.cs` files predominantly contain definitions, with very little code
* Calls to `Hw` class are handled by the `Platform` abstraction
  * This way the actual operation logic is separate from hardware implementation details, for example when retrieving temperature readings there is no need for the higher-level routines to make any distinctions depending on whether the data is coming from the Embedded Controller or the WMI BIOS, which allows for future extensibility.
  * The `Platform` abstraction is also where model-specific functionality differentiation could be implemented in the future: the code there retrieves platform (system board) name via WMI first, and only then sets up the hardware abstraction.

### Source Layout {#layout}

* `All` Common project metadata
* `App` Core application functionality and everything directly related to it
  * `App\App.cs` Application entry point
  * `App\Cli` Command-line mode application
    * `App\Cli\CliOp.cs` The main loop handling and processing command-line parameters
  * `App\Gui` Graphical user interface mode application
    * `App\Gui\GuiTray.cs` The application context that instantiates all other GUI classes
* `Driver` Kernel-mode _WinRing0_ driver
* `External` System API imported methods as well as data structures for interacting with them
* `Hardware` Routines for interacting with hardware
* `Library` General classes shared between CLI and GUI modes, abstracted from the main application to the extent possible
  * `Library\Gui` Libraries relating to the Windows graphical user interface (not applicable to CLI mode)
* `Resources` Resources to be embedded in the resulting executable (such as graphics)

### Binaries

* `Bin` Has the compilation result after running `make build`
  * `Bin\OmenMon.exe` is the resulting binary
* `Obj` Intermediate build data, safe to delete with `make clean`
* `Packages` Third-party dependencies: safe to delete, re-download with `make prepare`
  * This is provided just in case for future extensibility, as there are currently no such dependencies
