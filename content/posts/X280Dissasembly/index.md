---
title : 'Disassemble Thinkpad X280'
date : 2026-04-05T22:57:55-05:00
tags: ["dissasembly",'thinkpad','x280', 'easy']
summary: "Tutorial on how to disassemble a Thinkpad x280 ft. BIOS chip location."
---

This post is honestly kinda unnecessary, since I should just send you off to watch a youtube video 
(which I don't think is necessary either, because this machine is super easy work with), but it helps me
show the exact spot of the bios chip (and saves you from having to dig through a Russian forum like I had to XD.)

Anyway, here's a disassembly video for my visual learners:

{{< youtube XB3tJnmhmHw >}}

## Remove bottom cover.

Just as a reminder. Make sure the machine is completely turned off.

Now we take out the 5 visible screws on the bottom cover. Honestly, I'm not sure what screwdriver size you should
use, but whatever fits and let you remove them works.

![x280bottom.jpg](/imgs/x280libreboot/x280bottom.jpg)
 
## Remove main battery.

With the machine open, it's essential to disconnect any power source from the system, just like the
[coreboot documentation](https://doc.coreboot.org/mainboard/lenovo/skylake.html#:~:text=Disconnect/remove%20all%20batteries%20(and%20CMOS%20battery%20if%20equipped).) 
says.

To remove the battery, take out the 5 screws holding it in place. You can use the same screwdriver you used to take off the bottom cover.
![x280pcb.jpg](/imgs/x280libreboot/x280pcb.jpg)

Now pull it gently towards you and that's it
![x280battery.jpg](/imgs/x280libreboot/x280battery.jpg)

## Remove CMOS battery.

{{< alert >}}
**Warning!** Don't forget to unplug the CMOS battery just because it's not shown in the video.
{{< /alert >}}


To remove the CMOS battery you need to take out the main battery first. Now just pull the connector and you're good to go.

![x280cmos.jpg](/imgs/x280libreboot/x280cmos.jpg)
