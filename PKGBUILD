# $Id: PKGBUILD 132532 2015-04-29 20:38:01Z jlichtblau $
# Maintainer: Vesa Kaihlavirta <vegai@iki.fi>
# Maintainer: Jaroslav Lichtblau <svetlemodry@archlinux.org>

_pkgbase=quassel
pkgbase=quassel-qt
pkgname=('quassel-qt-client' 'quassel-qt-core' 'quassel-qt-monolithic')
pkgver=0.12.2
pkgrel=2
pkgdesc="Next-generation distributed IRC client"
arch=('i686' 'x86_64')
url="http://quassel-irc.org/"
license=('GPL')
makedepends=('cmake' 'extra-cmake-modules' 'qt5-base' 'qt5-tools' 'qca-qt5' 'qt5-script')
source=(http://quassel-irc.org/pub/$_pkgbase-$pkgver.tar.bz2
        $_pkgbase.service
        $_pkgbase.conf
        fix_qt5.5_build.patch
        fix_ssl_check.patch)
sha256sums=('6bd6f79ecb88fb857bea7e89c767a3bd0f413ff01bae9298dd2e563478947897'
            '5dbe20290f3361b9b7a74a52905137e76b656976febf2d31082a2276f9dcde7f'
            'f3031ea8217e01ba42cea14606169e3e27affa5918968ffd5a03c21ae92fe2b8'
            'a92914a26fe3f6601da7cfd26fb6a6fe6a0ba6c0bd91ac9416765974127365f6'
            'c62f50511322c6cd27b10b4c3d92d0c04262454467b95f1a84064f784c47ba7b')

prepare() {
    cd "$_pkgbase-$pkgver"
    patch -p1 -i "$srcdir/fix_qt5.5_build.patch"
    patch -p1 -i "$srcdir/fix_ssl_check.patch"
}

build() {
  cd "${srcdir}"
  [[ ! -d build ]] && mkdir build
  [[ ! -d build-client ]] && mkdir build-client
  [[ ! -d build-mono ]] && mkdir build-mono

  # Core
  cd "${srcdir}"/build
  cmake \
    -DCMAKE_INSTALL_PREFIX=/usr/ \
    -DWITH_KDE=OFF \
    -DUSE_QT5=ON \
    -DWITH_OPENSSL=ON \
    -DWANT_CORE=ON \
    -DWANT_MONO=OFF \
    -DWANT_QTCLIENT=OFF \
    -DWITH_DBUS=OFF \
    -DWITH_OXYGEN=OFF \
    -DWITH_PHONON=OFF \
    -DWITH_WEBKIT=OFF \
    -DCMAKE_BUILD_TYPE="Release" \
    ../quassel-${pkgver}/ \
    -Wno-dev
  make

  # Client
  cd "${srcdir}"/build-client
  cmake \
    -DCMAKE_INSTALL_PREFIX=/usr/ \
    -DUSE_QT5=ON \
    -DWITH_KDE=OFF \
    -DWITH_OPENSSL=ON \
    -DWANT_CORE=OFF \
    -DWANT_QTCLIENT=ON \
    -DWANT_MONO=OFF \
    -DCMAKE_BUILD_TYPE="Release" \
    ../quassel-${pkgver}/ \
    -Wno-dev
  make

  # Monolithic
  cd "${srcdir}"/build-mono
  cmake \
    -DCMAKE_INSTALL_PREFIX=/usr/ \
    -DUSE_QT5=ON \
    -DWITH_KDE=OFF \
    -DWITH_OPENSSL=ON \
    -DWANT_CORE=OFF \
    -DWANT_QTCLIENT=OFF \
    -DWANT_MONO=ON \
    -DCMAKE_BUILD_TYPE="Release" \
    ../quassel-${pkgver}/ \
    -Wno-dev
  make
}

package_quassel-qt-core() {
  pkgdesc="Next-generation distributed IRC client - core only"
  depends=('icu' 'qca-qt5' 'qt5-script')
  conflicts=('quassel-core')
  install='quassel.install'
  backup=(etc/conf.d/quassel)
  cd "${srcdir}"/build

  make DESTDIR="${pkgdir}" install

  # Remove unneeded pixmap icons
  rm -r "${pkgdir}"/usr/share

  install -Dm644 "${srcdir}"/$_pkgbase.service \
    "${pkgdir}"/usr/lib/systemd/system/$_pkgbase.service
  install -Dm644 "${srcdir}"/$_pkgbase.conf \
    "${pkgdir}"/etc/conf.d/$_pkgbase
}

package_quassel-qt-client() {
  pkgdesc="Next-generation distributed IRC client - client only"
  depends=('qt5-base' 'qt5-webkit' 'desktop-file-utils' 'hicolor-icon-theme')
  conflicts=('quassel-monolithic' 'quassel-qt-monolithic' 'quassel-client')
  cd "${srcdir}"/build-client

  make DESTDIR="${pkgdir}" install
}

package_quassel-qt-monolithic() {
  pkgdesc="Next-generation distributed IRC client - monolithic"
  depends=('qt5-base' 'qca-qt5' 'qt5-webkit' 'qt5-script' 'desktop-file-utils' 'hicolor-icon-theme')
  conflicts=('quassel-client' 'quassel-qt-client' 'quassel-monolithic')
  cd "${srcdir}"/build-mono

  make DESTDIR="${pkgdir}" install
}
