# TFT35V1.0-Klipper-screen
In the process of writing/editing.

Below is the installation instructions for Raspberry 3B, Raspbian Bullseye Legacy Lite 64bit
At the time of installation and writing the instructions, version: Linux raspberrypi 5.15.32-v8+ #1538 SMP PREEMPT Thu Mar 31 19:40:39 BST 2022 aarch64 GNU/Linux
Why exactly bullseye? Yes, because they don’t want to install nimpy on the Buaster, which are necessary for the ADXL345 accelerometer to work in the clipper. Focal did not try. If I check, I'll post.

At this stage, there are 3 options:
1) You have nothing installed, then go to the "Installing from scratch" item.
2) You already have klipper running, no screen (no Klipperscreen). Then follow the instructions, just install the Klipperscreen.
3) You already have a Klipperscreen (for example, HDMI), and you want to connect your native 3.5 inch screen. - Let's move on to setting up the TFT35