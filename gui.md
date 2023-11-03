---
suppress_logo: true
title: Graphical Interface
---

# OmenMon Graphical Interface (GUI) Mode

* [Notification Icon](#icon) -- [Tool Tip](#tip) / [Balloon Tip](#balloon)
* [Context Menu](#menu) -- [Fan](#menu-fan) / [Graphics](#menu-graphics) / [Keyboard](#menu-keyboard) / [Settings](#menu-settings)
* [Main Window](#main)
  * [Keyboard](#keyboard) -- [Color Dialog](#color) / [Color Parameter](#color-param) / [Color Preset](#color-preset) -- [Delete](#color-delete) / [Save](#color-save)
  * [Temperature](#temperature) / [Fan Monitoring](#fan) / [Fan Control](#fan-control) -- [Fan Program](#fan-program)
  * [System Status & Information](#system)
* [About Dialog](#about) / [Error Messages](#error)

**OmenMon** by default starts in graphical mode with a notification icon in the system tray. [Command-line mode](/cli/) is also available.

![OmenMon graphical mode overview](/pic/gui-overview.png)

## Notification Icon {#icon}

The application launches in minimized mode with no window being shown. It only places its icon in the notification area:

<img alt="Dynamic Notification Icon with No Background" src="/pic/gui-notify-icon-bgnone.png" />
<img alt="Static Notification Icon" src="/pic/gui-notify-icon-static.png" /> 
<img alt="Dynamic Notification Icon with Cool Background" src="/pic/gui-notify-icon-bgwarm.png" />
<img alt="Dynamic Notification Icon with Warm Background" src="/pic/gui-notify-icon-bgcool.png" />

The application icon can be **static** or **dynamic**, displaying the **maximum temperature** reading, with the latter optionally also showing a background indicating whether the _Performance_ mode is active. Icon type can be changed through the [context menu](#menu-settings) or directly in the [configuration file](/config/#guidynamicicon).

* **Left**-clicking on the icon shows the [main window](#main) or hides it if it was being shown already
* **Right**-clicking on the icon pops up the [context menu](#menu)

Application-created (i.e. non-system) notification icons may end up being hidden by _Windows_. If that happens, click on the upward arrow to display the overflow menu. You can then drag the icon back to the notification area to keep it visible at all times.

### Tool Tip {#tip}

Hovering the cursor over the icon shows a **tool tip**.

When a [fan program](#fan-program) is running, it can be used to check its status:

<img alt="Notification Tip" src="/pic/gui-notify-tip.png" width="50%" />

### Balloon Tip {#balloon}

A **balloon tip** can appear to relay important messages:

<img alt="Balloon Notification Tip" src="/pic/gui-notify-tip-balloon.png" width="50%" />

* This is currently only used if the user launches another instance of the application in graphical mode
  * Only one application instance is allowed in graphical mode as running multiple would make no sense
  * [Command-line mode](/cli/) can be used in parallel even the application is already running in graphical mode
* The functionality to show important status updates, such as possible overheating, is implemented but not currently used
* Balloon tips can be disabled entirely by setting [GuiTipDuration](/config/#guitipduration) to `0`

## Context Menu {#menu}

**Right**-clicking on the notification area icon brings up the context menu:

<img alt="OmenMon Context Menu" src="/pic/gui-menu.png" width="75%" />

The top of the context menu shows the following:

* Application name and version -- clicking on it brings up the [_About_ dialog](#about)
* Optional [translator credit](/config/#messages) -- only if using a translated version

The menu stays open even when clicked on, to facilitate changing multiple settings in one go. To close the context menu, click anywhere outside of it, for example on the desktop.

Most of the items are grouped into one of the four **submenus**: [Fan](#menu-fan), [Graphics](#menu-graphics), [Keyboard](#menu-keyboard) and [Settings](#menu-settings), except the following two on the bottom:

#### Show Monitor / Hide Monitor {#show-hide-monitor}

Shows or hides the [main window](#main), which is equivalent to clicking on the notification icon. The name of this item changes depending on whether the main window is already being shown or not.

#### Exit

Closes the application. **OmenMon** may still start automatically upon logon or respond to the _Omen_ key being pressed, depending on the choices in the [Settings](#menu-settings) submenu. The related settings can also be [changed from the command line](/cli/#task).

### Fan {#menu-fan}

The items in this submenu control the fans and the thermal platform settings:

<img alt="Fan Context Menu" src="/pic/gui-menu-fan.png" />

#### Maximum {#menu-fan-maximum}

Toggles the maximum fan-speed mode on or off. In this mode, each fan operates at around 5,500 revolutions per minute [rpm]. Maximum fan-speed mode is disabled by default.

If this setting is enabled, which is indicated with a check mark next to it, other fan settings are not available. To change the other settings, disable this setting first.

#### Fan Program List {#menu-fan-program}

Runs or terminates a pre-configured [fan program](/config/#fan-programs). The entries here will differ depending on your settings. The sample configuration file has three programs: _OEM Test_, _Power_ and _Silent_.

If a program is active, it is indicated with a check mark, and other fan settings are not available. To change the other settings, first terminate the active fan program by clicking on the check-marked item.

#### Fan Mode List {#menu-fan-mode}

Sets the fan performance mode.

Only _Thermal Platform Version 1_ modes _Default_, _Performance_ and _Cool_ will be displayed by default, unless a different mode is enabled, in which case it will be shown as well. The remaining choices are _Thermal Platform Version 0_ legacy modes. You can still see and enable these from the [main window](#fan-control).

If a mode is selected here, the application will not take any steps to make sure it persists, which means it will generally revert back to _Default_ in 120 seconds [s] unless [Const fan control](#fan-control) setting is selected in the [main window](#main).

Even if this setting is not available, for example when a [fan program](#menu-fan-program) is active, the current fan mode is shown with a check mark.

#### Off {#menu-fan-off}

Toggles the fans on or off. The fans are on by default. This can be used to switch them off completely. Note that this can lead to the system overheating within seconds if under load.

If this setting is enabled, which is indicated with a check mark next to it, other fan settings are not available. To change the other settings, disable this setting first.

### Graphics {#menu-graphics}

The items in this submenu control the graphics adapter and video settings:

<img alt="Graphics Context Menu" src="/pic/gui-menu-gpu.png" />

#### High Refresh Rate / Standard Refresh Rate {#refresh-rate}

This can be used to quickly switch between high and regular screen refresh rates. The predefined rates are controlled by the [PresetRefreshRateHigh](/config/#presetrefreshratehigh) and [PresetRefreshRateLow](/config/#presetrefreshratelow) configuration settings, which you should adjust to match your hardware specifications:

* The **high** refresh rate is usually 144 or 165 Hertz, and the latter value is the default
* The **low** (standard) refresh rate is generally 60 Hertz [Hz] and that is the default

If the current refresh rate is any of the two preset rates, a check mark is shown next to it.

#### Base Power / Extra Power / Extra Power with Boost

These three mutually-exclusive presets control the GPU power level:

* **Base Power** disables both _Custom TGP_ and _PPAB_, leaving _Base TGP_ only
* **Extra Power** enables _Custom TGP_ but keeps _PPAB_ off
* **Extra Power with Boost** enables both _Custom TGP_ and _PPAB_

On the author's system, these correspond to the power levels of 80, 115 and 150 Watt [W] respectively.

A check mark appears next to the active preset. If _PPAB_ is enabled with no _Custom TGP_, this corresponds to the base setting.

#### Reload Color Profile

This item lets you reload the default color profile associated with the display. Sometimes the profile is dropped when display settings change or a 3D application is launched. This is a quick workaround for whenever that happens. The same workaround is also a part of the [Advanced Optimus Fix](#advanced-optimus-fix).

#### Set Display Off

Clicking this item will power off the display and switch the keyboard backlight off while the machine remains powered on. This might be useful when running background tasks and keeping the lid up for better thermal performance.

As an exception, the menu will automatically close when you click on this item (since the screen is supposed to go off).

#### Discrete Exclusive / Optimus Soft-Switching

These two mutually-exclusive settings control the choice of the GPU. Note that this is <u>not</u> the _Advanced Optimus_ switch: _Advanced Optimus_ settings can only be changed from the _nVidia Control Panel_ or the _nVidia_ notification icon if you enabled it.

This is equivalent to changing the pertinent setting in the _UEFI Setup_ but saves you the trip. A reboot is required, and you will be prompted whether you want to proceed with it at once.

### Keyboard {#menu-keyboard}

This submenu exposes some of the keyboard settings:

<img alt="Keyboard Context Menu" src="/pic/gui-menu-kbd.png" />

Much more is available through the [main window](#keyboard).

#### Backlight

Toggles the keyboard backlight on and off. Note that you can still switch presets even if the backlight is off at the moment.

#### Keyboard Preset List {#menu-keyboard-preset}

Loads a pre-configured keyboard backlight [color preset](/config/#color). The entries here will differ depending on your settings. Two hard-coded presets appear if none are defined in the configuration file: _OmenMon Cool_ and _OEM Default_. The sample configuration file has a couple more.

You can save and delete presets using the [main window](#keyboard).

### Settings {#menu-settings}

This submenu exposes some of the application settings:

<img alt="Settings Context Menu" src="/pic/gui-menu-settings.png" />

Much more can be changed in the [configuration file](/config/).

Changes to these settings take effect immediately, and the configuration file is saved the moment any settings change. 

#### Stay on Top

If **enabled**, the main window will stay on top of all other windows even if deactivated. This might be useful if you're for example running a benchmark but still want to monitor the fan and temperature readings.

Otherwise, if this setting is **disabled**, the window will be moved to the background and possibly become obscured by the active window. This is the default behavior.

This setting defaults to **disabled**.

#### Dynamic Icon

If **disabled**, the icon will be a static picture:

<img alt="Static Notification Icon" src="/pic/gui-notify-icon-static.png" /> 

If **enabled**, the application icon will report the current highest temperature reading across all monitored sensors:

<img alt="Dynamic Notification Icon with No Background" src="/pic/gui-notify-icon-bgnone.png" />

How often the value is updated is controlled by the [UpdateIconInterval](/config/#updateiconinterval) configuration setting.

Enabling the dynamic icon puts an extra workload on the Embedded Controller even when the [main window](#main) is hidden. This might be worth considering if you are using other applications that also interact with the Embedded Controller.

This setting defaults to **disabled** however the sample configuration file sets it to **enabled**.

#### Dynamic Background

This setting is only applicable if the [Dynamic Icon](#dynamic-icon) setting is enabled.

If **disabled**, the icon will have no background.

If **enabled**, the icon will have a <span style="background-image: linear-gradient(90deg, #ff0802, #ac02ff); color: white; font-weight: bold; padding: 0em 0.2em;">warm</span> background if [_Performance_ mode](#menu-fan) is enabled, or a <span style="background-image: linear-gradient(0deg, #8804ff, #03ef9b); color: white; font-weight: bold; padding: 0em 0.2em;">cool</span> background in any other mode.

<img alt="Dynamic Notification Icon with Cool Background" src="/pic/gui-notify-icon-bgwarm.png" />
<img alt="Dynamic Notification Icon with Warm Background" src="/pic/gui-notify-icon-bgcool.png" />

The hardware seems to have a tendency to drop out of the _Performance_ mode on its own sometimes, so this is a useful thing to check for. However note that if a fan program is running, the specified _Performance_ mode will be maintained automatically, so this is for informational purposes only in such case: you do not need to take any action.

This setting defaults to **disabled** however the sample configuration file sets it to **enabled**.

#### Start with Windows

If **enabled**, the application will be started whenever a user logs into _Windows_. This is equivalent to running `-Task Gui=On` from the [command line](/cli/#task).

Changing this setting also changes the [AutoStartup](/config/#autostartup) setting in the configuration file.

This setting defaults to **disabled** however the sample configuration file sets it to **enabled**.

#### Apply Settings on Startup

If **enabled**, the application will upon startup apply the default graphics power preset and fan program as outlined in the documentation for the [AutoConfig](/config/#autoconfig) configuration setting.

The `Gui` task state will also be synchronized to the value of the [AutoStartup](/config/#autostartup) setting.

Note that to fully take advantage of this setting, the configuration file has to be edited manually.

This setting defaults to **disabled** however the sample configuration file sets it to **enabled**.

#### Advanced Optimus Fix

This setting attempts to work-around some issues related to the _nVidia Advanced Optimus_ functionality. It is equivalent to enabling the `Mux` [task](/cli/#task), that is running `-Task Mux=On` from the command line.

If **enabled**, whenever the _nVidia Advanced Optimus_ switches the GPU, if it is the discrete GPU that is being enabled and this occurs the first time following a reboot, the application will:

* Reapply the color profile -- to workaround the bug where the color profile is not being applied
* Restart the _Windows Explorer_ shell -- to workaround the bug where the screen starts to stutter
* Restart the _nVidia Display Container_ service -- to restore any _nVidia_ notification-area icons, which no longer appear following the _Explorer_ shell restart

There is no corresponding configuration option for this setting. It is **disabled** by default.

#### Intercept Omen Key

This setting controls whether the application responds to the _Omen_ key. It is equivalent to enabling the `Key` [task](/cli/#task), that is running `-Task Key=On` from the command line.

The application's response to the _Omen_ key can be further controlled with the [KeyCustomAction](/config/#key) and [KeyToggleFanProgram](/config/#keytogglefanprogram) configuration settings. Briefly, subsequent key presses can either toggle the main window or fan program or the key can be used to launch another application with arbitrary arguments.

Note that to fully take advantage of this setting, the configuration file has to be edited manually.

There is no corresponding configuration option for this setting. It is **disabled** by default.

## Main Window {#main}

The main window can be brought up by either clicking on the [notification icon](#icon), choosing the _Show Panel_ item from the [context menu](#menu), or by pressing the _Omen_ key -- if the [Intercept Omen Key](#intercept-omen-key) setting is enabled and [KeyCustomAction](/config/#key) is disabled.

<img alt="OmenMon Main Window" src="/pic/gui-main.png" width="75%" />

The window dimensions cannot be adjusted, and the window itself cannot be minimized: it can only be shown or hidden.

* Clicking the `?` button brings up the [About dialog](#about)
* Clicking the `×` button or pressing <kbd>Alt</kbd>-<kbd>F4</kbd> hides the window from view
  * If [GuiCloseWindowExit](/config/#guiclosewindowexit) is set to **true**, it closes the application entirely instead

The window can also be hidden by clicking on the [notification icon](#icon) when it is being shown, or by choosing the [Hide Panel](#show-hide-panel) item from the context menu.

The items in the main window are organized into the following groups: [Keyboard Backlight & Color](#keyboard), [Temperature Sensor Readings](#temperature), [Fan Monitoring](#fan) & [Control](#fan-control), and [System Status & Information](#system).

### Keyboard Backlight & Color {#keyboard}

<img alt="Keyboard Controls" src="/pic/gui-main-kbd.png" width="75%" />

* Use the **checkbox** to toggle keyboard backlight on and off
  * If the backlight is off, no changes can be made to the keyboard settings here
  * However, presets can still be loaded from the [context menu](#menu-keyboard) and changes can be made from the [command line](/cli/#color)
* The **picture** shows the current backlight colors for each of the four zones
  * Click on a zone to bring up the [Color dialog](#color) for the given zone

#### Color Dialog {#color}

<img alt="Color Dialog" src="/pic/gui-color.png" width="50%" />

* The **window title** tells you the zone the color of which is being changed
* Choose a color from the list or using the picker: changes are applied **in real time**
* Take note of the **custom** colors:
  * The leftmost four are the current backlight colors
  * The rightmost ten are vivid colors verified to look good with the LEDs (not all colors do)
  * The middle two slots are for your own use: they are retained for as long as the main window stays open
* Click **OK** or press <kbd>Enter</kbd> to dismiss the dialog at any time

#### Color Parameter {#color-param}

* Instantly set the four zones to any color combination by typing the parameter
* The format follows the same syntax as when [changing colors from the command line](/cli/#color)
* Whenever you change the colors using the [Color dialog](#color), the parameter value is updated automatically

#### Color Preset List {#color-preset}

Pick a preset from the **drop-down list** to immediately apply it

#### Delete Preset {#color-delete}

Press the `×` button to **delete** a preset.

A confirmation is required:

<img alt="Delete Preset Dialog" src="/pic/gui-main-preset-del.png" width="25%" />

#### Save a Preset {#color-save}

Press the `✓` button to **save** a preset.

* Name the new preset
* Press `✓` again to confirm
* Or, press `×` if you changed your mind

<img alt="Save a Preset Dialog" src="/pic/gui-main-preset-save.png" width="25%" />

### Temperature Sensor Readings {#temperature}

<img alt="Temperature Sensor Readings" src="/pic/gui-main-temp.png" width="50%" />

This portion of the main window shows temperature sensor readings.

* Up to 9 sensors are available, which includes:
  * 8 _Embedded Controller_ sensors labeled `CPUT`, `GPTM`, `RTMP`, `TMP1`, `TNT2`-`TNT5`
  * 1 _BIOS_ sensor labeled `BIOS`
* Not all sensors are active all the time
  * For example the `GPTM` sensor is only active when the discrete GPU is in use
* Hover the mouse over the particular sensor to see its description
* A superscript **<sup>+</sup>** or **<sup>-</sup>** next to a sensor indicates an ascending or descending trend
* The temperature is shown in degrees Celsius [°C]
* Readings are updated every [UpdateMonitorInterval](/config/#updatemonitorinterval) seconds [s]

### Fan Monitoring {#fan}

<img alt="Fan Monitoring" src="/pic/gui-main-const.png" width="75%" />

Within the _Fan Monitoring & Control_ group:
* The **left**-hand side shows the CPU fan data
* The **right**-hand side shows the GPU fan data

For each fan, the following are shown:
* Current fan speed in revolutions per minute [rpm] = [<sup>1</sup>/<sub>min</sub>] = [min⁻¹] = [′⁻¹]
* Relative fan rate as a ratio of the maximum speed in percent [%]

Additionally, the relative fan rate is shown on a bar for each fan:
* The <span style="background-image: linear-gradient(0deg, #8804ff, #03ef9b); color: white; font-weight: bold; padding: 0em 0.2em;">cool</span> bar, moving left to right, refers to the CPU fan
* The <span style="background-image: linear-gradient(90deg, #ff0802, #ac02ff); color: white; font-weight: bold; padding: 0em 0.2em;">warm</span> bar, moving right to left, refers to the GPU fan

Vertical trackbars on each side automatically move as the fan speed levels change -- unless `Const` mode is selected, in which case they wait to be moved by the user in order to select the desired speed level for each fan.

If applicable, a value beneath the bars on the right-hand side indicates the remaining time until the fan mode reverts back to the default settings. This is indicated in seconds [s] = [″].

Fan readings are updated every [UpdateMonitorInterval](/config/#updatemonitorinterval) seconds [s].

### Fan Control {#fan-control}

<img alt="Fan Controls" src="/pic/gui-main-fan.png" width="50%" />

Fans can be controlled with the **radio buttons**, of which there are five -- each indicating a different control mode:

* **Prog** to use a [fan program](#fan-program) selected from the drop-down list
* **Auto** to use the automatic defaults with a mode picked from the drop-down list
* **Const** to hold the fans at a constant speed
* **Max** to set the fans to maximum speed
* **Off** to switch the fans off entirely

A border around the <span style="background-image: linear-gradient(45deg, #ff0802, #ac02ff); padding: 0.2em 0.2em 0.3em;"><span style="background-color: white; font-weight: bold; padding: 0em 0.2em 0.1em">`✓`</span></span> button indicates the selected settings have changed and are different from those currently active. Click the button to apply them.

The settings are applied only when the `✓` button is pressed. It can still be pressed even if the settings are unchanged, as it might sometimes be useful to reapply the same settings.

#### Automatic Fan Mode {#fan-auto}

* The drop-down list by default shows the currently-active mode
* Modes other than the _Default_ can be selected from the drop-down list
* Unlike in the [context menu](#menu-fan), legacy modes can be selected here as well
* If you want a mode other than _Default_ to persist, select **Const** afterwards
  * Otherwise, the mode will revert to _Default_ when the timer runs out
  * Do not press the `✓` button after selecting **Const** as this would switch to the **Const** mode

#### Constant-Speed Fan Mode {#fan-const}

When the **Const** option is selected, the following changes, regardless of whether the `✓` button was pressed:

* The vertical trackbars on each side, which normally cannot be dragged by the user but keep adjusting automatically to indicate the current fan speed level, stop moving and become accessible -- the <span style="background-color: #ccc; font-weight: bold; padding: 0 0.2em">gray</span> color changes to <span style="background-color: #0078d7; color: white; font-weight: bold; padding: 0 0.2em">blue</span>
* The timer will not be allowed to run out, which means any custom settings will persist for as long as the application keeps running and the window is not hidden -- for details on how this is implemented, see [FanCountdownExtendThreshold](/config/#fancountdownextendthreshold)

When using the trackbars for setting the custom fan speed, keep in mind the following:

* The lowest position corresponds to level 20 and the highest -- to level 55, that is 2,000 and 5,500 rpm respectively
  * These can be adjusted with [FanLevelMax](/config/#fanlevelmax) and [FanLevelMin](/config/#fanlevelmin)
* If the lowest position is selected, it is interpreted as a 0
  * However, the hardware constraint that **at least one fan must be running at any given time** is upheld
  * If you want both fans off entirely, which is generally not a good idea, use the **Off** setting

Remember to press the <span style="background-image: linear-gradient(45deg, #ff0802, #ac02ff); padding: 0.2em 0.2em 0.3em;"><span style="background-color: white; font-weight: bold; padding: 0em 0.2em 0.1em">`✓`</span></span> button to apply the settings afterwards.

#### Fan Program

Pick a custom pre-defined fan program from the drop-down list and press the <span style="background-image: linear-gradient(45deg, #ff0802, #ac02ff); padding: 0.2em 0.2em 0.3em;"><span style="background-color: white; font-weight: bold; padding: 0em 0.2em 0.1em">`✓`</span></span> button to run it.

The program will continue running for as long as the application is open. You can monitor the program status in the [System Status & Information](#system) area if the main window is being shown, as well as in the notification icon [tool tip](#tip).

Switching to any other control mode and applying the settings will also automatically terminate the fan program. However, making any changes to the fan settings from the [command line](/cli/) does not take into account whether a fan program is running. In fact it is possible to [run a fan program from the command line](/cli/#prog) in parallel as well. The responsibility not to run more than a single fan program at any given time is with the user.

For more details regarding this functionality and how to configure it, see [Fan Programs](/config/#fan-programs), [FanCountdownExtendThreshold](/config/#fancountdownextendthreshold), [UpdateProgramInterval](/config/#updateprograminterval).

### System Status & Information {#system}

<img alt="System Status & Information" src="/pic/gui-main-sys.png" width="50%" />

This portion of the window consists of a text field with **three rows**. These begin with the system data as reported by WMI, which might be useful for issue tracking, as well as to differentiate platform-specific settings in the future:

* **HP** -- Manufacturer
* **8A14** -- Product Identifier
* **32.25** -- Product Version
* **Mfg 20220101** -- [Born-on Date](/cli/#mfgdate)

The next couple of values might come handy for performance evaluation. In particular, AC adapter issues, if present, may be preventing the hardware from operating at full speed:

* **215W** -- [Default Power Limit 4](/cli/#system)
* **AC Power OK** -- [Smart AC Adapter Status](/cli/#adapter)

The following row focuses on the GPU:

* **GPU Optimus** -- [GPU Mode](/cli/#gpumode)
* **DState D1 cTGP PPAB** -- [GPU Power](/cli/#gpu)
  * If disabled, these show as **~~cTGP~~** **~~PPAB~~** (struck-through) and in gray
* **Not Throttling** -- [Throttling Status](/cli/#throttling)

Much more of the status information can be retrieved by running `-Bios` from the [command-line mode](/cli/#bios).

The bottom row is primarily used for [fan-program](#fan-program) status reporting. In the example:

* **T<sub>max</sub> 70°C** is the highest temperature reading across all the sensors
* **Lvl 70** is the selected program temperature level -- the highest entry lower or equal than the temperature reading [°C]
* **Fans 40, 42** are the fan levels corresponding to the temperature level -- the fans have been set to these speeds [krpm]
* **Power** is the name of the currently-running fan program
* **@ 11:52:35** is the timestamp when the fan program was last updated

Even if the main window is hidden, the same information can be obtained from the notification icon [tool tip](#tip).

## About Dialog {#about}

An _About_ dialog is available by either clicking on the question-mark icon in the [main window](#main) title bar or the application name in the [context menu](#menu). The dialog shows the application version alongside a brief description and a link to the project homepage:

<img alt="OmenMon About Dialog" src="/pic/gui-about.png" width="50%" />

### Error Messages {#error}

The _About_ dialog is repurposed for displaying error messages. 

As an example, using **OmenMon** in graphical interface mode requires compatible _HP Omen_ hardware. An error message is produced upon startup otherwise:

<img alt="OmenMon Error Message Dialog" src="/pic/gui-error.png" width="50%" />

Unhandled exceptions will still be shown with the default prompt to facilitate bug fixes.

Note that although the graphical mode cannot be launched without the compatible _HP Omen_ WMI BIOS interface, where possible, some of the application functionality, such as the [Embedded Controller command-line operations](/cli/#embedded-controller-operations), should still be available for use with non-*HP Omen* hardware.
