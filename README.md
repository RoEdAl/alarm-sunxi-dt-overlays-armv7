# Arch Linux ARM - Device Tree overlays for sunxi devices (32-bit)

My try to bring idea of additional [DT overlays](http://github.com/armbian/sunxi-DT-overlays) from Armbian to Arch Linux.
Requires bootloader ready to apply DT overlays - see [this](http://github.com/RoEdAl/alarm-sun8i-h3) repository.

Currently only `sun8i-h3-...` DT overlays are supported/packaged.
User provided DT overlays are not supported.

The idea is very simple.
To enable, say additional USB port just install `sun8i-h3-dto-usbport2` package (for OrangePi Zero) and reboot.
If you want audio codec just install `sun8i-h3-dto-analog-codec` package and reboot etc.
The `/boot/overlays.txt` config file is updated by simple post-install hook (shell script).

See [boot.txt](http://github.com/RoEdAl/alarm-sun8i-h3/blob/master/uboot/boot.txt) U-Boot script.

All `sun8i-h3-dto-...` packages requires `sunxi-dt-overlays-toolkit` and `sun8i-h3-dt-overlays` ones.
