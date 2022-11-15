# Patches, configuration, and APKBUILD for mainline Linux on Asus C201

This repo comes with the patches, APKBUILD, and kernel configuration that I'm using to build mainline Linux for the Asus C201.

What's currently working:

Feature | Status
--- | ---
Internal Storage | ✔️ (\*)
Display | ✔️
GPU acceleration via Panfrost | ✔️
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
Internal Microphone | ✔️ (1)
Wifi (Client, AP, Ad-Hoc) | ✔️
Wifi Monitor Mode | ✖️ (2)
Bluetooth | ✔️ (\*)
Charger Detection | ✔️
Lid Switch | ✔️
CPU, GPU, and battery thermals | ✔️
Suspend | ✔️
Shutdown | ✔️
Rebooting | ✔️
Real Time Clock | ✔️
BMQ scheduler | ✔️
LRNG framework | ✔️
Clang Thin LTO | ✔️ (*)

(1): Requires switching the audio profile to "Off" and back after using the headset microphone. Very likely a UCM issue.

(2): Likely unsupported by chipset (BCM4354).

(\*): Mainline feature that works with patches

# Overclocking

What's currently working:

Feature | Status
--- | ---
Display Overclocking via DT | ✔️
CPU overclocking via DT | ✔️
GPU overclocking | ✖️
