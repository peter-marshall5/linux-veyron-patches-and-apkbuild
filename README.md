# Patches, configuration, and APKBUILD for mainline Linux on Asus C201

This repo comes with the patches, APKBUILD, and kernel configuration that I'm using to build mainline Linux for the Asus C201.

What's currently working:

Feature | Status
--- | ---
Internal Storage | ✔️ (\*)
Display | ✔️
Backlight Control | ✔️
HDMI output | ✔️ (\*)
USB 2.0 | ✔️
Keyboard | ✔️
Touchpad | ✔️
Battery Monitoring | ✔️
Internal Speakers | ✔️
Headphone Output | ✔️
Headphone Jack Detection | ✔️
Headset Microphone | ✔️
Internal Microphone | ✖️ (1)
Wifi | ✔️
Wifi Hotspot | ✔️
Wifi Monitor Mode | ✖️
Bluetooth | ✔️ (\*)
Charger Detection | ✔️
Lid Switch | ✔️
CPU, GPU, and battery thermals | ✔️
Suspend | ✔️
Shutdown | ✔️
Rebooting | ~ (2)
Real Time Clock | ✔️
BMQ scheduler | ✔️
LRNG framework | ✔️
Clang Thin LTO | ✔️ (*)

(1): This is likely due to the ALSA configuration and UCM that I'm using.
(2): Reboots to recovery mode only, probably an easy fix.

(*): Mainline feature that works with patches

# Overclocking

What's currently working:

Feature | Status
--- | ---
Display Overclocking via DT | ✔️
CPU overclocking via DT | ✔️
GPU overclocking | ✖️
