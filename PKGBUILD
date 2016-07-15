# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor  Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Thomas Dziedzic < gostrc at gmail >
# Contributor: James Campos <james.r.campos@gmail.com>
# Contributor: BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Dongsheng Cai <dongsheng at moodle dot com>
# Contributor: Masutu Subric <masutu.arch at googlemail dot com>
# Contributor: TIanyi Cui <tianyicui@gmail.com>

_pkgname=nodejs
pkgname=('nodejs' 'nodejs-doc' 'npm')
_nodever=6.3.0
pkgver=${_nodever}
_npmver=3.10.5
pkgrel=1
arch=('i686' 'x86_64')
url='http://nodejs.org/'
license=('MIT')
source=("http://nodejs.org/dist/v$_nodever/node-v$_nodever.tar.xz"
"https://github.com/npm/npm/archive/v$_npmver.tar.gz")
sha256sums=('SKIP'
'SKIP')

prepare() {
  cd node-v$_nodever

  msg 'Fixing for python2 name'
  find -type f -exec sed \
    -e 's_^#!/usr/bin/env python$_&2_' \
    -e 's_^\(#!/usr/bin/python2\).[45]$_\1_' \
    -e 's_^#!/usr/bin/python$_&2_' \
    -e 's_^\( *exec \+\)python\( \+.*\)$_\1python2\2_'\
    -e 's_^\(.*\)python\( \+-c \+.*\)$_\1python2\2_'\
    -e "s_'python'_'python2'_" -i {} \;
  find test/ -type f -exec sed 's_python _python2 _' -i {} \;
}

build() {
	rm -rf $srcdir/node-v$_nodever/deps/npm/*
	cp -r $srcdir/npm-$_npmver/* $srcdir/node-v$_nodever/deps/npm
  cd $srcdir/node-v$_nodever

  export PYTHON=python2
  ./configure \
    --prefix=/usr \
    --with-intl=system-icu \
    --shared-openssl \
    --shared-zlib \
    --shared-libuv \
    --shared-http-parser
  make
}

package_nodejs() {
  pkgver=${_nodever}
	depends=('openssl' 'zlib' 'icu' 'libuv' 'http-parser')
	makedepends=('python2' 'procps-ng')
	pkgdesc='Evented I/O for V8 JavaScript'

  cd node-v$pkgver

  make DESTDIR="$pkgdir" install

	rm -rf ${pkgdir}/usr/lib
	rm ${pkgdir}/usr/bin/npm
	rm -rf ${pkgdir}/usr/include/npm

  install -D -m644 LICENSE \
    "$pkgdir"/usr/share/licenses/nodejs/LICENSE
}

package_nodejs-doc() {
	pkgdesc="Documentation for Node.js"
	pkgver=$_nodever

	cd node-v$pkgver
  install -d "$pkgdir"/usr/share/doc/nodejs
  cp -r doc/api/{*.html,assets} \
    "$pkgdir"/usr/share/doc/nodejs
}

package_npm() {
	depends=('nodejs')
	makedepends=('python2')
	pkgdesc="Package manager for Node.js."
  pkgver=$_npmver

	cd node-v$_nodever

	make DESTDIR="$pkgdir" install
	rm -rf ${pkgdir}/usr/share/systemtap \
				 ${pkgdir}/usr/include \
				 ${pkgdir}/usr/share/doc
	rm ${pkgdir}/usr/bin/node

	# Why 777? :/
	chmod -R u=rwX,go=rX "$pkgdir"

	# Fix files owned by nobody:
	chown -R root "$pkgdir/usr/lib/node_modules"

	# Provide node-gyp executable
  cp "$pkgdir"/usr/lib/node_modules/npm/bin/node-gyp-bin/node-gyp "$pkgdir"/usr/bin/node-gyp
  sed -i 's|"`dirname "$0"`/../../|"`dirname "$0"`/../lib/node_modules/npm/|' "$pkgdir"/usr/bin/node-gyp

	install -Dm644 deps/npm/LICENSE \
    "$pkgdir"/usr/share/licenses/npm/LICENSE
}
