pkgname=pueue
pkgver=3.2.0
pkgrel=1
pkgdesc="A CLI tool for managing long running shell commands"
arch=('aarch64')
url="https://github.com/nukesor/pueue"
license=('MIT')
source=("$pkgname-$pkgver.tar.gz::$url/archive/v$pkgver.tar.gz")
sha256sums=('0c3126579661f894fb02a0d8c0e138ab23b277e97cea2d85e48d3d2b9fb1c372')

TERMUX_PREFIX='/data/data/com.termux/files/usr'

prepare() {
  cd "$pkgname-$pkgver"

  rustup target add aarch64-linux-android
  cargo fetch --target "$CARCH-unknown-linux-gnu" --locked

  # Patches
  for d in ~/.cargo/registry/src/index.crates.io-*/libproc-*; do
    patch --silent --strip 1 --directory ${d} < "${startdir}/crates-libproc-for-android.diff" || :
  done
  patch --silent --strip 1 --input "${startdir}/patches.diff"
}

build() {
  cd "$pkgname-$pkgver"

  export AR_aarch64_linux_android='/opt/android-ndk/toolchains/llvm/prebuilt/linux-x86_64/bin/llvm-ar'

  export CFLAGS=''
  CFLAGS+=' -fstack-protector-strong'
  CFLAGS+=' -Oz'
  CFLAGS+=" -I${TERMUX_PREFIX}/include"

  export RUSTFLAGS="-C link-args=-Wl,-rpath=${TERMUX_PREFIX}/lib"

  cargo build \
    --target aarch64-linux-android \
    --locked \
    --release
}

package() {
  cd "$pkgname-$pkgver"
  install -Dm700 -t "${pkgdir}${TERMUX_PREFIX}/bin" target/aarch64-linux-android/release/pueue
  install -Dm700 -t "${pkgdir}${TERMUX_PREFIX}/bin" target/aarch64-linux-android/release/pueued
}
