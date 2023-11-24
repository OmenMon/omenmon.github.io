---
suppress_logo: true
title: Configuration
---

# OmenMon Configuration

* [Configuration File](#file) -- [Loading](#load) / [Saving](#save)
* [Configuration Format](#format) -- [Boolean Values](#boolean) / [Numerical Values](#numerical) / [String Values](#string)
* [Configuration Entries](#entries)
  * [Color Presets](#color) / [Embedded Controller](#ec)
  * [Fan Control](#fan) / [Fan Programs](#fan-programs)
  * [GPU](#gpu) / [Graphical User Interface](#gui) / [Key Custom Action](#key)
  * [Preset Settings](#preset) / [Update Intervals](#update)
  * [Localizable Messages](#messages)
* [Sample Configuration](#example)

## Configuration File {#file}

Configuration is stored in the file `OmenMon.xml` in the same directory as `OmenMon.exe`

### Loading {#load}

* The presence of the configuration file is optional
* Any errors reading the file, whether due to it being absent, malformed, or inaccessible, will be silently ignored
* For settings where no configuration could be read, the application will use the predefined defaults

### Saving {#save}

* The configuration file will be saved the moment any of the pertinent settings are changed by the user
  * The configuration file is never saved when running in CLI mode
  * Only certain settings can be changed from the GUI
  * The remaining settings can only be modified by editing the configuration file (while the application is not running)
* The original file will be reused if present and not malformed, otherwise a new file will be generated
  * Comments in the original file are preserved, except if within the [ColorPresets](#color-presets) or [FanPrograms](#fan-programs) elements.
* The application saves all the settings it can read from the configuration file
  * Settings not listed in the original configuration file will be added with their default values
* If the settings could not be saved, an error will be shown
  * This could happen due to a permissions issue, or if the same file were being used by another process

## Configuration Format {#format}

* The configuration file is a standard XML document encoded in UTF-8 with no Byte Order Mark (BOM)
* The root element `<OmenMon>` has two child elements:
  * `<Config>` is where all the configuration variables are stored
  * `<Messages>` is where any of the message strings used by the program can be overriden

Most elements in the configuration section directly relate to configuration settings and have neither child elements nor attributes. The exceptions to this are `<ColorPresets>`, `<FanPrograms>` and `<KeyCustomAction>` discussed [further down below](#color-presets).

An example configuration entry follows the convention:

````xml
<?xml version="1.0" encoding="utf-8"?> 
<OmenMon>
    <Config>
        <Name>value</Name>
    </Config>
</OmenMon>
````

Where `value` can be any of:

### Boolean Values {#boolean}

Either:

* **true**, **yes**, **on** or **1** for an affirmative setting
* or **false**, **no**, **off** or **0** for a negative setting

Any of the letters can be upper-case.

When the configuration is saved, Boolean values are normalized to **true** or **false** respectively.

### Numerical Values {#numerical}

Any of:

* **0b&lt;_binary_value_&gt;**, such as `0b00000001`
* **0x&lt;_hexadecimal_value_&gt;**, such as `0xFF`
* **&lt;_decimal_value_&gt;** such as `1000`

Any of the letters in hexadecimal values can be upper-case.

When the configuration is saved, the values are normalized to the numerical base that makes the most sense for the particular configuration entry. Currently, all numerical settings are stored as decimal values.

### String Values {#string}

The `value` is a free-form string that is read verbatim without any parsing at the configuration-loading stage.

Note that special characters [have to be escaped](https://stackoverflow.com/a/46637835/290085) as per standard XML conventions. The gist of it:

* For `<` use `&lt;`
* For `>` use `&gt;`
* For `&` use `&amp;`

## Configuration Entries {#entries}

#### AutoConfig

A [Boolean value](#boolean) that defaults to **false**.

If set to **true**, once the application is started in GUI mode, automatically:

* Enable or disable automatic start-up when a user logs on, depending on the value of the [AutoStartup](#autostartup) setting
* Set the [GPU power level](/cli#gpu) to the value of [GpuPowerDefault](#gpupowerdefault)
* Start the [fan program](#fan-programs) named in [FanProgramDefault](#fanprogramdefault)
* Switch the [fan program](#fan-programs) to the one named in [FanProgramDefaultAlt](#fanprogramdefaultalt) whenever the system loses AC power, and switch it back when AC power is restored

This setting can be changed from the GUI context menu: [Settings](/gui#menu-settings) → [Apply Settings on Startup](/gui#apply-settings-on-startup)

#### AutoStartup

A [Boolean value](#boolean) that defaults to **false**.

When [AutoConfig](#autoconfig) is enabled, whether to automatically apply the setting to make the application start when a user logs on.

Note: it might be a bit counter-intuitive how this works but if you are <u>not</u> using [AutoConfig](#autoconfig), this setting is irrelevant, as whether the application starts automatically or not is determined by the `Gui` task being either enabled or disabled, which can be changed with:
* `OmenMon -Task Gui=On` in CLI mode
* [Settings](/gui#menu-settings) → [Start with Windows](/gui#start-with-windows) in GUI mode

The latter also changes the value for this setting.

#### BiosErrorReporting

A [Boolean value](#boolean) that defaults to **true**.

The default behavior is to throw an exception if a BIOS call returns an error response. If set to **false**, BIOS errors will be silently ignored instead. This makes it possible to try using the application with hardware that is not fully compatible.

### Color Presets {#color}

Keyboard backlight color preset entries follow the convention:

````xml
<?xml version="1.0" encoding="utf-8"?> 
<OmenMon>
    <Config>
        <ColorPresets>
            <Preset Name="DefaultOem">0F84FA:710FFA:F9350F:FAAC0F</Preset>
            <Preset Name="DefaultApp">0080FF:00FF00:00FF00:FFFFFF</Preset>
        </ColorPresets>
    </Config>
</OmenMon>
````

Where:
* `<ColorPresets>` is the root element all presets are contained within
* `<Preset Name="value">` is a container for each color preset entry
* The `value` of the `Name=` attribute is a [string](#string) used as the name of the preset
* The parameter value such as `0080FF:00FF00:00FF00:FFFFFF` is a specially-formatted [string](#string) that follows the same syntax as when [changing colors from the command line](/cli#color)

If you are using the GUI mode, the parameter values will be [automatically shown](/gui#color-param) and retained when you [save your preset](/gui#color-save). While there is no equivalent functionality to delete or save presets from the CLI mode, if the backlight is already set to the colors you like, you can manually add the value shown by `OmenMon -Bios Color` to the configuration file. You can also reference these presets from the command line, for example with `OmenMon -Bios Color=DefaultApp`

![Setting the colors from preset name](/pic/cli-bios-set.png)

The two presets shown above are hard-coded into the application and will populate the preset list if no other presets were defined. Although `DefaultOem` is the factory-default preset while `DefaultApp` is one of the author's favorites, this is mainly to make it easy to demonstrate and test the application's functionality. If you delete them they will never reappear -- as long as you have other presets defined.

If the preset name starts with `Default`, as is the case with the two example entries above, the actual name will be looked up through the [localization-message](#messages) system under the identifier starting with `GuiMenuActKbdColorPresetDefault`. For example, `DefaultApp` will be looked up under `GuiMenuActKbdColorPresetDefaultApp`, which resolves to "_OmenMon Cool_" and that is the name the preset will appear under throughout the GUI.

The takeaway from this is that you should <u>not</u> start your preset names with `Default` unless you're also providing a [localizable string](#messages) for them: it will still work but look ugly.

The `Default` preset name look-up does not happen in the command-line mode but the name defined in the configuration file is still shown and can be used for color setting. If a preset has a `​ ​` (space) in it, substitute it with an `_` (underscore) so that it can be used as a command-line parameter value, for example: `OmenMon -Bios Color=Name_with_Spaces`

### Embedded Controller {#ec}

#### EcFailLimit

A [numerical value](#numerical) that defaults to **15**.

Maximum number of failed attempts waiting to read from an Embedded Controller.

#### EcMonInterval

A [numerical value](#numerical) that defaults to **1000**. The unit is milliseconds [ms], so a value of `1000` means an update once per second.

How frequently the Embedded Controller monitoring information is updated. This applies only to the command-line mode `OmenMon -EcMon` context.

#### EcMutexTimeout

A [numerical value](#numerical) that defaults to **200**.

How long before giving up trying to obtain an Embedded Controller lock. The unit is milliseconds [ms].

Only one application can be accessing the Embedded Controller at any given time, otherwise the results might be unpredictable. Before attempting any Embedded Controller operations, the application tries to obtain the `Global\Access_EC` mutually-exclusive lock (mutex). If the lock is being held by another application, it will wait for it to be released but not longer than the value of this setting.

#### EcRetryLimit

A [numerical value](#numerical) that defaults to **3**.

Maximum number of attempts an Embedded Controller read or write operation will be retried.

#### EcWaitLimit

A [numerical value](#numerical) that defaults to **30**.

Maximum number of attempts an Embedded Controller I/O port read operation will be retried.

Each Embedded Controller read or write operation includes a couple of I/O port read operations, so this setting relates to a lower level than [EcRetryLimit](#ecretrylimit).

### Fan Control {#fan}

#### FanCountdownExtendAlways

A [Boolean value](#boolean) that defaults to **false**.

If set to **true**, any non-zero fan countdown will always be continually extended, for as long as the application is running, even with no fan program active, no constant-speed button selected, and the main window hidden, which means any custom fan settings can be made persistent without the need for a fan program.

Please note that this means unsuitable fan settings will never expire, even if you close the main window and forget you are running **OmenMon**. If you enable this, the onus is on you to make sure that the fan settings are appropriate for the situation.

#### FanCountdownExtendInterval

A [numerical value](#numerical) that defaults to **120**. The unit is seconds [s].

This value is used to determine <u>by how much</u> the fan countdown is extended each time.

Custom fan-control settings take effect for a limited duration. As long as a fan program is active or the constant-speed fan mode button is selected in the GUI (even if not necessarily running at a constant-speed setting), before the countdown runs out, this value will be written to the Embedded Controller register to extend that duration so that the fans do not revert to the default settings.

#### FanCountdownExtendThreshold

A [numerical value](#numerical) that defaults to **5**. The unit is seconds [s].

This value is used to calculate <u>when</u> the fan countdown is to be extended.

If the constant-speed fan mode button is selected in the GUI (even if not necessarily running at a constant-speed setting), the countdown will be extended when it reaches less than the value for [UpdateMonitorInterval](#updatemonitorinterval) + [FanCountdownExtendThreshold](#fancountdownextendthreshold).
             
In fan program mode, the equivalent is [UpdateProgramInterval](#updateprograminterval) + [FanCountdownExtendThreshold](#fancountdownextendthreshold). As an example, if a fan program is set to update every 30 seconds, and the threshold value is the default 5 seconds, once the countdown value falls down to 34 seconds, the countdown timer will be reset to the value of [FanCountdownExtendInterval](#fancountdownextendinterval) on the next fan program update.

#### FanLevelMax

A [numerical value](#numerical) that defaults to **55**. The unit is thousands of revolutions per minute [krpm].

Maximum fan level threshold. This setting is used in constant-speed mode for fan speed adjustment using the trackbars.

<img alt="Fan Controls" src="/pic/gui-main-fan.png" width="50%" />

When setting constant fan speed, if both values are set to the maximum, it will be considered equivalent to enabling the [maximum fan speed mode](/cli#fanmax).

#### FanLevelMin

A [numerical value](#numerical) that defaults to **20**. The unit is thousands of revolutions per minute [krpm].

Minimum fan level threshold. This setting is used in constant-speed mode for fan speed adjustment using the trackbars.

When setting constant fan speed, the minimum value will be interpreted as a **0**, i.e. switching the fan off.

#### FanLevelNeedManual

A [Boolean value](#boolean) that defaults to **false**.

If **true**, before setting the fan levels, an attempt will be made to set the _manual_ fan mode first by writing the value of `0x06` to the `OMCC` register. This also applies when running a [fan program](#fan-programs), with `0x00` being written to the `OMCC` register when a fan program is terminated.

Whether this call is necessary is model-specific. With the aim of reducing Embedded Controller workload, this setting is disabled by default. If you find that fan-level setting does not appear to work on your model, try changing this to **true**.

#### FanLevelUseEc

A [Boolean value](#boolean) that defaults to **false**.

By default, **OmenMon** will make a BIOS call to set the fan levels. If this does not work, you can alternatively set this value to **true**, so that the fan levels will be set instead by writing to the Embedded Controller registers directly.

Note that fan-level setting with a BIOS call from the GUI will silently ignore any BIOS errors: this is because it has been reported that on some models, although the BIOS returns an error, the fan-level settings are still applied correctly. You can check if the call returns an error on your specific model by using the [FanLevel](/cli#fanlevel) BIOS setting from the [command line](/cli).

#### FanProgramDefault

A [string value](#string) with no set default.

When [AutoConfig](#autoconfig) is enabled, this indicates the name of the [fan program](#fan-programs) that will be loaded on startup.

When [KeyToggleFanProgram](#keytogglefanprogram) is enabled and [KeyCustomAction](#key) is disabled, this indicates the name of the [fan program](#fan-programs) that will be toggled by pressing the _Omen_ key when the application window is already shown.

The value must exactly match the program name for the setting to take effect.

Just like [FanProgramDefaultAlt](#fanprogramdefaultalt), this setting is not applicable to running a fan program in the [CLI mode](/cli#prog).

#### FanProgramDefaultAlt

A [string value](#string) with no set default.

When [AutoConfig](#autoconfig) is enabled, a [fan program](#fan-programs) is running (whether the [FanProgramDefault](#fanprogramdefault) activated by [AutoConfig](#autoconfig) or otherwise), and the system loses AC power (i.e. switches to battery power, this program will be launched instead of the default one. The system will revert to the default fan program once the AC power is restored.

Just like [FanProgramDefault](#fanprogramdefault), this setting is not applicable to running a fan program in the [CLI mode](/cli#prog).

#### FanProgramModeCheckFirst

A [Boolean value](#boolean) that defaults to **true**.

When **true**, during a fan program, **OmenMon** will first check (using the Embedded Controller) if the desired fan mode is not set already before setting it (using a BIOS WMI call).

If set to **false**, the check will be skipped, which results in one fewer Embedded Controller operation every [UpdateProgramInterval](#updateprograminterval), at the cost of one more WMI BIOS call: this setting can be used to reduce Embedded Controller load in scenarios where it is an issue.

### Fan Programs

Fan program definitions follow the convention:

````xml
<?xml version="1.0" encoding="utf-8"?> 
<OmenMon>
    <Config>
        <FanPrograms>
            <Program Name="High Noise">
                <FanMode>Performance</FanMode>
                <GpuPower>Maximum</GpuPower>
                <Level Temperature="00"><Cpu>40</Cpu><Gpu>42</Gpu></Level>
                <Level Temperature="30"><Cpu>50</Cpu><Gpu>52</Gpu></Level>
                <Level Temperature="40"><Cpu>55</Cpu><Gpu>57</Gpu></Level>
            </Program>
            <Program Name="Silence">
                <FanMode>Default</FanMode>
                <GpuPower>Minimum</GpuPower>
                <Level Temperature="00"><Cpu>00</Cpu><Gpu>00</Gpu></Level>
                <Level Temperature="60"><Cpu>21</Cpu><Gpu>23</Gpu></Level>
                <Level Temperature="70"><Cpu>40</Cpu><Gpu>42</Gpu></Level>
                <Level Temperature="80"><Cpu>50</Cpu><Gpu>52</Gpu></Level>
                <Level Temperature="85"><Cpu>55</Cpu><Gpu>57</Gpu></Level>
            </Program>
        </FanPrograms>
    </Config>
</OmenMon>
````

Where:

* `<FanPrograms>` is the root element all programs are contained within
* `<Program Name="value">` is a container for each fan program entry
* `<FanMode>` is a case-sensitive [string](#string) that matches the [fan performance mode](/cli#fanmode) to be kept as long as the program is active
* `<GpuPower>` is a case-sensitive [string](#string) that matches the [GPU power level](/cli#gpu) to be kept as long as the program is active
* `<Level Temperature="value">`, with `value` being a [number](#numerical), is a container for fan settings at a given temperature level. When the fan program is running, on every update (i.e. at a given interval), it applies the fan settings for the <u>highest</u> level <u>lower</u> than the <u>maximum</u> current temperature at that moment
* Within the `<Level>` container, `<Cpu>` and `<Gpu>` correspond to the [fan level](/cli#fanlevel) settings for each fan respectively

Note that [FanProgramDefault](#fanprogramdefault) identifies the fan program that is started when opening the application and can optionally be toggled on or off with the _Omen_ key, while [FanProgramDefaultAlt](#fanprogramdefaultalt) defines an alternative fan program in a situation when the system loses AC power.

The fan curve of the _Power_ program used in the default configuration file is as follows:

<img alt="Power Fan Program Curve" src="/pic/prog-power.png" width="50%" />

**OmenMon** does not currently offer a visual fan curve editor. Online tools can be used instead, such as [Desmos](https://www.desmos.com/calculator/6vfpghtud0) (the link opens an editable visualization of the above curve).

### GPU {#gpu}

#### GpuPowerDefault

A [string value](#string) with no set default.

When [AutoConfig](#autoconfig) is enabled, this indicates the name of the [GPU Power Level](/cli#gpu) that will be set on startup.

#### GpuPowerSetInterval

A [numerical value](#numerical) that defaults to **200**. The unit is milliseconds [ms].

Sometimes the GPU power settings do not take effect the first time, so the command to change them runs twice, and the second time is only after the specified delay period.

### Graphical User Interface {#gui}

#### GuiCloseWindowExit

A [Boolean value](#boolean) that defaults to **false**.

Whether closing the window closes the whole application -- if **true**, or the application keeps running minimized in the notification area -- if **false**.

The application needs to run in the background for the custom fan programs to work. It can however be launched with the _Omen_ key even when not currently running.

#### GuiDpiChangeResize

A [Boolean value](#boolean) that defaults to **false**.

Whether the application window should be automatically resized in response to DPI changes.

#### GuiDynamicIcon

A [Boolean value](#boolean) that defaults to **false**.

Whether the notification (system tray) area icon is dynamic or not. A dynamic icon shows the maximum current temperature:

![Dynamic Notification Icon with No Background](/pic/gui-notify-icon-bgnone.png)

 A static icon shows a stylized logo:

![Static Notification Icon](/pic/gui-notify-icon-static.png)

If the dynamic icon is enabled, temperature sensors will be queried every [UpdateIconInterval](#updateiconinterval) seconds. If the dynamic icon is disabled, these Embedded Controller operations do not take place, which might be preferable in some scenarios.

Temperature text is rendered dynamically but the additional system load due to this should be negligible.

This value can be changed with the GUI context menu setting: [Settings](/gui#menu-settings) → [Dynamic Icon](/gui#dynamic-icon)

#### GuiDynamicIconHasBackground

A [Boolean value](#boolean) that defaults to **false**.

Whether the dynamic icon, if enabled with [GuiDynamicIcon](#guidynamicicon), has a dynamic background or not. Dynamic background is a <span style="background-image: linear-gradient(90deg, #ff0802, #ac02ff); color: white; font-weight: bold; padding: 0em 0.2em;">warm</span> gradient in _Performance_ [mode](/cli#fanmode):

![Dynamic Notification Icon with Warm Background](/pic/gui-notify-icon-bgwarm.png)

Or a <span style="background-image: linear-gradient(0deg, #8804ff, #03ef9b); color: white; font-weight: bold; padding: 0em 0.2em;">cool</span> gradient otherwise:

![Dynamic Notification Icon with Cool Background](/pic/gui-notify-icon-bgcool.png)

Since it appears the hardware has a tendency to sometimes drop out of the _Performance_ mode on its own, it might be useful to periodically check this if you are aiming for maximum performance. Note however that a fan program will also maintain the set mode automatically, so this is just for the informational value.

This adds an extra Embedded Controller query every [UpdateIconInterval](#updateiconinterval) seconds and some additional load, which should still be negligible: the background image is cached, and only redrawn when switching to or from the _Performance_ [mode](/cli#fanmode).

This value can be changed with the GUI context menu setting: [Settings](/gui#menu-settings) → [Dynamic Background](/gui#dynamic-background)

If [GuiDynamicIcon](#guidynamicicon) is set to **false**, the value of this setting is irrelevant.

#### GuiStayOnTop

A [Boolean value](#boolean) that defaults to **false**.

Whether the application window stays on top of all other windows or not.

This value can be changed with the GUI context menu setting: _Settings_ → _Stay on Top_

#### GuiSysInfoFontSize

A [numerical value](#numerical) that defaults to **0**. The unit is pixels [px].

Override the default font size for the _System Information & Status_ area. This is mainly intended as a workaround for the situation when the text does not fit in the box due to the line height being larger on systems where the default regional settings are set to a non-alphabetic language such as Chinese. In such cases, **14** seems to be a good value; on systems using the Latin alphabet by default, **17** is the maximum size that fits into the box.

Setting this to **0** leaves the font size at its automatic default.

#### GuiTipDuration

A [numerical value](#numerical) that defaults to **30000**. The unit is milliseconds [ms].

How long to show a balloon tip in the notification area. The default setting of 30000, or 30 seconds, is automatically scaled down by the operating system to the highest allowed value, which is more in the order of 10 seconds or so.

These notifications can be annoying, so the feature is used sparingly: the only situation when you can currently see such a notification is when the application is already running in the background and an attempt was made by the user to run another instance of it (unless as a result of pressing the _Omen_ key):

<img alt="Balloon Notification Tip" src="/pic/gui-notify-tip-balloon.png" width="50%" />

A facility is implemented to relay important [fan program](/gui#fan_program) messages (such as potential overheating) as balloon tips but it is not being used for anything so far.

Setting this to **0** disables this type of notification entirely.

### Key Custom Action {#key}

The entry for _Omen_ key custom action is defined as follows:

````xml
<?xml version="1.0" encoding="utf-8"?> 
<OmenMon>
    <Config>
        <KeyCustomAction>
            <Enabled>false</Enabled>
            <!-- This example command will turn off the display
                 and keyboard backlight while the laptop keeps running -->
            <ExecCmd>cmd.exe</ExecCmd>
            <ExecArgs>/c start /min "" powershell -WindowStyle Hidden (Add-Type '[DllImport(\"user32.dll\")] public static extern int SendMessage(int hWnd, int hMsg, int wParam, int lParam);' -Name User32 -PassThru)::SendMessage(-1, 0x0112, 0xF170, 2) ^| Out-Null</ExecArgs>
            <Minimized>true</Minimized>
        </KeyCustomAction>
    </Config>
</OmenMon>
````

Where:
* `<KeyCustomAction>` is the container for the custom-action settings
* `<Enabled>` is a [Boolean](#boolean) flag: if **true**, pressing the _Omen_ key will execute the specified custom action; if **false**, the standard action is to show the main application window if not being shown already, or otherwise either hide the window or toggle the default fan program on or off depending on the [KeyToggleFanProgram](#keytogglefanprogram) setting
* `<ExecCmd>` is a [string](#string) specifying the command to be executed
* `<ExecArgs>` is a [string](#string) with arguments to the command

Note that for this to take effect, the _Omen_ key interception task needs to be enabled:
* `OmenMon -Task Key=On` in CLI mode
* _Settings_ → _Intercept Omen Key_ in GUI mode

Also note that the command will be executed **with administrative rights**.

The convoluted example above uses the _PowerShell_ launched from the _Command Prompt_ to perform the equivalent of clicking the GUI context menu item: _Graphics_ → _Set Display Off_ or the following code snippet from `Library\Os.cs`:

````csharp
// Sets the display to standby
public static void SetDisplayOff() {

    // Send a system command message
    User32.SendMessage(
        (IntPtr) User32.HWND_BROADCAST,
        User32.WM_SYSCOMMAND,
        (IntPtr) User32.SC_MONITORPOWER,
        (IntPtr) User32.MONITORPOWER.STANDBY);

}
````

#### KeyToggleFanProgram

A [Boolean value](#boolean) that defaults to **false**.

If [Key Custom Action](#key) is not enabled and the application window is already shown on screen,
subsequent _Omen_ key presses toggle the [FanProgramDefault](#fanprogramdefault) on and off.

### Preset Settings {#preset}

#### PresetRefreshRateHigh

A [numerical value](#numerical) that defaults to **165**. The unit is times per second or Hertz [Hz].

Preset value to be used for [Graphics](/gui#menu-graphics) → [High Refresh Rate](/gui#refresh-rate).

#### PresetRefreshRateLow

A [numerical value](#numerical) that defaults to **60**. The unit is times per second or Hertz [Hz].

Preset value to be used for [Graphics](/gui#menu-graphics) → [Standard Refresh Rate](/gui#refresh-rate).

### Update Intervals {#update}

#### UpdateIconInterval

A [numerical value](#numerical) that defaults to **3**. The unit is seconds [s].

How frequently the dynamic notification icon is updated.

If [GuiDynamicIcon](#guidynamicicon) is disabled, this setting is irrelevant.

#### UpdateMonitorInterval

A [numerical value](#numerical) that defaults to **3**. The unit is seconds [s].

How often the monitoring data on the main form is updated.

If the main form is hidden (not visible even as a taskbar icon), this setting is irrelevant.

#### UpdateProgramInterval

A [numerical value](#numerical) that defaults to **15**. The unit is seconds [s].

How often the fan program is updated.

If no fan program is running, this setting is irrelevant.

### Message Section {#messages}

The application is ready to be translated into any language. The format for localizable message entries follows the convention:

````xml
<?xml version="1.0" encoding="utf-8"?> 
<OmenMon>
    <Messages>
        <String Key="CliTranslated">Translated to [Language] by [Author]</String>
        <String Key="GuiTranslated">Translated by [Author]</String>
    </Messages>
</OmenMon>
````

Both keys and values here are [strings](#string). Message identifiers can be looked up in the source for `Library\LocaleData.cs`, where the defaults are also stored. Only the values that the user wants overriden have to be specified.

The two entries shown above are a special case: by default, they are **empty**. Setting them to any other value will add a translator credit to the header printed out whenever the program is run in CLI mode and to the top of the context-menu in the GUI mode.

## Sample Configuration {#example}

An extensively-annotated sample configuration file is distributed with the application. It is also provided below for reference, with the difference that the version here also sets [FanProgramDefault](#fanprogramdefault) to `Power` and enables [KeyToggleFanProgram](#keytogglefanprogram).

````xml
<?xml version="1.0" encoding="utf-8"?> 
<OmenMon>

    <!--

      //\\   OmenMon: Hardware Monitoring & Control Utility
     //  \\  Configuration Settings XML File
         //  https://omenmon.github.io/

    -->

    <!-- Note: comments under child nodes such as <ColorPresets> and <FanPrograms>
         will be overwritten when the file is automatically generated upon save -->

    <Config>

        <!-- Automatically apply settings on start -->
        <AutoConfig>true</AutoConfig>

        <!-- Automatically start up with Windows -->
        <AutoStartup>true</AutoStartup>

        <!-- Ignore BIOS errors if false (for not fully compatible devices) -->
        <BiosErrorReporting>true</BiosErrorReporting>

        <!-- Color Backlight -->

        <!-- Note: two default entries are hard-coded in the application
             and will appear if there is nothing else to show instead -->
        <ColorPresets>
            <Preset Name="DefaultOem">0F84FA:710FFA:F9350F:FAAC0F</Preset>
            <Preset Name="DefaultApp">0080FF:00FF00:00FF00:FFFFFF</Preset>
            <Preset Name="All Amber">FF8800:FF8800:FF8800:FF8800</Preset>
            <Preset Name="All White">FFFFFF:FFFFFF:FFFFFF:FFFFFF</Preset>
            <Preset Name="Cyan Magenta Yellow">FFFF00:FF0080:3AE5E7:FFFF00</Preset>
            <Preset Name="Red Green Blue">0000FF:00FF00:FF0000:FFFFFF</Preset>
        </ColorPresets>

        <!-- Embedded Controller -->

        <!-- Maximum number of failed attempts waiting to read -->
        <EcFailLimit>15</EcFailLimit>

        <!-- Embedded Controller monitoring interval [ms]
             (applies to command-line mode -EcMon context) -->
        <EcMonInterval>1000</EcMonInterval>

        <!-- How long before bailing out trying to get a mutex [ms] -->
        <EcMutexTimeout>200</EcMutexTimeout>

        <!-- Maximum number of read or write attempts -->
        <EcRetryLimit>3</EcRetryLimit>

        <!-- Iterations before waiting fails each time -->
        <EcWaitLimit>30</EcWaitLimit>

        <!-- Fan Control -->

        <!-- Fan countdown will always be continually extended, even
             with no fan program running, no constant-speed button
             selected, and the main window hidden, until exit -->
        <FanCountdownExtendAlways>false</FanCountdownExtendAlways>

        <!-- Fan countdown timer will be extended by this value [s] -->
        <FanCountdownExtendInterval>120</FanCountdownExtendInterval>

        <!-- Fan countdown extension threshold [s]
             If the constant-speed button is selected (even if not
             necessarily running at a constant-speed setting), the
             fan countdown value will be extended when it reaches
             less than the value for:
             UpdateMonitorInterval + FanCountdownExtendThreshold
             In fan program mode, the threshold is:
             UpdateProgramInterval + FanCountdownExtendThreshold -->
        <FanCountdownExtendThreshold>5</FanCountdownExtendThreshold>

        <!-- Minimum and maximum fan level thresholds [krpm]
             (for trackbar constant-speed level adjustment,
             lowest setting will be interpreted as 0) -->
        <FanLevelMax>55</FanLevelMax>
        <FanLevelMin>20</FanLevelMin>

        <!-- Before setting fan levels, set manual
             fan mode first using the Embedded Controller -->
        <FanLevelNeedManual>false</FanLevelNeedManual>

        <!-- Use the Embedded Controller instead
             of a BIOS call for fan-level setting -->
        <FanLevelUseEc>false</FanLevelUseEc>

        <!-- Default fan program, which might be loaded on startup
             (depending on the Autoconfig setting) -->
        <FanProgramDefault>Power</FanProgramDefault>

        <!-- Default alternate fan program to switch to
             if no longer on AC power (i.e. on battery) -->
        <FanProgramDefaultAlt>Silent</FanProgramDefaultAlt>

        <!-- Check first (using the EC) if the fan mode is not set already
             before setting it (using a BIOS WMI call) during a fan program
             (if false, makes one EC operation less every UpdateProgramInterval,
             at the cost of one more WMI BIOS call: can be used to reduce EC load) -->
        <FanProgramModeCheckFirst>true</FanProgramModeCheckFirst>

        <!-- Fan program definitions
             Curve visualization: https://www.desmos.com/calculator/6vfpghtud0
                                  (editable initial data for the Power program) -->
        <FanPrograms>
            <Program Name="Power">
                <FanMode>Performance</FanMode>
                <GpuPower>Maximum</GpuPower>
                <Level Temperature="00"><Cpu>00</Cpu><Gpu>00</Gpu></Level>
                <Level Temperature="36"><Cpu>21</Cpu><Gpu>00</Gpu></Level>
                <Level Temperature="39"><Cpu>22</Cpu><Gpu>22</Gpu></Level>
                <Level Temperature="42"><Cpu>23</Cpu><Gpu>24</Gpu></Level>
                <Level Temperature="45"><Cpu>24</Cpu><Gpu>26</Gpu></Level>
                <Level Temperature="48"><Cpu>25</Cpu><Gpu>27</Gpu></Level>
                <Level Temperature="51"><Cpu>26</Cpu><Gpu>29</Gpu></Level>
                <Level Temperature="54"><Cpu>28</Cpu><Gpu>31</Gpu></Level>
                <Level Temperature="57"><Cpu>30</Cpu><Gpu>33</Gpu></Level>
                <Level Temperature="60"><Cpu>32</Cpu><Gpu>35</Gpu></Level>
                <Level Temperature="63"><Cpu>34</Cpu><Gpu>37</Gpu></Level>
                <Level Temperature="66"><Cpu>36</Cpu><Gpu>40</Gpu></Level>
                <Level Temperature="69"><Cpu>38</Cpu><Gpu>43</Gpu></Level>
                <Level Temperature="72"><Cpu>41</Cpu><Gpu>46</Gpu></Level>
                <Level Temperature="75"><Cpu>44</Cpu><Gpu>49</Gpu></Level>
                <Level Temperature="78"><Cpu>47</Cpu><Gpu>52</Gpu></Level>
                <Level Temperature="81"><Cpu>50</Cpu><Gpu>55</Gpu></Level>
                <Level Temperature="84"><Cpu>55</Cpu><Gpu>57</Gpu></Level>
            </Program>
            <Program Name="Silent">
                <FanMode>Default</FanMode>
                <GpuPower>Minimum</GpuPower>
                <Level Temperature="00"><Cpu>00</Cpu><Gpu>00</Gpu></Level>
                <Level Temperature="50"><Cpu>21</Cpu><Gpu>00</Gpu></Level>
                <Level Temperature="55"><Cpu>25</Cpu><Gpu>25</Gpu></Level>
                <Level Temperature="60"><Cpu>30</Cpu><Gpu>30</Gpu></Level>
                <Level Temperature="65"><Cpu>35</Cpu><Gpu>35</Gpu></Level>
                <Level Temperature="70"><Cpu>40</Cpu><Gpu>40</Gpu></Level>
                <Level Temperature="75"><Cpu>45</Cpu><Gpu>45</Gpu></Level>
                <Level Temperature="80"><Cpu>50</Cpu><Gpu>50</Gpu></Level>
                <Level Temperature="85"><Cpu>55</Cpu><Gpu>57</Gpu></Level>
            </Program>
        </FanPrograms>

        <!-- GPU -->

        <!-- Default GPU power settings, which might be loaded on startup
             (depending on the Autoconfig setting) -->
        <GpuPowerDefault>Maximum</GpuPowerDefault>

        <!-- The wait before setting the GPU power for the 2nd time [ms]
             (sometimes the settings do not take effect the first time,
             so the command is sent twice but the second time only after
             the specified period has passed) -->
        <GpuPowerSetInterval>200</GpuPowerSetInterval>

        <!-- Graphical User Interface -->

        <!-- Whether closing the window closes the whole application -->
        <GuiCloseWindowExit>false</GuiCloseWindowExit>

        <!-- Whether to resize the main window if DPI changes -->
        <GuiDpiChangeResize>false</GuiDpiChangeResize>

        <!-- Whether to use a dynamic notification icon by default
             (icon text shows current temperature) -->
        <GuiDynamicIcon>true</GuiDynamicIcon>

        <!-- Whether the dynamic icon has a dynamic background or not
             (background is warm in performance mode, cool otherwise) -->
        <GuiDynamicIconHasBackground>true</GuiDynamicIconHasBackground>

        <!-- Whether the main window stays on top of all other windows -->
        <GuiStayOnTop>false</GuiStayOnTop>

        <!-- Override System Information font size (leave 0 for the default) -->
        <GuiSysInfoFontSize>0</GuiSysInfoFontSize>

        <!-- How long to show a tip in the notification area for [ms]
             (0 to disable entirely; the default setting of 30000, or 30 s,
              is scaled down to a couple of seconds by the operating system) -->
        <GuiTipDuration>30000</GuiTipDuration>

        <!-- Omen Key -->

        <!-- Custom Omen key action -->
        <KeyCustomAction>
            <Enabled>false</Enabled>
            <!-- This example command will turn off the display
                 and keyboard backlight while the laptop keeps running -->
            <ExecCmd>cmd.exe</ExecCmd>
            <ExecArgs>/c start /min "" powershell -WindowStyle Hidden (Add-Type '[DllImport(\"user32.dll\")] public static extern int SendMessage(int hWnd, int hMsg, int wParam, int lParam);' -Name User32 -PassThru)::SendMessage(-1, 0x0112, 0xF170, 2) ^| Out-Null</ExecArgs>
            <Minimized>true</Minimized>
        </KeyCustomAction>

        <!-- If the application window is already shown on screen,
             and custom action is disabled, subsequent Omen key
             presses toggle the default fan program on and off -->
        <KeyToggleFanProgram>true</KeyToggleFanProgram>

        <!-- Preset Settings -->

        <!-- High display refresh rate preset value [Hz] -->
        <PresetRefreshRateHigh>165</PresetRefreshRateHigh>

        <!-- Standard display refresh rate preset value [Hz] -->
        <PresetRefreshRateLow>60</PresetRefreshRateLow>

        <!-- Update Interval -->

        <!-- How often the dynamic notification icon is updated [s] -->
        <UpdateIconInterval>3</UpdateIconInterval>

        <!-- How often the monitoring data on the main form is updated [s] -->
        <UpdateMonitorInterval>3</UpdateMonitorInterval>

        <!-- How often the fan program is updated (if running) [s] -->
        <UpdateProgramInterval>15</UpdateProgramInterval>

    </Config>

    <!-- Localizable Messages -->

    <!-- Any of the application's messages can be redefined,
         for example translated to another language -->

    <Messages>

        <!-- The following two strings will optionally show translator credit
             (these are empty by default, and nothing is shown) -->

        <!-- <String Key="CliTranslated">Translated to [Language] by [Author]</String> -->
        <!-- <String Key="GuiTranslated">Translated by [Author]</String> -->
    </Messages>

</OmenMon>
````
