# $Id$
# Maintainer : Thomas Baechler <thomas@archlinux.org>

pkgname=nvidia-275xx-pf
pkgver=275.43
_minkver=3.4
_maxkver=3.5
_extramodules=extramodules-${_minkver}-pf
_kernver="$(cat /usr/lib/modules/${_extramodules}/version)"
pkgrel=3
pkgdesc="NVIDIA 275-xx drivers for linux-pf"
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
#depends=("linux-pf<${_maxkver}" "linux-pf>=${_minkver}" "nvidia-utils-275xx=${pkgver}")
#makedepends=("linux-headers-pf<${_maxkver}" "linux-headers-pf>=${_minkver}")
conflicts=('nvidia-pf')
license=('custom')
install=nvidia.install
options=(!strip)

if [ "$CARCH" = "i686" ]; then
    _arch='x86'
    _pkg="NVIDIA-Linux-${_arch}-${pkgver}"
    source=("http://us.download.nvidia.com/XFree86/Linux-${_arch}/${pkgver}/${_pkg}.run")
    md5sums=('aba5889666208d6b69aa6b27ff608842')
elif [ "$CARCH" = "x86_64" ]; then
    _arch='x86_64'
    _pkg="NVIDIA-Linux-${_arch}-${pkgver}-no-compat32"
    source=("http://us.download.nvidia.com/XFree86/Linux-${_arch}/${pkgver}/${_pkg}.run")
    md5sums=('5a80b13f0e92e33367d49866f6377dc1')
fi

build() {
    cd "${srcdir}"
    sh "${_pkg}.run" --extract-only
    cd "${_pkg}/kernel"
    sed -i 's/CFLAGS="$CFLAGS -I$SOURCES\/arch\/x86\/include"/CFLAGS="$CFLAGS -I$SOURCES\/arch\/x86\/include -I$SOURCES\/arch\/x86\/include\/generated"/' conftest.sh
    sed -i 's/#include <asm\/system.h>/#include <linux\/version.h>\n#if LINUX_VERSION_CODE < KERNEL_VERSION(3, 4, 0)\n#include <asm\/system.h>\n#endif/' conftest.sh
    sed -i 's/#include <asm\/system.h>/#if LINUX_VERSION_CODE < KERNEL_VERSION(3, 4, 0)\n#include <asm\/system>\n#endif/' nv-linux.h
    make SYSSRC=/usr/lib/modules/"${_kernver}/build" module
}

package() {
    install -D -m644 "${srcdir}/${_pkg}/kernel/nvidia.ko" \
        "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia.ko"
    install -d -m755 "${pkgdir}/etc/modprobe.d"
    echo "blacklist nouveau" >> "${pkgdir}/etc/modprobe.d/nouveau_blacklist.conf"
    sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='${_extramodules}'/" "${startdir}/nvidia.install"
    gzip "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia.ko"
}
