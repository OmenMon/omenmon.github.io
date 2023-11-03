---
title: OmenMon
---

# Introducing OmenMon

![OmenMon graphical mode overview](/pic/gui-overview.png)

**OmenMon** is a lightweight application that interacts with the Embedded Controller (EC) and WMI BIOS routines of an _HP Omen_ laptop in order to access hardware settings, in particular to [query temperature sensors](/gui#temperature) and dynamically [adjust fan speeds](/gui#fan-control). It also helps you pick your favorite [keyboard backlight colors](/gui#keyboard) and put the [_Omen_ key](/config#key) to better use.

**OmenMon** endeavors to replace all the useful functionality of the _Omen Hub_ (a.k.a. _Omen Control Center_), the laptop manufacturer's application, without any of its numerous anti-features. It does not connect to the network at all, does not have advertising, built-in store, social-media integration and whatnot. It does only what you expect it to do and nothing else.

**OmenMon** is designed to run with minimal resource overhead. It comes with a clear and compact [graphical interface](/gui), offering a great degree of [configurability](/config) while also featuring an extensive [command-line mode](/cli) where various BIOS and EC read and write operations can be performed manually. 

Most features are specific to _HP_ devices with a compatible BIOS interface exposed by the `ACPI\PNP0C14` driver but command-line [Embedded Controller operations](/cli#ec) should work on all laptops.

## OmenMon vs the Goliath

| Feature                 | Omen Hub |  OmenMon | Comparison       |
|:------------------------|:--------:|:--------:|:-----------------|
| Disk size               | ~ 400 MB | ~ 400 kB | ~ 1000 × Smaller |
| Memory footprint        | ~ 1.4 GB | ~ 14 MB  | ~ 100 × Smaller  |
| Loading time            | > 10 s   | < 1 s    | > 10 × Faster    |
| Change backlight color  | ✓        | ✓        | Tie              |
| Control the fans        | ✓        | ✓        | Tie              |
| Unlock full performance | ✓        | ✓        | Tie              |
| Use the _Omen_ key      | ✓        | ✓        | Tie              |
| Advertisements included | ✓        | ×        | Win              |
| User data collection    | ✓        | ×        | Win              |
| Built-in social media   | ✓        | ×        | Win              |
| Built-in store          | ✓        | ×        | Win              |
| Cost to buy             | 0*       | 0        | Tie              |

<sub>*) Not actually free but paid for with the purchase price of the laptop</sub>

### Disclaimer

**OmenMon** is not affiliated with or endorsed by _HP_. Any brand names are used for informational purposes only. The software is provided as-is and comes with no warranty. The user solely assumes all responsibility arising from its use.
