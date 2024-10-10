---
layout: post
title:  "MacBook Pro (2023) external screen resolution issue"
date:   2024-08-20 10:04:08 +0100
---

# Problem

I use a single wide-screen 4k monitor for my home office setup. My laptop lid remains closed and the laptop is plugged into a docking station the entire time.

However, after upgrading my MacBook to a 2023 MacBook Pro (Apple M2 Pro chip) I was unable to use my Dell S3422DW monitor, it was displaying the following error on the screen:

> The current input timing is not supported by the monitor display. Change your input timing to 3440x1440, 60Hz.

With the laptop lid open I was able to change the Display settings using the laptop screen on the laptop and change the refresh rate to **50Hz** after which the display started working. However when I closed the lid of the laptop it would revert back to the **60Hz** setting and the external monitor would again not display.

# Solution

To work around this issue I installed the [screenresolution](https://github.com/jhford/screenresolution) app e.g.:

```bash
brew install screenresolution
```

Then I was able to issue the following command before closing the lid and the display was set correctly:

```bash
sleep 6; screenresolution set 3440x1440x32@50
```

This setting survives across reboots and the issue is resolved.
