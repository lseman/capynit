pkgname=capynit
_encrypt=true
pkgver=0.1
pkgrel=1
pkgdesc="Custom capybara initramfs"
arch=('x86_64')
license=('GPL')
depends=('pax-utils')
makedepends=('git' 'libxo' 'docbook-xml' 'docbook-xsl' "xmlto")
source=("git+https://github.com/chimera-linux/chimerautils.git"
        "git+https://github.com/robxu9/bash-static.git"
        "git+https://anongit.gentoo.org/git/proj/pax-utils.git"
        init)
md5sums=('SKIP'
         'SKIP'
         'SKIP'
         'c78fc1acc5f01752dd5dce6a505a357f')
options=('!strip' '!emptydirs' 'staticlibs' '!lto')
prepare() {
    # arch stuff for lddtree
    cd "$srcdir/pax-utils"
    sed -E 's|(  paths =) \[\]|\1 ["/usr/lib"]|g' -i lddtree.py
}

build() {
    # build chimerautils
    cd "$srcdir/chimerautils"
    pwd
    mkdir -p build
    cd build
    arch-meson ..
    ninja

    # build pax-utils
    cd "$srcdir/pax-utils"
    arch-meson build \
        -Duse_seccomp=true \
        -Dlddtree_implementation=python \
        -Duse_fuzzing=false
    meson compile -C build

    # build bash-static
    cd "$srcdir/bash-static"
    pwd
    bash build.sh linux 0
}

package() {

    capypath=${pkgdir}/opt/capynit
    mkdir -p $capypath

    mkdir --parents $capypath/{dev,mnt/root,proc,root,sys,usr}
    cp --archive /dev/{null,console,tty} $capypath/dev/
    mkdir -p $capypath/usr/{bin,lib,lib64,etc}
    # create symbolic links
    # bin point to usr/bin
    # sbin point to usr/bin
    # lib point to usr/lib
    # lib64 point to usr/lib
    cd $capypath
    ln -s usr/bin bin
    ln -s usr/bin sbin
    ln -s usr/lib lib
    ln -s usr/lib lib64
    ln -s usr/etc etc
    cd usr
    ln -s bin sbin

    cd "$srcdir/chimerautils"
    cd build
    DESTDIR="$capypath" meson install

    PAXUTIL=${srcdir}/pax-utils
    binlits=("modprobe" "depmod" "insmod" "modinfo" "reboot" "switch_root" "mount" "umount" "fsck" "findfs")

    # if _encrypt is true, then we need to append more utils to binlits
    if $_encrypt; then
        binlits+=("cryptsetup" "dmsetup")
    fi

    for binlit in "${binlits[@]}"; do
        bindir=$(which $binlit)
        $PAXUTIL/lddtree.py --copy-to-tree $capypath $bindir
    done

    cp $srcdir/bash-static/releases/bash $capypath/usr/bin/bash
    cp $srcdir/init $capypath/init

    # if _encrypt is true, then we need to add some lines to init
    if $_encrypt; then
        # we change ENCRYPTED_ROOTFS to true in init
        sed -i 's/ENCRYPTED_ROOTFS=false/ENCRYPTED_ROOTFS=true/g' $capypath/init
    fi

}
