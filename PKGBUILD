# Maintainer: graysky <graysky AT archlinux DOT us>
# Contributor: Sergej Pupykin <pupykin.s+arch@gmail.com>
# Contributor: Brad Fanella <bradfanella@archlinux.us>
# Contributor: [vEX] <niechift.dot.vex.at.gmail.dot.com>
# Contributor: Zeqadious <zeqadious.at.gmail.dot.com>
# Contributor: BlackIkeEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Maxime Gauduin <alucryd@gmail.com>

_prefix=/usr

pkgname=xbmc-beta
_pkgname=xbmc
pkgver=13.2rc1
_codename=Gotham
pkgrel=2
pkgdesc="A software media player and entertainment hub for digital media - 13.2rc1."
arch=('i686' 'x86_64')
url="http://xbmc.org"
license=('GPL2')
provides=("xbmc=$pkgver")
conflicts=('xbmc')
depends=(
'avahi' 'hicolor-icon-theme' 'lame' 'libass' 'libbluray' 'libcdio' 'libmad'
'libmicrohttpd' 'libmodplug' 'libmpeg2' 'libmysqlclient' 'libsamplerate'
'libssh' 'libva' 'libvdpau' 'libvorbis' 'libxrandr' 'libxslt' 'lzo' 'mesa'
'mesa-demos' 'python2' 'python2-pillow' 'rtmpdump' 'sdl_image' 'smbclient'
'taglib' 'tinyxml' 'unzip' 'xorg-xdpyinfo' 'yajl'
)
makedepends=(
'afpfs-ng' 'libnfs' 'libplist' 'shairplay' 'libcec' 'boost' 'cmake' 'doxygen'
'gperf' 'jasper' 'java-runtime-headless' 'nasm' 'swig' 'taglib' 'udisks'
'upower' 'zip'
)
optdepends=(
'afpfs-ng: Apple File Protocol suport'
'libcec: Pulse-Eight USB-CEC adapter support'
'libnfs: NFS Share support'
'libplist: AirPlay support'
'lirc: remote controller support'
'pulseaudio: for pulseaudio support'
'shairplay: AirPlay support'
'unrar: access compressed files without unpacking them'
'udisks: automount external drives'
'upower: display battery level'
'xbmc-standalone-service: systemd service and user to autostart xbmc at boot'
'xorg-xinit: autostart xbmc'
)
install="${_pkgname}.install"
source=(
  "xbmc-$pkgver-$_codename.tar.gz::https://github.com/xbmc/xbmc/archive/$pkgver-$_codename.tar.gz"
  'libnfs-header-changes.patch'
)
sha256sums=('e2475d70a7ee9f4fe7d7fe3c2c14b4a45855142282f39bdd5ec65d580abad882'
            '9b49e98540cfcf3431d38cf9cd981ed8450ccbfcb70eed7f862f82f39d66763b')

prepare() {
  cd "$srcdir/xbmc-$pkgver-$_codename"
  patch -p1 -i "$srcdir/libnfs-header-changes.patch"

  find -type f -name *.py -exec sed 's|^#!.*python$|#!/usr/bin/python2|' -i "{}" +
  sed 's|^#!.*python$|#!/usr/bin/python2|' -i tools/depends/native/rpl-native/rpl
  sed 's/python/python2/' -i tools/Linux/xbmc.sh.in
}

build() {
  cd "$srcdir/xbmc-$pkgver-$_codename"

  # Bootstrapping XBMC
  ./bootstrap

  # Configuring XBMC
  export PYTHON_VERSION=2  # external python v2
  ./configure --prefix=$_prefix --exec-prefix=$_prefix \
    --disable-debug \
    --enable-optimizations \
    --enable-gl \
    --enable-sdl \
    --enable-vaapi \
    --enable-vdpau \
    --enable-xvba \
    --enable-joystick \
    --enable-xrandr \
    --enable-rsxs \
    --enable-projectm \
    --enable-x11 \
    --enable-rtmp \
    --enable-samba \
    --enable-nfs \
    --enable-afpclient \
    --enable-airplay \
    --enable-airtunes \
    --enable-ffmpeg-libvorbis \
    --enable-dvdcss \
    --disable-hal \
    --enable-avahi \
    --enable-webserver \
    --enable-optical-drive \
    --enable-libbluray \
    --enable-texturepacker \
    --enable-udev \
    --enable-libusb \
    --enable-libcec \
    --enable-external-libraries \
    --with-lirc-device=/run/lirc/lircd

  # Now (finally) build
  make
}

package() {
  cd "$srcdir/xbmc-$pkgver-$_codename"
  # Running make install
  make DESTDIR="$pkgdir" install

  # Tools
  install -D -m 0755 "$srcdir/xbmc-$pkgver-$_codename/tools/TexturePacker/TexturePacker" \
    "${pkgdir}$_prefix/lib/xbmc/"

  # Licenses
  install -dm755 "${pkgdir}$_prefix/share/licenses/$_pkgname"
  for licensef in LICENSE.GPL copying.txt; do
    mv "${pkgdir}$_prefix/share/doc/xbmc/$licensef" \
      "${pkgdir}$_prefix/share/licenses/$_pkgname"
  done
}
# vim:set ts=2 sw=2 et:
