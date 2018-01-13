#
# Device Tree overlays for sunxi devices
#

_dto_packages=(
	'analog-codec'
	'cir'
	'usbhost0'
	'usbhost1'
	'usbhost2'
	'usbhost3'
	'i2c0'
	'i2c1'
	'i2c2'
	'uart1'
	'uart2'
	'uart3'
	'pwm'
	'pps-gpio'
	'w1-gpio'
	'spdif-out'
	'spi-add-cs1'
	'spi-jedec-nor'
	'spi-spidev'
)

_dto_packages_desc=(
	'analog codec'
	'CIR receiver'
	'USB host controler 0'
	'USB host controler 1'
	'USB host controler 2'
	'USB host controler 3'
	'TWI/I2C bus 0'
	'TWI/I2C bus 1'
	'TWI/I2C bus 2'
	'serial port 1'
	'serial port 2'
	'serial port 3'
	'hardware PWM controller'
	'pulse-per-second GPIO client'
	'1-Wire GPIO master'
	'SPDiF/Toslink audio output'
	'SPI chip select 1 with GPIO'
	'MTD support for JEDEC compatible SPI NOR flash'
	'SPIdev device node (/dev/spidevX.Y) for userspace SPI access'
)

pkgbase='sunxi-dt-overlays'
pkgname=(
	'sunxi-dt-overlays-toolkit'
	'sun8i-h3-dt-overlays'
	"${_dto_packages[@]/#/sun8i-h3-dto-}")
pkgdesc='Device Tree overlays for sunxi devices'
pkgver=r54.4dfd904
pkgrel=1
arch=('armv7h')
_linux_kernel_pkg='linux-armv7-rc'
url='http://github.com/armbian/sunxi-DT-overlays'
license=('GPL2')
makedepends=('git' 'dtc' 'uboot-tools')
options=('!strip')

source=('sunxi-dto::git+https://github.com/armbian/sunxi-DT-overlays.git'
	'upd-sunxi-dt-overlays.sh'
	'overlays.txt'
)
md5sums=('SKIP'
         '5e9488d2ea6406e836093f9218f69f88'
         'd830ed89e377a18f426b81b287bc15bd')

