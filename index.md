---
disclaimer: Disclaimer Override Test
---

# OmenMon

## Coming Soon

### Header 3

#### Header 4

##### Header 5

* List Item
  * List Sub-Item

* * *

> Blockquote

| Table | Header |
|------:|:-------|
|   1   |    4   |
|   2   |    5   |
|   3   |    6   |


![Favorites Icon](https://omenmon.github.io/favicon.png)

**Bold** `Code` _Italic_ [Link](https://github.com/OmenMon) ~~Strikethrough~~

```csharp
// Handle identifying all windows for message broadcast purposes
public const int HWND_BROADCAST = 0xFFFF;

// Broadcasts a specific message
public static bool BroadcastMessage(uint msg, MessageParam param = MessageParam.Default) {
    IntPtr lParam = (IntPtr) param;
    return User32.PostMessage(
        (IntPtr) User32.HWND_BROADCAST,  // Send to all top-most windows
        msg,                             // The message identifier registered beforehand
        (IntPtr) Config.AppProcessId,    // Add a semi-unique identifier to sieve out duplicates
        lParam);                         // Used to distinguish message types
}
````
