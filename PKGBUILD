_realname=qemu
pkgbase=mingw-w64-${_realname}
pkgname=(
  "${MINGW_PACKAGE_PREFIX}-qemu"
  "${MINGW_PACKAGE_PREFIX}-qemu-common"
  "${MINGW_PACKAGE_PREFIX}-qemu-guest-agent"
  "${MINGW_PACKAGE_PREFIX}-qemu-image-util"
)
pkgver=staging.r0.g0000000 # This is auto-updated by the pkgver() function
pkgrel=1
pkgdesc="QEMU - a generic and open source machine emulator and virtualizer (mingw-w64) [staging]"
arch=('any')
mingw_arch=('ucrt64') # Restricted to ucrt64 for faster CI; add 'mingw64' 'clang64' etc. if needed
license=('spdx:GPL-2.0-or-later AND LGPL-2.1-or-later AND BSD-2-Clause AND BSD-3-Clause AND MIT')
url="https://qemu.org/"
msys2_repository_url="https://github.com/qemu/qemu"

makedepends=(
  "git"
  "${MINGW_PACKAGE_PREFIX}-cc"
  "${MINGW_PACKAGE_PREFIX}-meson"
  "${MINGW_PACKAGE_PREFIX}-autotools"
  "${MINGW_PACKAGE_PREFIX}-ninja"
  "${MINGW_PACKAGE_PREFIX}-python"
  "${MINGW_PACKAGE_PREFIX}-python-pip"
  "${MINGW_PACKAGE_PREFIX}-python-setuptools"
  "${MINGW_PACKAGE_PREFIX}-python-sphinx"
  "${MINGW_PACKAGE_PREFIX}-python-sphinx_rtd_theme"
  "${MINGW_PACKAGE_PREFIX}-python-wheel"
  "${MINGW_PACKAGE_PREFIX}-tools"
)
case ${MSYSTEM} in
  CLANG*)
    makedepends+=("binutils")
      ;;