pkgver() {
  cd "${srcdir}/sunxi-dto"
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

_build_dtbo() {
  local PFX=$1
  shift
  local DESC=$1
  shift

  for DTS; do
    msg2 "DTS[${PFX}]: ${DTS}"
    dtc -@ -Wno-unit_address_vs_reg -Wno-gpios_property -I dts -O dtb -o "${PFX}/${PFX}-${DTS}.dtbo" "${PFX}/${PFX}-${DTS}.dts"
  done

  mkimage -A arm -O linux -T script -C none -n "${DESC} fixup" -d "${PFX}/${PFX}-fixup.scr-cmd" "${PFX}/${PFX}-fixup.scr"
}

build() {
  cd "${srcdir}/sunxi-dto"

  _build_dtbo 'sun8i-h3' 'Allwinner H2+/H3' "${_dto_packages[@]}"
}

package_sunxi-dt-overlays-toolkit() {
  pkgdesc='DT overlays toolkit for SunXi SoCs'

  install -Dm755 "${srcdir}/upd-sunxi-dt-overlays.sh" "${pkgdir}/usr/share/sunxi-dt-overlays/upd-sunxi-dt-overlays.sh"
}

# hook template
_create_ovl_hook() {
  local OVL_PREFIX=$1
  local SOC_NAME=$2
  local BASE_DIR=$3

  (cat <<EOF
[Trigger]
Type = File
Operation = Install
Operation = Remove
Target = boot/dtbs/overlay/${OVL_PREFIX}-*.dtbo
Target = etc/conf.d/${OVL_PREFIX}

[Action]
Description = Updating ${SOC_NAME} DT overlays
Depends = ${OVL_PREFIX}-dt-overlays
When = PostTransaction
Exec = /usr/bin/upd-${OVL_PREFIX}-dt-overlays
EOF
  ) >> "${BASE_DIR}/usr/share/libalpm/hooks/98-upd-${OVL_PREFIX}-dt-overlays.hook"
}

_get_dto_packages_with_desc() {
  for (( i = 0 ; i < ${#_dto_packages[@]} ; i++ ))
  do
    echo "sun8i-h3-dto-${_dto_packages[$i]}: ${_dto_packages_desc[$i]}"
  done
}

package_sun8i-h3-dt-overlays() {
  pkgdesc='DT overlays for Allwinner H2+/H3 SoC'
  depends=($_linux_kernel_pkg 'sunxi-dt-overlays-toolkit')

  OLD_IFS=${IFS}
  IFS=$'\n'
  local _optdepends=( $(_get_dto_packages_with_desc) )
  IFS=${OLD_IFS}
  optdepends=( "${_optdepends[@]}" )

  groups=('sun8i-h3-dto')
  backup=('boot/overlays.txt')
  provides=('sunxi-dt-overlays')
  conflicts=('sunxi-dt-overlays')
 
  install -dm755 ${pkgdir}/usr/bin
  ln -s /usr/share/sunxi-dt-overlays/upd-sunxi-dt-overlays.sh ${pkgdir}/usr/bin/upd-sun8i-h3-dt-overlays

  install -dm755 "${pkgdir}/usr/share/libalpm/hooks"
  _create_ovl_hook 'sun8i-h3' 'Allwinner H2+/H3' "${pkgdir}"
  install -Dm644 "${srcdir}/overlays.txt" "${pkgdir}/boot/overlays.txt"

  cd "${srcdir}/sunxi-dto"
  install -Dm644 'sun8i-h3/sun8i-h3-fixup.scr' "${pkgdir}/boot/dtbs/allwinner/overlay/sun8i-h3-fixup.scr"
}

_install_dtbo() {
  local PFX=$1
  shift

  for DTBO; do
    install -Dm644 "${srcdir}/sunxi-dto/${PFX}/${PFX}-${DTBO}.dtbo" "${pkgdir}/boot/dtbs/overlay/${PFX}-${DTBO}.dtbo"
  done
}

_get_package_desc() {
  for (( i = 0 ; i < ${#_dto_packages[@]} ; i++ ))
  do
    if [[ ${_dto_packages[$i]} == $1 ]]; then
      echo "Device Tree overlay for Allwinner H2+/H3 SoC - ${_dto_packages_desc[$i]}"
      return 0
    fi
  done
  return 1
}

package_sun8i-h3-dto-analog-codec() {
  pkgdesc="$(_get_package_desc analog-codec)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

  _install_dtbo 'sun8i-h3' 'analog-codec'
}

package_sun8i-h3-dto-cir() {
  pkgdesc="$(_get_package_desc cir)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'cir'
}

package_sun8i-h3-dto-usbhost0() {
  pkgdesc="$(_get_package_desc usbhost0)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'usbhost0'
}

package_sun8i-h3-dto-usbhost1() {
  pkgdesc="$(_get_package_desc usbhost1)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')
  
   _install_dtbo 'sun8i-h3' 'usbhost1'
}

package_sun8i-h3-dto-usbhost2() {
  pkgdesc="$(_get_package_desc usbhost2)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'usbhost2'
}

package_sun8i-h3-dto-usbhost3() {
  pkgdesc="$(_get_package_desc usbhost3)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'usbhost3'
}

package_sun8i-h3-dto-i2c0() {
  pkgdesc="$(_get_package_desc i2c0)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'i2c0'
}

package_sun8i-h3-dto-i2c1() {
  pkgdesc="$(_get_package_desc i2c1)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'i2c1'
}

package_sun8i-h3-dto-i2c2() {
  pkgdesc="$(_get_package_desc i2c2)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'i2c2'
}

package_sun8i-h3-dto-uart1() {
  pkgdesc="$(_get_package_desc uart1)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'uart1'
}

package_sun8i-h3-dto-uart2() {
  pkgdesc="$(_get_package_desc uart2)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'uart2'
}

package_sun8i-h3-dto-uart3() {
  pkgdesc="$(_get_package_desc uart3)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'uart3'
}

package_sun8i-h3-dto-pwm() {
  pkgdesc="$(_get_package_desc pwm)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'pwm'
}

package_sun8i-h3-dto-spdif-out() {
  pkgdesc="$(_get_package_desc spdif-out)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'spdif-out'
}

package_sun8i-h3-dto-pps-gpio() {
  pkgdesc="$(_get_package_desc pps-gpio)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'pps-gpio'
}

package_sun8i-h3-dto-w1-gpio() {
  pkgdesc="$(_get_package_desc w1-gpio)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'w1-gpio'
}

package_sun8i-h3-dto-spi-jedec-nor() {
  pkgdesc="$(_get_package_desc spi-jedec-nor)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'spi-jedec-nor'
}

package_sun8i-h3-dto-spi-spidev() {
  pkgdesc="$(_get_package_desc spi-spidev)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'spi-spidev'
}

package_sun8i-h3-dto-spi-add-cs1() {
  pkgdesc="$(_get_package_desc spi-add-cs1)"
  groups=('sun8i-h3-dto')
  depends=('sun8i-h3-dt-overlays')

   _install_dtbo 'sun8i-h3' 'spi-add-cs1'
}