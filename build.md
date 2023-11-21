---
suppress_logo: true
title: Building
---

# Build Information

* [Build Instructions](#instructions) -- [Using Make](#make)
* [Project Structure](#structure) -- [Source Layout](#layout) / [Binaries](#binaries)
* [Version History](#history)

## Build Instructions {#instructions}

**OmenMon** is written in _C# 11_ targeting _.NET Framework 4.8.1_. Earlier framework versions should work as well but the choice to go with the latest was made due to the possible DPI awareness handling (user interface scaling) improvements.

Reliance on the _.NET Framework_ instead of the newer _.NET Core_ means there should be no dependencies to be resolved by users at runtime, since _.NET Framework 4.8.1_ is essentially bundled with any recent _Windows_ release from version 10 onwards since September 2022 (this also includes LTSC 2021 with the latest updates).

To build **OmenMon**, you need a _Windows_ system with _Microsoft Build Tools_ (or _Visual Studio_) version 17 (2022) installed. Compiling with the previous version 16 (2019) would require code changes due to the _C# 11_ not being supported.

Earlier _C#_ versions would not work without some code having to be rewritten. This almost entirely relates to the definition syntax using features not available in the earlier language versions.

<u>Note</u>: the application has not been designed with the _Visual Studio GUI_. All of the code is hand-written and very different from what Visual Studio would automatically generate. As a result, it might not be possible to use GUI tools such as the _Forms Designer_ or _Resource Manager_ while working with the project.

* Download [Microsoft Build Tools for VS2022](https://aka.ms/vs/17/release/vs_BuildTools.exe) 
* Install: `vs_buildtools.exe --add Microsoft.VisualStudio.Workload.MSBuildTools --quiet`
* Checkout or download the main [OmenMon](https://github.com/OmenMon/OmenMon) repository and the separate [OmenMon Resources](https://github.com/OmenMon/Resources) repository

### Using Make {#make}

* ~~Download packages with~~ `make prepare`. ~~This only has to be done once.~~
  * There are currently no external package dependencies, so this step can be skipped
* Build with `make build` started from the project root directory
  * Depending on the build environment, you might need to edit the path to `msbuild.exe` inside `make.cmd`
  * You can set the assembly version number and word using build properties: `-p:AssemblyVersion=1.2.3.4 -p:AssemblyVersionWord=Custom`
  * In the above scenario, an `AssemblyMetadata` `Timestamp` field will be added automatically with the format `yyyy-MM-dd HH:mm`
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

## Version History {#history}

### 0.58.0 (Pending)

  * Fix incorrect temperature level in the configuration file: thank you to **[@Rupurudu](https://github.com/Rupurudu)** for spotting it and submmiting a [pull request](https://github.com/OmenMon/OmenMon/pull/11)
  * Do not show the [default CPU Power Limit 4](https://omenmon.github.io/cli#system) value on unsupported models where it equals `0`: thank you to **[@TembuRaptor007](https://github.com/TembuRaptor007)** and **[@Rupurudu](https://github.com/Rupurudu)** for bringing my attention to this

### 0.57.0 (2023-11-19)

_**Organizational note:** from now on, all new builds will be marked as **releases**, since pre-releases (previews) are difficult to discover from the main repository page, leading to people not being aware of the latest builds. This build also brings all the changes from 0.56.1, 0.56.2, and 0.56.3 for those that never saw them._

  * Reduce the maximum believable temperature threshold for the `TNT2` sensor to 97°C, in response to a [report](https://old.reddit.com/r/HPOmen/comments/17oet58/omenmon_fan_gpu_keyboard_performance_monitoring/k973utz/) by _Reddit_ user **[ManuSC12](https://www.reddit.com/user/ManuSC12)** that the reading is permanently stuck at 98°C in his model, making it impossible to use fan programs: the long-term solution is to add a different case for that particular model number but since it's unknown which one it is, lower the threshold in the meantime
  * Do not check error status for [HasMemoryOverclock](https://omenmon.github.io/cli#hasmemoryoverclock), [HasOverclock](https://omenmon.github.io/cli#hasoverclock), [HasUndervoltBios](https://omenmon.github.io/cli#hasundervoltbios): let these calls fail silently, as they tend to do on older models, and just assume no support
  * Allow fine-grained control of fan-level setting with the new configuration options [FanLevelNeedManual](https://omenmon.github.io/config#fanlevelneedmanual) and  [FanLevelUseEc](https://omenmon.github.io/config#fanleveluseec) thank you to **[@n-elia](https://github.com/n-elia)** and **[P4R1H](https://github.com/P4R1H)** for the detailed reporting, hopefully this is the solution that finally covers all bases
  * Add the [FanCountdownExtendAlways](https://omenmon.github.io/config#fancountdownextendalways) configuration option to always continually extend the countdown for as long as the application is running, even with no fan program active, no constant-speed button selected, and the main window hidden, which means any custom fan settings can now be made persistent without the need for a fan program: thank you to **[@n-elia](https://github.com/n-elia)** for the [idea](https://github.com/OmenMon/OmenMon/issues/8#issue-1995887925) and the implementation suggestion
  * Report AC power status in the [System Status & Information](https://omenmon.github.io/gui#system) box in the main GUI window based on a system API call first, only query [smart AC adapter status](https://omenmon.github.io/cli#adapter) if on AC power
  * Add the [FanProgramDefaultAlt](https://omenmon.github.io/config#fanprogramdefaultalt) setting for a fan program to switch to whenever the system loses AC power, the program will be switched back to the original once AC power is restored, however all this only happens if [AutoConfig](https://omenmon.github.io/config#autoconfig) is enabled: thank you to _Reddit_ user **[ManuSC12](https://www.reddit.com/user/ManuSC12)** for the [idea](https://old.reddit.com/r/HPOmen/comments/17oet58/omenmon_fan_gpu_keyboard_performance_monitoring/k970bad/)

### 0.56.3 (2023-11-13)

  * If the call to set fan levels using WMI BIOS fails, try to set the level individually for each fan using the Embedded Controller (this applies to GUI _Const_ fan-control mode and fan programs only)

### 0.56.2 (2023-11-12)

  * Explicitly specify Embedded Controller mutex security in an attempt to better co-operate with other applications in sharing the access to the Embedded Controller
  * Adjust the sample configuration settings to reduce Embedded Controller I/O intensity in order to mitigate potential conflicts reported in some scenarios when other software attempts to access the Embedded Controller at the same time

### 0.56.1 (2023-11-12)

  * Disable keyboard UI for incompatible devices that report no backlight, let the [HasBacklight](https://omenmon.github.io/cli#hasbacklight) and [KbdType](https://omenmon.github.io/cli#kbdtype) BIOS calls fail silently instead of reporting any errors, which makes it easier to use the rest of the application with a not fully-compatible device: thank you to _Reddit_ user **[____N-](https://www.reddit.com/user/____N-)** for providing information that made this improvement possible
  * Update the sample fan programs in the bundled configuration file, link to an [editable fan curve visualization online](https://www.desmos.com/calculator/6vfpghtud0) 

### 0.56 (2023-11-10)

  * Fan speed values reported by the Embedded Controller will be discarded if they exceed [FanLevelMax](https://omenmon.github.io/config#fanlevelmax) by over 10%.
  * Incorporate changes from pre-release versions 0.55.1 and 0.55.2 into the release

### 0.55.2 (2023-11-10)

  * Disable keyboard backlight & color interface entirely on unsupported models with per-key RGB [keyboard type](https://omenmon.github.io/cli#kbdtype) as [requested](https://github.com/OmenMon/OmenMon/issues/3#issue-1984556312) by **[@dd871](https://github.com/dd871)**
  * Make the [GPU mode switching](https://omenmon.github.io/gui#gpu-mode) switch to `0x00` `Hybrid` mode instead of `0x02` `Optimus` if [system design data](https://omenmon.github.io/cli#system) byte #7 flag `0x08` is not set (but `0x04` is, since otherwise the menu item would not have been enabled): based on [further report](https://github.com/OmenMon/OmenMon/issues/1#issuecomment-1805499020) by **[@ArmynC](https://github.com/ArmynC)**

### 0.55.1 (2023-11-09)

  * Change the default for [KeyToggleFanProgram](https://omenmon.github.io/config#keytogglefanprogram) in line with the documentation for consistency, since the sample configuration file bundled with the release also has `<FanProgramDefault>None</FanProgramDefault>`, so subsequent keypresses would appear not to work
  * Fix translation template (`OmenMon.en_US.xml` in the [Localization](https://github.com/OmenMon/Localization) repository) issue where unnecessary backslash escape characters caused RTF control sequences to fail: thank you to **[@bbaallance](https://github.com/bbaallance)** for [the report](https://github.com/OmenMon/OmenMon/issues/1#issuecomment-1804073393)
  * Re-enable [GPU mode switching](https://omenmon.github.io/gui#gpu-mode) also for devices that have bit #2 (`0x04`) set in byte #7 of [system design data](https://omenmon.github.io/cli#system): thanks to **[@ArmynC](https://github.com/ArmynC)** for [the report](https://github.com/OmenMon/OmenMon/issues/1#issuecomment-1804141326)
  * Support Unicode characters other than 7-bit ASCII in rich-text fields: thank you to **[@bbaallance](https://github.com/bbaallance)** for [the report](https://github.com/OmenMon/OmenMon/issues/1#issuecomment-1804073393)

### 0.55 (2023-11-08)

  * Build process updates to allow setting version number dynamically in preparation for _GitHub_ build workflow
  * Minor code changes and comment improvements pending the public release of the source code
  * Publish the source code; additionally, publish [a separate repository](https://github.com/OmenMon/Resources) with non-GPL3 components
  * Reorganize the documentation: add license information, improve build instructions to cover setting assembly version via a build property and merging the files from the separate _Resources_ repository
  * First build to be completed using the automated _GitHub_ workflow
  * Add `BiosErrorReporting` configuration setting to optionally ignore BIOS errors instead of throwing an exception
  * Disable GPU mode-switching menu items instead of hiding them (introduced in 0.54)
  * Improve the _PowerShell_ console workaround and catch the exception it might cause (reported by **[@Kubagf](https://github.com/Kubagf)**)
  * Fix the issue where model-dependent platform fan and temperature array setup did not properly define the default case: thank you **[@Dragofagnir](https://github.com/Dragofagnir)** and **[@wangzhengbin](https://github.com/wangzhengbin)** 

### 0.54 (2023-11-06)

  * Make platform fan and temperature array setup model-dependent
  * Make BIOS calls to retrieve GPU mode not raise an exception on unsupported models, hide the menu items related to GPU mode in such scenarios
  * Add `GuiDpiChangeResize` configuration option to set whether the window should be automatically resized in response to DPI changes
  * Add `GuiSysInfoFontSize` configuration option to override the font size used for _System Information & Status_

### 0.53 (2023-11-06)

  * Update missing localization string for _Unknown_ throttling status (introduced in 0.51)

### 0.52 (2023-11-05)

  * Fix `DynamicIcon` and `DynamicIconHasBackground` configuration settings not being saved
  * Resolve the issue when unless the main window is being shown, temperature sensors are not updated before calculating maximum temperature. Thank you to **[@wangzhengbin](https://github.com/wangzhengbin)** for reporting this issue.

### 0.51 (2023-11-05)

  * Resolve the issue when a BIOS call to check throttling status results in an unhandled exception where not supported. The call is not supported on 2023 models where it yields BIOS error code 6. The status will now be reported as _Unknown_ in these scenarios. Thank you to **[@breadeding](https://github.com/breadeding)** for contributing information that made it possible to fix this issue.
  * Main window title consistency fix

### 0.50 (2023-11-04)

  * Initial public preview
  * Publish a complete documentation at [omenmon.github.io](https://omenmon.github.io/)
  * Publish an XML translation template at [github.com/OmenMon/Localization](https://github.com/OmenMon/Localization)
  * Detect _PowerShell_ console to workaround output issues

### 0.49

  * In anticipation that some of the routines may fail on different hardware (since it's only been tested on the author's laptop), add more attempts to catch exceptions that never occured in the tests thus far

### 0.48

  * Minor bugfixes throughout the whole application

### 0.47

  * Workaround dynamic notification icon issue: if icon handle is freed, icon will disappear until the next refresh if the notification text changes; not freeing the handle apparently eventually leads to a "generic error in GDI+." The issue is resolved by storing the previous icon handle and freeing it but only just before the icon is about to be updated anyway.

### 0.46

  * Optimize the automatic configuration process for faster initial loading (start the fan program in a separate thread if at launch)

### 0.45

  * Make the _Omen_ key optionally toggle fan program on and off if the main window is already being shown

### 0.44

  * Restructure the configuration settings layout, add new configuration options

### 0.43

  * Add a couple more of minor context-menu items

### 0.42

  * Improve the fan program routines and the user interface around it, including status reporting

### 0.41

  * Add fully-customizable fan program functionality both via the command-line and the GUI

### 0.40

  * Add the ability to run a fan program synchronously from the command line

### 0.39

  * Add fan program functionality to the GUI and make fan programs controllable through the interface alongside other modes (another intermediate step towards custom fan programs)

### 0.38

  * Add fan program data handling, load fan programs from the XML configuration file upon startup and save them back (an intermediate step towards custom fan programs)

### 0.37

  * Replace the inactive `IRSN` Embedded Controller temperature sensor with the temperature reported by the BIOS

### 0.36

  * Add the option to reload the color profile to the context menu

### 0.35

  * Implement system information and system status message functionality

### 0.34

  * User interface enhancements

### 0.33

  * Add fan controls to the GUI

### 0.32

  * Add real-time fan monitoring to the GUI

### 0.31

  * Refactor and consolidate between the CLI and the GUI the code referencing low-level BIOS and embedded controller operations

### 0.30

  * Improve localizable message and global-scope identifier consistency

### 0.29

  * Add real-time temperature monitoring to the GUI

### 0.28

  * Add hardware platform classes for abstracted sensor and fan control support

### 0.27

  * In a scenario when the DPI changes while the application is running, resize the main form in response to the DPI update

### 0.26

  * Add a simple _About_ dialog, also reused for caught exception error messages

### 0.25

  * _Omen_ key event handling improvements, close the form if the key is pressed when the form is already open

### 0.24

  * Automatically save the configuration, including color presets when changed, back to the configuration XML file

### 0.23

  * Implement the GUI functionality for color setting with instant updates

### 0.22

  * Add keyboard color setting through a preset to the CLI mode, also resolve read and written color values as preset names

### 0.21

  * Add keyboard line art to the main window, dynamically recolored every time the colors are updated

### 0.20

  * Designed the main window for monitoring and control in GUI mode

### 0.19

  * Display refresh rate adjustment via the GUI context menu

### 0.18

  * Optional customizable action for the _Omen_ key in the XML configuration file

### 0.17

  * Add _Omen_ key handler and an improved _Advanced Optimus_ fix, install and remove these tasks from either CLI or GUI (this completes the porting of the [OmenHwCtl](https://github.com/GeographicCone/OmenHwCtl) functionality)

### 0.16

  * Add task scheduling via the COM interface (an intermediate step towards porting the rest of the [OmenHwCtl](https://github.com/GeographicCone/OmenHwCtl) functionality)

### 0.15

  * Enable the changing of the settings through the GUI context menu interface

### 0.14

  * Add automatic startup and automatic configuration in GUI mode

### 0.13

  * Optionally provide a dynamic notification icon with custom font and dynamic background

### 0.12

  * Limit GUI mode to only a single instance, show a notification if already running; optimization of console routines

### 0.11

  * Add GUI mode, with the application starting minimized to tray by default

### 0.10

  * Add configuration variables loaded from an external XML file

### 0.09

  * Print out embedded controller register names in queries and in monitor, as well as allow referencing registers by their name instead of numerical value

### 0.08

  * Refactor the code and add support for message customization (localization) loaded from an external XML file

### 0.07

  * Improve the syntax versatility of BIOS command-line operations

### 0.06

  * Command-line support for BIOS controls (ported from [OmenHwCtl](https://github.com/GeographicCone/OmenHwCtl))

### 0.05

  * Support for BIOS controls via a WMI (CIM) interface

### 0.04

  * Support running either as a _Windows_ GUI or a console app within the same executable

### 0.03

  * Command-line support for embedded controller register monitoring (inspired by [NBFC](https://github.com/hirschmann/nbfc)'s `ec-probe`)

### 0.02

  * Command-line support for embedded controller queries

### 0.01

  * Embedded controller hardware interaction implementation by means of kernel-mode _Ring 0_ driver