esac
depends=(
  "${MINGW_PACKAGE_PREFIX}-angleproject"
  "${MINGW_PACKAGE_PREFIX}-capstone"
  "${MINGW_PACKAGE_PREFIX}-curl"
  "${MINGW_PACKAGE_PREFIX}-cyrus-sasl"
  "${MINGW_PACKAGE_PREFIX}-dtc"
  "${MINGW_PACKAGE_PREFIX}-expat"
  "${MINGW_PACKAGE_PREFIX}-fontconfig"
  "${MINGW_PACKAGE_PREFIX}-freetype"
  "${MINGW_PACKAGE_PREFIX}-fribidi"
  "${MINGW_PACKAGE_PREFIX}-cc-libs"
  "${MINGW_PACKAGE_PREFIX}-gdk-pixbuf2"
  "${MINGW_PACKAGE_PREFIX}-gettext"
  "${MINGW_PACKAGE_PREFIX}-glib2"
  "${MINGW_PACKAGE_PREFIX}-gmp"
  "${MINGW_PACKAGE_PREFIX}-gnutls"
  "${MINGW_PACKAGE_PREFIX}-graphite2"
  "${MINGW_PACKAGE_PREFIX}-gst-plugins-base"
  "${MINGW_PACKAGE_PREFIX}-gstreamer"
  "${MINGW_PACKAGE_PREFIX}-gtk-vnc"
  "${MINGW_PACKAGE_PREFIX}-gtk3"
  "${MINGW_PACKAGE_PREFIX}-harfbuzz"
  "${MINGW_PACKAGE_PREFIX}-jack2"
  "${MINGW_PACKAGE_PREFIX}-jbigkit"
  "${MINGW_PACKAGE_PREFIX}-lerc"
  "${MINGW_PACKAGE_PREFIX}-libc++"
  "${MINGW_PACKAGE_PREFIX}-libdatrie"
  "${MINGW_PACKAGE_PREFIX}-libdeflate"
  "${MINGW_PACKAGE_PREFIX}-libepoxy"
  "${MINGW_PACKAGE_PREFIX}-libffi"
  "${MINGW_PACKAGE_PREFIX}-libiconv"
  "${MINGW_PACKAGE_PREFIX}-libidn2"
  "${MINGW_PACKAGE_PREFIX}-libjpeg-turbo"
  "${MINGW_PACKAGE_PREFIX}-libnfs"
  "${MINGW_PACKAGE_PREFIX}-libpng"
  "${MINGW_PACKAGE_PREFIX}-libpsl"
  "${MINGW_PACKAGE_PREFIX}-libslirp"
  "${MINGW_PACKAGE_PREFIX}-libssh"
  "${MINGW_PACKAGE_PREFIX}-libssh2"
  "${MINGW_PACKAGE_PREFIX}-libtasn1"
  "${MINGW_PACKAGE_PREFIX}-libthai"
  "${MINGW_PACKAGE_PREFIX}-libtiff"
  "${MINGW_PACKAGE_PREFIX}-libunistring"
  "${MINGW_PACKAGE_PREFIX}-libunwind"
  "${MINGW_PACKAGE_PREFIX}-libusb"
  "${MINGW_PACKAGE_PREFIX}-libwebp"
  "${MINGW_PACKAGE_PREFIX}-libwinpthread"
  "${MINGW_PACKAGE_PREFIX}-lz4"
  "${MINGW_PACKAGE_PREFIX}-lzo2"
  "${MINGW_PACKAGE_PREFIX}-nettle"
  "${MINGW_PACKAGE_PREFIX}-openssl"
  "${MINGW_PACKAGE_PREFIX}-opus"
  "${MINGW_PACKAGE_PREFIX}-orc"
  "${MINGW_PACKAGE_PREFIX}-p11-kit"
  "${MINGW_PACKAGE_PREFIX}-pango"
  "${MINGW_PACKAGE_PREFIX}-pixman"
  "${MINGW_PACKAGE_PREFIX}-SDL2"
  "${MINGW_PACKAGE_PREFIX}-SDL2_image"
  "${MINGW_PACKAGE_PREFIX}-snappy"
  "${MINGW_PACKAGE_PREFIX}-spice"
  "${MINGW_PACKAGE_PREFIX}-usbredir"
  "${MINGW_PACKAGE_PREFIX}-virglrenderer"
  "${MINGW_PACKAGE_PREFIX}-xz"
  "${MINGW_PACKAGE_PREFIX}-zlib"
  "${MINGW_PACKAGE_PREFIX}-zstd"
)

options=('!emptydirs')
source=(
  "qemu::git+https://gitlab.com/qemu-project/qemu.git#branch=staging"
)
sha256sums=('SKIP')

pkgver() {
  cd "${srcdir}/qemu"
  # Generate a sequential version format based on Git commit (e.g. r12345.abcd123)
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
  # Generate dummy files originally expected by the MSYS2 packager so build doesn't fail
  echo "MSYS2 Build from CI" > "${srcdir}/msys2.readme.txt"
  echo "MSYS2 QEMU Guest Agent" > "${srcdir}/msys2.qemu-guest-agent.txt"
  echo "#!/bin/bash" > "${srcdir}/msys2.examples.tests.sh"

  cd "${srcdir}/qemu"
  # Initialize QEMU submodules required for the build process
  git submodule update --init --recursive
}

build() {
  mkdir -pv "${srcdir}/build-${MSYSTEM}" && cd "${srcdir}/build-${MSYSTEM}"

  ../qemu/configure $CONFIGURE_OPTS \
    --prefix=${MINGW_PREFIX} \
    --bindir=bin \
    --datadir=share/qemu \
    --localedir=share/locale \
    --mandir=share/man \
    --docdir=share/doc/qemu

  # Finally build
  make

  cd qga/vss-win32
  widl -I${MINGW_PREFIX}/include -L${MINGW_PREFIX}/include \
    -t "${srcdir}"/qemu/qga/vss-win32/qga-vss.idl -o qga-vss.tlb
}

