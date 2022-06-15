#https://github.com/calamares/calamares/releases

pkgname=phyOS-calamares
_pkgname=calamares
pkgver=3.2.59
pkgrel=01
pkgdesc='Distribution-independent installer framework'
arch=('i686' 'x86_64')
license=(GPL)
url="https://github.com/calamares/calamares/releases"
license=('LGPL')
depends=('kconfig' 'kcoreaddons' 'kiconthemes' 'ki18n' 'kio' 'solid' 'yaml-cpp' 'kpmcore>=4.2.0' 'mkinitcpio-openswap'
         'boost-libs' 'ckbcomp' 'hwinfo' 'qt5-svg' 'polkit-qt5' 'gtk-update-icon-cache' 'plasma-framework'
         'qt5-xmlpatterns' 'squashfs-tools' 'libpwquality' 'efibootmgr' 'icu')
conflicts=('arco-calamares' 'arco-calamares-comp' 'arco-calamares-dev')
makedepends=('extra-cmake-modules' 'qt5-tools' 'qt5-translations' 'git' 'boost')
backup=('usr/share/calamares/modules/bootloader.conf'
        'usr/share/calamares/modules/displaymanager.conf'
        'usr/share/calamares/modules/initcpio.conf'
        'usr/share/calamares/modules/unpackfs.conf')

source=("$_pkgname-$pkgver::$url/download/v$pkgver/$_pkgname-$pkgver.tar.gz"
	"ucode_main.py"
	"ucode_module.desc"
	"dm_main.py"
	"packages_main.py"
	"cal-phyOS.desktop"
	"cal-phyOS-debug.desktop"
	"calamares_polkit")

sha256sums=('55adef250613e80a868f2aa3d1e57bdae5b769387d91decf0fe2b64e3605574f'
            '4df74b6c3b238cf0bff01a04566e265de3f143c297fbd8e0c0aa9447595cc8e9'
            '8c7ca12724e8b196c227ad898e88a09f82b06a871c44c3ece4c64fbbf031ca4a'
            '830b6e2efaf794dd3c0503dc990249742ac97b8a92a414b2c1cdc9d562864aed'
            '0d8a204420bb863f17c1e0a0c008443a5db1c37306e060ffacfde6e29e214ea5'
            '6d030bfc6d20caf3ad70cc1676763cd75638c8c79c2f9f1d7f3d05331030aa2d'
            'fd884147b0166e8115d8128282db8c2b00a93f81c17d8defd18e1fe01a377d1b'
            '7c38fc1461307e4aff9b72922c6406789776a8252521618c5dcbe85beccb04f5'
            '966c5f0834039dc6a7529e75f70417ba2510b1e643ffb49fd25855ce9dc244b4')

pkgver() {
	cd ${srcdir}/$_pkgname-$pkgver
	_ver="$(cat CMakeLists.txt | grep -m3 -e "  VERSION" | grep -o "[[:digit:]]*" | xargs | sed s'/ /./g')"
	_git=".r$(git rev-list --count HEAD).$(git rev-parse --short HEAD)"
	printf '%s%s' "${_ver}" #"${_git}"
}

prepare() {

	sed -i -e 's/"Install configuration files" OFF/"Install configuration files" ON/' "$srcdir/${_pkgname}-${pkgver}/CMakeLists.txt"
	sed -i -e 's/# DEBUG_FILESYSTEMS/DEBUG_FILESYSTEMS/' "$srcdir/${_pkgname}-${pkgver}/CMakeLists.txt"
	sed -i -e "s/desired_size = 512 \* 1024 \* 1024  \# 512MiB/desired_size = 512 \* 1024 \* 1024 \* 4  \# 2048MiB/" "$srcdir/${_pkgname}-${pkgver}/src/modules/fstab/main.py"

	# add pkgrelease to patch-version
	cd ${_pkgname}-${pkgver}
	_patchver="$(cat CMakeLists.txt | grep -m3 -e CALAMARES_VERSION_PATCH | grep -o "[[:digit:]]*" | xargs)"
	sed -i -e "s|CALAMARES_VERSION_PATCH $_patchver|CALAMARES_VERSION_PATCH $_patchver-$pkgrel|g" CMakeLists.txt

	install -Dm644 "${srcdir}/"ucode_main.py ${srcdir}/$_pkgname-$pkgver/src/modules/ucode/main.py
	install -Dm644 "${srcdir}/"ucode_module.desc ${srcdir}/$_pkgname-$pkgver/src/modules/ucode/module.desc
	install -Dm644 "${srcdir}/"dm_main.py ${srcdir}/$_pkgname-$pkgver/src/modules/displaymanager/main.py
	install -Dm644 "${srcdir}/"packages_main.py ${srcdir}/$_pkgname-$pkgver/src/modules/packages/main.py
}

build() {
	cd $_pkgname-$pkgver

	mkdir -p build
	cd build
	cmake .. \
	-DCMAKE_BUILD_TYPE=Release \
	-DCMAKE_INSTALL_PREFIX=/usr \
	-DCMAKE_INSTALL_LIBDIR=lib \
	-DWITH_PYTHONQT=OFF \
	-DWITH_KF5DBus=OFF \
	-DBoost_NO_BOOST_CMAKE=ON \
	-DWEBVIEW_FORCE_WEBKIT=OFF \
	-DSKIP_MODULES="webview tracking interactiveterminal initramfs \
	initramfscfg dracut dracutlukscfg \
	dummyprocess dummypython dummycpp \
	dummypythonqt services-openrc \
	keyboardq localeq welcomeq"
	make
}

package() {
	cd ${_pkgname}-${pkgver}/build
	make DESTDIR="$pkgdir" install
	install -Dm644 "$srcdir/cal-phyOS.desktop" "$pkgdir/usr/share/applications/cal-phyOS.desktop"
	install -Dm644 "$srcdir/cal-phyOS-debug.desktop" "$pkgdir/usr/share/applications/cal-phyOS-debug.desktop"
	install -Dm755 "$srcdir/calamares_polkit" "$pkgdir/usr/bin/calamares_polkit"
	rm "$pkgdir/usr/share/applications/calamares.desktop"
}
