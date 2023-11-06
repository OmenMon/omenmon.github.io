---
suppress_logo: true
title: Other Info
---

# More Information

* [Considerations](#considerations) -- [Legal](#legal)
* [Acknowledgements](#acknowledgements) -- [Application](#application) / [Web Page](#web-page)
* [Version History](#version-history)

## Considerations

* The application requires administrative privileges to access the WMI BIOS routines and install a kernel-mode driver to interact with the Embedded Controller.

* The only dependency is _Microsoft .NET Framework 4.8.1_. This is the version that should already be included with Windows. In other words, no external prerequisites need to be installed in a typical scenario.

* Most features are specific to _HP_ devices with a compatible BIOS interface exposed by the `ACPI\PNP0C14` driver but command-line Embedded Controller operations and the _nVidia Advanced Optimus_ fix task should work on all compatible hardware

* This application might not work well with other programs that access the Embedded Controller frequently. In particular, since it is intended as a replacement for the _Omen Hub_ (_Omen Control Center_), it is not expected to run concurrently with it. For interactions with other applications, Embedded Controller footprint may be reduced by hiding the monitor panel window, and switching to a static notification icon.

* Due to the fact that the same executable is designed to run both as a CLI (console) and GUI application, which is not officially supported on _Windows_, there are some quirks with how the application operates in console mode: redirecting output to a file is not possible, and interacting with the application while it is running is also interacting with the underlying command prompt (however, the command-line mode is non-interactive, so it doesn't matter in practice).

  * If you are using _PowerShell_ as your default shell and run into issues with how the console output is displayed, type `start cmd` to open a classic _Command Prompt_ window, and run the application from there.

### Legal

* Whenever the _HP_ or _Omen_ brands are referred to, it is for informational purposes only. Neither the application nor the website are affiliated with or endorsed by the manufacturer of the hardware the application operates on.

* This software and documentation provided free-of-charge comes with no warranty whatsoever, neither should fitness for any particular purpose be assumed or implied. **If you decide to run this software, you are doing so at your own risk, and solely assume all responsibility.** If you do not agree with this, do not use the application.

## Acknowledgements

### Application

* Includes code from [Open Hardware Monitor](https://www.openhardwaremonitor.org/) and its successor [Libre Hardware Monitor](https://github.com/LibreHardwareMonitor/LibreHardwareMonitor)
  * Copyright © 2009-2017 Michael Möller
  * Copyright © 2020-2023 LibreHardwareMonitor & Contributors
  * Licensed under the terms of the [Mozilla Public License (MPL) 2.0](https://mozilla.org/MPL/2.0/)

* Includes _WinRing0_ driver from [OpenLibSys](https://openlibsys.org/manual/WhatIsWinRing0.html)
  * Copyright © 2007-2010 OpenLibSys & Noriyuki Miyazaki
  * Licensed under the terms of the [Modified BSD License](https://openlibsys.org/manual/License.html)

* Based on the research into internal BIOS functions implemented earlier in [OmenHwCtl](https://github.com/GeographicCone/OmenHwCtl)
  * Copyright © 2023 GeographicCone (same author as **OmenMon**)

* Inspired by [Notebook Fan Control](https://github.com/hirschmann/nbfc)
  * Copyright © 2012-2019 Stefan Hirschmann

* Tray icon font is a variation of [Iosevka](https://be5invis.github.io/Iosevka), modified with [FontForge](https://fontforge.org/)
  * Copyright © 2015-2023 Renzhi Li (aka Belleve Invis)
  * Licensed under the [SIL Open Font License 1.1](https://scripts.sil.org/OFL)

* Artwork designed in [Inkscape](https://inkscape.org/) and converted to the `.ico` format with [icoutils](https://www.nongnu.org/icoutils/)

### Web Page

* Proportional font is the [Dinish](https://github.com/playbeing/dinish) typeface by [Bert Driehuis](https://github.com/playbeing)
  * Licensed under the [SIL Open Font License 1.1](https://scripts.sil.org/OFL)
  * Based on earlier work by [Altinn](https://github.com/Altinn/altinn-din), [Datto](https://www.datto.com/fonts/d-din), and originally [Charles Nix](https://www.monotype.com/studio/charles-nix)
* Monospace font is the [Iosevka](https://github.com/be5invis/Iosevka) typeface by [Renzhi Li](https://github.com/be5invis) (aka Belleve Invis)
  * Licensed under the [SIL Open Font License 1.1](https://scripts.sil.org/OFL)
* Design and layout based on the [Cayman](https://github.com/pages-themes/cayman) theme by [Parker Moore](https://github.com/parkr)
  * Licensed under the terms of [Creative Commons Zero 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/) license
  * Includes [Normalize CSS](https://github.com/necolas/normalize.css) by [Nicolas Gallagher](https://github.com/necolas)
    * Originally version 3.0.2, upgraded to version 8.0.1
    * Licensed under the terms of the [MIT License](https://github.com/necolas/normalize.css/blob/master/LICENSE.md)
* Syntax coloring using [Solarized Dark Color Scheme](http://ethanschoonover.com/solarized) by [Ethan Schoonover](https://ethanschoonover.com/)
  * SASS definition based on the work by [Mohsen Azimi](https://github.com/mohsen1/)
  * Based on earlier work by [Quint Guvernator](https://gist.github.com/qguv/7936275), [Nicolas Hery](https://gist.github.com/nicolashery/5765395), [Matteo Scotuzzi](https://gist.github.com/scotu/1272660) and [Thomas Reynolds](https://gist.github.com/tdreyno/1125708)
* Fonts processed for Web use with [FontTools](https://github.com/fonttools/fonttools)'s `pyftsubset` and [Bert Driehuis](https://github.com/playbeing)'s `woff2css`
* Favicon generated with [icoutils](https://www.nongnu.org/icoutils/)
* Artwork designed in [Inkscape](https://inkscape.org/)

## Version History

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