makeinstall() {
  cd "${srcdir}/build-${MSYSTEM}"

  make DESTDIR="${pkgdir}" install

  local P_INSTALL=$(find "${pkgdir}" -name "$(basename ${MINGW_PREFIX})" -type d)
  [ -d "${pkgdir}"/${MINGW_PREFIX} ] || mv -v "$P_INSTALL" "${pkgdir}"/

  cd "${pkgdir}"/${MINGW_PREFIX}

  mkdir -pv share/qemu/icons
  mv -v share/qemu/icons share/ || true
  mkdir -pv share/qemu/applications
  mv -v share/qemu/applications share/ || true
}

makeinstallpkg() {
  local _pkg=$1

  makeinstall

  cd "${pkgdir}"

  local _qemu=qemu/${MINGW_PREFIX}
  mkdir -pv qemu
  mv -v ./${MINGW_PREFIX} qemu/

  local _qemu_ga=qemu-guest-agent/${MINGW_PREFIX}
  mkdir -pv ${_qemu_ga}/bin ${_qemu_ga}/share/man/man7 ${_qemu_ga}/share/man/man8
  mv -v ${_qemu}/bin/qemu-ga.exe  ${_qemu_ga}/bin/ || true
  mv -v ${_qemu}/share/man/man8/qemu-ga.8 ${_qemu_ga}/share/man/man8/ || true
  mv -v ${_qemu}/share/man/man7/qemu-ga-ref.7 ${_qemu_ga}/share/man/man7/ || true

  local _qemu_img=qemu-image-util/${MINGW_PREFIX}
  mkdir -pv ${_qemu_img}/bin ${_qemu_img}/share/man/man1
  mv -v ${_qemu}/bin/qemu-img.exe  ${_qemu_img}/bin/ || true
  mv -v ${_qemu}/share/man/man1/qemu-img.1 ${_qemu_img}/share/man/man1/ || true

  local _qemu_com=qemu-common/${MINGW_PREFIX}
  mkdir -pv ${_qemu_com}/share/doc
  mv -v ${_qemu}/share/doc/qemu  ${_qemu_com}/share/doc/ || true

  mv -v ${_pkg}/${MINGW_PREFIX} ./
  rm -rf ${_qemu} ${_qemu_ga} ${_qemu_img} ${_qemu_com}

  cd ./${MINGW_PREFIX}
}

