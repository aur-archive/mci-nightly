# Maintainer: Alex Rønne Petersen <alex@lycus.org>

pkgname=("mci-nightly")
pkgdesc="The Managed Compiler Infrastructure: A modern and intuitive compiler infrastructure."
url="http://lycus.org"
license=("MIT")
arch=("i686" "x86_64")
pkgver="2012_06_06"
_relver="1.0"
pkgrel="4"

options=('!strip')
depends=("python-waf" "dmd" "glibc" "libffi" "gc" "python-sphinx" "texlive-core" "texlive-formatsextra")
provides=("mci-cli")
checkdepends=("valgrind" "gdb")

if [ $CARCH == "x86_64" ]; then
    _lp64="true"

    source=("http://nightlies.lycus.org/MCI-1.0-Linux-x86-64-Source-Debug-2012-06-06-04-33.tar.bz2")
    md5sums=("c37ec948061122f7bd0069e9329c1681")
elif [ $CARCH == "i686" ]; then
    _lp64="false"

    source=("http://nightlies.lycus.org/MCI-1.0-Linux-x86-32-Source-Debug-2012-06-06-04-33.tar.bz2")
    md5sums=("f95bd8067e8f6a3ed42b06cb9b67c56d")
fi

build() {
    cd "$srcdir/MCI-$_relver"

    msg "MCI: Configuring the build system"
    waf configure --mode=debug --lp64=$_lp64 --valgrind=true --check-d-compiler=dmd

    msg "MCI: Building the sources and embedded dependencies"
    waf build

    msg "MCI: Building the infrastructure documentation"
    waf docs

    msg "MCI: Building Ddoc documentation for the API"
    waf ddoc
}

check() {
    cd "$srcdir/MCI-$_relver/libffi-d/tests"

    msg "MCI: Testing embedded libffi-d"
    ./test.sh

    cd "$srcdir/MCI-$_relver/libgc-d/tests"

    msg "MCI: Testing embedded libgc-d"
    ./test.sh

    cd "$srcdir/MCI-$_relver"

    msg "MCI: Running unit tests"
    waf unittest

    msg "MCI: Running the test suite"
    waf test
}

package() {
    msg "MCI: Installing embedded dependencies"
    install -Dm644 "$srcdir/MCI-$_relver/build/libffi-d/libffi-d.a" "$pkgdir/usr/lib/libffi-d.a"
    install -Dm644 "$srcdir/MCI-$_relver/build/libgc-d/libgc-d.a" "$pkgdir/usr/lib/libgc-d.a"

    msg "MCI: Installing static libraries"
    install -Dm644 "$srcdir/MCI-$_relver/build/libmci.core.a" "$pkgdir/usr/lib/libmci.core.a"
    install -Dm644 "$srcdir/MCI-$_relver/build/libmci.assembler.a" "$pkgdir/usr/lib/libmci.assembler.a"
    install -Dm644 "$srcdir/MCI-$_relver/build/libmci.verifier.a" "$pkgdir/usr/lib/libmci.verifier.a"
    install -Dm644 "$srcdir/MCI-$_relver/build/libmci.optimizer.a" "$pkgdir/usr/lib/libmci.optimizer.a"
    install -Dm644 "$srcdir/MCI-$_relver/build/libmci.linker.a" "$pkgdir/usr/lib/libmci.linker.a"
    install -Dm644 "$srcdir/MCI-$_relver/build/libmci.debugger.a" "$pkgdir/usr/lib/libmci.debugger.a"
    install -Dm644 "$srcdir/MCI-$_relver/build/libmci.vm.a" "$pkgdir/usr/lib/libmci.vm.a"
    install -Dm644 "$srcdir/MCI-$_relver/build/libmci.compiler.a" "$pkgdir/usr/lib/libmci.compiler.a"
    install -Dm644 "$srcdir/MCI-$_relver/build/libmci.jit.a" "$pkgdir/usr/lib/libmci.jit.a"

    msg "MCI: Installing the command line interface"
    install -Dm755 "$srcdir/MCI-$_relver/build/mci" "$pkgdir/usr/bin/mci"

    mkdir -p "$pkgdir/usr/share/doc/d"

    msg "MCI: Installing the infrastructure documentation"
    cp -r "$srcdir/MCI-$_relver/docs/_build/html" "$pkgdir/usr/share/doc/d/mci"
    rm -rf "$pkgdir/usr/share/doc/d/mci/_sources"
    find "$pkgdir/usr/share/doc/d/mci" -type f | xargs chmod 0644

    msg "MCI: Installing Ddoc documentation for the API"
    cp -r "$srcdir/MCI-$_relver/docs/_ddoc" "$pkgdir/usr/share/doc/d/mci/ddoc"
    find "$pkgdir/usr/share/doc/d/mci/ddoc" -type f | xargs chmod 0644
}
