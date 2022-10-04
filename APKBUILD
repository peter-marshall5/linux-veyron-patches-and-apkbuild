# Maintainer: 

pkgname=linux
pkgver=5.19.9
_kernver=$pkgver
pkgrel=2
pkgdesc="Linux kernel"
url="https://kernel.org"
depends="mkinitfs"
_depends_dev="perl gmp-dev elfutils-dev bash flex bison"
makedepends="$_depends_dev sed bc
	diffutils findutils xz"
options="!strip !check" # no tests
install=

#subpackages="$pkgname-dev:_dev:$CBUILD_ARCH"
source="
	config-linux.arm
"

arch="any"
license="GPL-2.0"

_carch="arm"
_cross="arm-linux-gnueabihf-"

prepare() {
	if [ ! -e "$srcdir"/linux-$_kernver ]; then

	cd "$srcdir"
	if [ ! -e linux-$_kernver.tar.xz ]; then
		wget https://cdn.kernel.org/pub/linux/kernel/v${pkgver%%.*}.x/linux-$_kernver.tar.xz -O linux-$_kernver.tar.xz
	fi
	tar xJf linux-$_kernver.tar.xz

	local _patch_failed=
	cd "$srcdir"/linux-$_kernver

	# first apply patches in specified order
	for i in $(find "$startdir"/patches -type f); do
		case $i in
		*.patch)
			msg "Applying $i..."
			if ! patch -s -p1 -N -i $i; then
				echo $i >>failed
				_patch_failed=1
			fi
			;;
		esac
	done

	if ! [ -z "$_patch_failed" ]; then
		error "The following patches failed:"
		cat failed
		return 1
	fi

	# remove localversion from patch if any
	rm -f localversion*
	oldconfig

	fi

	nconfig
}

oldconfig() {
	local _config=config-linux.${_carch}
	local _builddir="$srcdir"/build-linux.$_carch
	mkdir -p "$_builddir"
	echo "-$pkgrel-linux" > "$_builddir"/localversion-alpine \
		|| return 1

	cp "$srcdir"/$_config "$_builddir"/.config
	make -C "$srcdir"/linux-$_kernver \
		O="$_builddir" \
		ARCH="$_carch" \
		CROSS_COMPILE="$_cross" \
		LLVM=1 LD=/usr/bin/ld.lld listnewconfig oldconfig
}

nconfig() {
	local _builddir="$srcdir"/build-linux.$_carch
	make -C "$srcdir"/linux-$_kernver \
		O="$_builddir" \
		ARCH="$_carch" \
		CROSS_COMPILE="$_cross" \
		LLVM=1 LD=/usr/bin/ld.lld menuconfig
}

build() {
	unset LDFLAGS
	export KBUILD_BUILD_TIMESTAMP="$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})"
	cd "$srcdir"/build-linux.$_carch
	make -j5 LLVM=1 LD=/usr/bin/ld.lld LOADADDR=2000000 ARCH="$_carch" CROSS_COMPILE="$_cross"
}

_package() {
	local _outdir="$1"
	export KBUILD_BUILD_TIMESTAMP="$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})"

	cd "$srcdir"/build-linux.$_carch
	# modules_install seems to regenerate a defect Modules.symvers on s390x. Work
	# around it by backing it up and restore it after modules_install
	#cp Module.symvers Module.symvers.backup

	mkdir -p "$_outdir"/boot "$_outdir"/boot/dtbs "$_outdir"/lib/modules

	#cat arch/arm/boot/Image | gzip -9 > Image.gz
	install -Dm644 "arch/arm/boot/zImage" "$pkgdir/boot/vmlinuz-$_kernver"
	#install -Dm644 Image.gz "$pkgdir/boot/vmlinuz-$_kernver.gz"
	install -Dm644 arch/arm/boot/dts/*.dtb "$pkgdir/boot/dtbs/"

	make LLVM=1 LD=/usr/bin/ld.lld INSTALL_MOD_STRIP=1 -j5 modules_install \
		ARCH="$_carch" \
		CROSS_COMPILE="$_cross" \
		INSTALL_MOD_PATH="$_outdir" V=1

	#cp Module.symvers.backup Module.symvers

	rm -f "$_outdir"/lib/modules/${_kernver}/build \
		"$_outdir"/lib/modules/${_kernver}/source
	rm -rf "$_outdir"/lib/firmware

	install -D -m644 include/config/kernel.release \
		"$_outdir"/usr/share/kernel/linux/kernel.release
}

# main flavor installs in $pkgdir
package() {
	depends="$depends linux-firmware-any"

	_package "$pkgdir"
}

_dev() {
	# copy the only the parts that we really need for build 3rd party
	# kernel modules and install those as /usr/src/linux-headers,
	# simlar to what ubuntu does
	#
	# this way you dont need to install the 300-400 kernel sources to
	# build a tiny kernel module
	#
	pkgdesc="Headers and script for third party modules for linux kernel"
	depends="$_depends_dev"
	local dir="$subpkgdir"/usr/src/linux-headers-${_kernver}
	export KBUILD_BUILD_TIMESTAMP="$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})"

	# first we import config, run prepare to set up for building
	# external modules, and create the scripts
	mkdir -p "$dir"
	cp "$srcdir"/build-linux.$_carch/.config "$dir"/.config
	echo "-$pkgrel-linux" > "$dir"/localversion-alpine

	make LLVM=1 -j1 -C "$srcdir"/linux-$_kernver O="$dir" ARCH="$_carch" CROSS_COMPILE="$_cross" \
		syncconfig prepare modules_prepare scripts

	# remove the stuff that points to real sources. we want 3rd party
	# modules to believe this is the soruces
	rm "$dir"/Makefile "$dir"/source

	# copy the needed stuff from real sources
	#
	# this is taken from ubuntu kernel build script
	# http://kernel.ubuntu.com/git/ubuntu/ubuntu-zesty.git/tree/debian/rules.d/3-binary-indep.mk
	cd "$srcdir"/linux-$_kernver
	find .  -path './include/*' -prune \
		-o -path './scripts/*' -prune -o -type f \
		\( -name 'Makefile*' -o -name 'Kconfig*' -o -name 'Kbuild*' -o \
		   -name '*.sh' -o -name '*.pl' -o -name '*.lds' -o -name 'Platform' \) \
		-print | cpio -pdm "$dir"

	cp -a scripts include "$dir"

	find $(find arch -name include -type d -print) -type f \
		| cpio -pdm "$dir"

	install -Dm644 "$srcdir"/build-linux.$_carch/Module.symvers \
		"$dir"/Module.symvers

	mkdir -p "$subpkgdir"/lib/modules/${_kernver}
	ln -sf /usr/src/linux-headers-${_kernver} \
		"$subpkgdir"/lib/modules/${_kernver}/build
}

sha512sums="
96a6076cfd6b5c11333d348ea0519bc59c2ca12c51f383b58854703bcd7edde616045c431778b94094fe6b52ac52d04d5dcf4e674c2c3b9107bfcb38d4d3de24  config-linux.arm
"
