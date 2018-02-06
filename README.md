# Arch Linux ARM - Device Tree overlays for sunxi devices (32-bit)

My try to bring idea of additional [DT overlays](//github.com/armbian/sunxi-DT-overlays) from Armbian to Arch Linux.
Requires bootloader ready to apply DT overlays - see [this](//github.com/RoEdAl/alarm-uboot-sunxi-armv7) repository.

The idea is very simple.
To enable, say additional USB port just install `sun8i-h3-dto-usbport2` package (for OrangePi Zero) and reboot.
If you want audio codec just install `sun8i-h3-dto-analog-codec` package and reboot etc.
The `/boot/overlays.txt` config file is updated by simple post-install hook (shell script).

See [boot.txt](//github.com/RoEdAl/alarm-sun8i-h3/blob/master/uboot/boot.txt) U-Boot script.

## Supported SoCs:

* H2+/H3 - `sun8i-h3`
* A10 - `sun4i-a10`
* A20 - `sun7i-a20`

---

* All `sun8i-h3-dto-...` packages requires `sunxi-dt-overlays-toolkit` and `sun8i-h3-dt-overlays` ones.
* All `sun4i-a10-dto-...` packages requires `sunxi-dt-overlays-toolkit` and `sun4i-a10-dt-overlays` ones.
* All `sun7i-a20-dto-...` packages requires `sunxi-dt-overlays-toolkit` and `sun7i-a20-dt-overlays` ones.

