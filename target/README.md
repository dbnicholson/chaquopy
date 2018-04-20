## Crystax NDK

Extract Crystax 10.3.2 to $CRYSTAX_DIR.

Rename all the libcrystax.* files under $CRYSTAX_DIR/sources/crystax to libcrystax.*.original.

Clone the following Crystax repositories:

    vendor/dlmalloc
    vendor/freebsd
    vendor/libkqueue
    vendor/libpwq
    platform/bionic
    platform/ndk
    platform/system/core
    toolchain/llvm-3.6/compiler-rt

Check them all out on the branch crystax-r10.

cd platform/ndk

Apply 5372.patch for issue #5372 (Crystax issue #1455).

cd sources/crystax
NDK=$CRYSTAX_DIR ABIS=armeabi-v7a,x86 make

Copy the resulting libcrystax.* files to $CRYSTAX_DIR/sources/crystax.


## OpenSSL build

Extract OpenSSL source to $OPENSSL_DIR

$CRYSTAX_DIR/build/tools/build-target-openssl.sh --verbose --abis=armeabi-v7a,x86 $OPENSSL_DIR

The OpenSSL libraries and includes will now be in $CRYSTAX_DIR/sources/openssl/<version>. Copy the
Android.mk from sources/openssl/1.0.1p to this subdirectory.


## Python build

Extract Python source to $PYTHON_DIR

cd target/crystax

For Python 3.6, run the following extra commands, derived from
https://github.com/inclement/crystax_python_builds:

    patch -t -d $PYTHON_DIR -p1 -i patch_python3.6.patch
    cp android.mk.3.6 config.c.3.6 interpreter.c.3.6 $CRYSTAX_DIR/build/tools/build-target-python
    mkdir $CRYSTAX_DIR/sources/python/3.6
    cp sources-Android.mk.3.6 $CRYSTAX_DIR/sources/python/3.6/Android.mk

export OPENSSL_VERSION=<version built above>
./build-target-python.sh --verbose --abis=armeabi-v7a,x86 $PYTHON_DIR

The Python libraries and includes will now be in $CRYSTAX_DIR/sources/python. These should be
packaged for the Maven repository by package-target.sh. If adding a new Python x.y version,
they will also need to be copied to any other machines where Chaqupy itself is built.