package_qemu() {
  depends+=(
    "${MINGW_PACKAGE_PREFIX}-qemu-common"
    "${MINGW_PACKAGE_PREFIX}-qemu-guest-agent"
    "${MINGW_PACKAGE_PREFIX}-qemu-image-util"
  )
  optdepends=(
    "${MINGW_PACKAGE_PREFIX}-spice-gtk: Contains spicy. For connections to QEMU SPICE server"
    "${MINGW_PACKAGE_PREFIX}-virt-viewer: Contains remote-viewer. For connections to QEMU SPICE or VNC server"
  )

  makeinstallpkg qemu

  mkdir -pv share/qemu/firmware
  find share/qemu/firmware -type f -exec \
    sed -i "s%\(\"filename\"\s*:\s*\"\).*edk2%\1${MINGW_PREFIX}/share/qemu/edk2%" {} \;

  mkdir -pv lib/qemu/plugins/test lib/qemu/plugins/contrib
  install -t lib/qemu/plugins/test "${srcdir}"/build-${MSYSTEM}/tests/tcg/plugins/*.dll || true
  install -t lib/qemu/plugins/contrib "${srcdir}"/build-${MSYSTEM}/contrib/plugins/*.dll || true
}

package_qemu-common() {
  depends=()

  makeinstallpkg qemu-common

  mkdir -pv share/licenses/qemu
  tar -C "${srcdir}"/qemu -c $(
    cd "${srcdir}"/qemu &&
      find -iname "*COPYING*" -or -iname "*LICENSE*" |
      grep -v "\s" |
      grep -Ev "meson.(test|msi|doc)" |
      grep -Ev "(license.c|relicense.pl|license.doctree|LicenseCheck)"
    ) | tar -C share/licenses/qemu -xv

  install -v -Dm644 -t share/doc/qemu/ "${srcdir}"/msys2.readme.txt
  install -v -Dm644 -t share/doc/qemu/ "${srcdir}"/msys2.qemu-guest-agent.txt
  install -v -Dm755 -t share/doc/qemu/ "${srcdir}"/msys2.examples.tests.sh
}

package_qemu-guest-agent() {
  depends=(
    "${MINGW_PACKAGE_PREFIX}-gettext"
    "${MINGW_PACKAGE_PREFIX}-cc-libs"
    "${MINGW_PACKAGE_PREFIX}-glib2"
    "${MINGW_PACKAGE_PREFIX}-libc++"
    "${MINGW_PACKAGE_PREFIX}-libiconv"
    "${MINGW_PACKAGE_PREFIX}-libunwind"
    "${MINGW_PACKAGE_PREFIX}-libwinpthread"
    "${MINGW_PACKAGE_PREFIX}-qemu-common"
  )

  makeinstallpkg qemu-guest-agent

  install -v -Dm644 -t bin/ "${srcdir}"/build-${MSYSTEM}/qga/vss-win32/qga-vss.dll
  install -v -Dm644 -t bin/ "${srcdir}"/build-${MSYSTEM}/qga/vss-win32/qga-vss.tlb
}

package_qemu-image-util() {
  depends=(
    "${MINGW_PACKAGE_PREFIX}-brotli"
    "${MINGW_PACKAGE_PREFIX}-bzip2"
    "${MINGW_PACKAGE_PREFIX}-curl"
    "${MINGW_PACKAGE_PREFIX}-cc-libs"
    "${MINGW_PACKAGE_PREFIX}-gettext"
    "${MINGW_PACKAGE_PREFIX}-glib2"
    "${MINGW_PACKAGE_PREFIX}-gmp"
    "${MINGW_PACKAGE_PREFIX}-gnutls"
    "${MINGW_PACKAGE_PREFIX}-libiconv"
    "${MINGW_PACKAGE_PREFIX}-libidn2"
    "${MINGW_PACKAGE_PREFIX}-libffi"
    "${MINGW_PACKAGE_PREFIX}-libnfs"
    "${MINGW_PACKAGE_PREFIX}-libpsl"
    "${MINGW_PACKAGE_PREFIX}-libssh"
    "${MINGW_PACKAGE_PREFIX}-libssh2"
    "${MINGW_PACKAGE_PREFIX}-libunistring"
    "${MINGW_PACKAGE_PREFIX}-libtasn1"
    "${MINGW_PACKAGE_PREFIX}-libwinpthread"
    "${MINGW_PACKAGE_PREFIX}-nettle"
    "${MINGW_PACKAGE_PREFIX}-nghttp2"
    "${MINGW_PACKAGE_PREFIX}-openssl"
    "${MINGW_PACKAGE_PREFIX}-p11-kit"
    "${MINGW_PACKAGE_PREFIX}-zlib"
    "${MINGW_PACKAGE_PREFIX}-zstd"
    "${MINGW_PACKAGE_PREFIX}-qemu-common"
  )

  makeinstallpkg qemu-image-util
}

# Generate wrappers
for _name in "${pkgname[@]}"; do
  _short="package_${_name#${MINGW_PACKAGE_PREFIX}-}"
  _func="$(declare -f "${_short}")"
  eval "${_func/#${_short}/package_${_name}}"
done
