# Maintainer: Martin Schmölzer <mschmoelzer@gmail.com>

# Based on the summon-arm-toolchain script by Piotr Esden-Tempski
# https://github.com/esden/summon-arm-toolchain

pkgname=cross-arm-none-eabi-gcc
pkgver=4.8.0
pkgrel=4
epoch=1
#_snapshot=4.8-20130411
_newlibver=2.0.0
pkgdesc="The GNU Compiler Collection - cross compiler for ARM EABI (bare-metal) target."
arch=(i686 x86_64)
url="http://gcc.gnu.org/"
license=('GPL' 'LGPL')
groups=('cross-arm-none-eabi')
depends=('cross-arm-none-eabi-binutils>=2.23.2' 'gmp' 'mpfr' 'libmpc')
makedepends=('flex' 'bison')
conflicts=('cross-arm-none-eabi-gcc-base')
options=(!libtool !emptydirs !strip zipman docs)
source=(ftp://ftp.gnu.org/gnu/gcc/gcc-${pkgver}/gcc-${pkgver}.tar.bz2
        #ftp://gcc.gnu.org/pub/gcc/snapshots/${_snapshot}/gcc-${_snapshot}.tar.bz2
        ftp://sourceware.org/pub/newlib/newlib-${_newlibver}.tar.gz
        gcc-${pkgver}-multilib.patch
        gcc-${pkgver}-no-exceptions.patch
        gcc-${pkgver}-fix-naked-functions.patch)
sha256sums=('b037fe5132b71ecad2ea7141ec92292b5d32427bf90fd90cde432b1d5abacc2c'
            '49c29e9129325e7c3b221aa829743ddcd796d024440e47c80fc0d6769af72d8a'
            'b9a92021893b508b7997af7d26c93d549a5b25e7a64986dd62dbf03b2f4e46d8'
            'fcc5e73711cbf127746228ce12ea2e067a15f11b8ae969ba3376404762284b44'
            'd9512759d7e3c5642f7c475c0ca9a8bce6eb3f71a7d2e3595780e2ed2976ccef')

if [ -n "${_snapshot}" ]; then
  _basedir=gcc-${_snapshot}
else
  _basedir=gcc-${pkgver}
fi

build() {
  export CFLAGS="-O2"
  export CXXFLAGS="-O2"
  unset CPPFLAGS

  cd ${srcdir}

  # Move newlib and libgloss to gcc source directory
  mv ${srcdir}/newlib-${_newlibver}/newlib   ${_basedir}
  mv ${srcdir}/newlib-${_newlibver}/libgloss ${_basedir}

  cd ${srcdir}/${_basedir}

  patch -Np1 -i "${srcdir}/gcc-${pkgver}-multilib.patch"
  patch -Np1 -i "${srcdir}/gcc-${pkgver}-no-exceptions.patch"
  patch -Np1 -i "${srcdir}/gcc-${pkgver}-fix-naked-functions.patch"

  mkdir build
  cd build

  ../configure --target=arm-none-eabi \
               --prefix=/usr \
               --libexecdir=/usr/lib \
               --with-pkgversion="Arch User Repository" \
               --with-bugurl="https://aur.archlinux.org/packages/cross-arm-none-eabi-gcc" \
               --enable-multilib \
               --enable-interwork \
               --enable-languages=c,c++ \
               --with-newlib \
               --with-gnu-as \
               --with-gnu-ld \
               --disable-nls \
               --disable-shared \
               --disable-threads \
               --with-headers=newlib/libc/include \
               --disable-libssp \
               --disable-libstdcxx-pch \
               --disable-libmudflap \
               --disable-libgomp \
               --with-system-zlib \
               --disable-newlib-supplied-syscalls

  make
}

package() {
  cd ${srcdir}/${_basedir}/build

  make DESTDIR=${pkgdir} -j1 install

  # libiberty.a conflicts with host version
  rm -f  $pkgdir/usr/lib/libiberty.a

  # We don't want these files in a cross version
  rm -f  $pkgdir/usr/share/man/man7/fsf-funding.7*
  rm -f  $pkgdir/usr/share/man/man7/gfdl.7*
  rm -f  $pkgdir/usr/share/man/man7/gpl.7*
  rm -rf $pkgdir/usr/share/info
  rm -rf $pkgdir/usr/share/gcc-${pkgver}
}

# vim: set ts=2 sw=2 ft=sh noet:
