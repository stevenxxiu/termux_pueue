pkgname=pueue
pkgver=3.1.2
pkgrel=1
pkgdesc="A CLI tool for managing long running shell commands"
arch=('aarch64')
url="https://github.com/nukesor/pueue"
license=('MIT')
makedepends=('cargo' 'git')
source=("$pkgname-$pkgver.tar.gz::$url/archive/v$pkgver.tar.gz")
sha256sums=('653eac9b7fc111cc4b9bddacbbf514932a8d273a059b20b1cc66af74e500eb5e')

TERMUX_PREFIX='/data/data/com.termux/files/usr'

prepare() {
  cd "$pkgname-$pkgver"

  rustup target add aarch64-linux-android
  cargo fetch --target "$CARCH-unknown-linux-gnu" --locked

  # Patches
  for d in ~/.cargo/registry/src/github.com-*/libproc-*; do
    patch --silent --strip 1 --directory ${d} < "${startdir}/crates-libproc-for-android.diff" || :
  done
  patch --silent --strip 1 --input "${startdir}/patches.diff"
}

build() {
  cd "$pkgname-$pkgver"

  export PATH="/opt/android-ndk/toolchains/llvm/prebuilt/linux-x86_64/bin:$PATH"
  export AR_aarch64_linux_android='/opt/android-ndk/toolchains/llvm/prebuilt/linux-x86_64/bin/llvm-ar'

  export CFLAGS=''
  CFLAGS+=' -fstack-protector-strong'
  CFLAGS+=' -Oz'
  CFLAGS+=" -I${HOME}/aarch64${TERMUX_PREFIX}/include"

  export RUSTFLAGS="-C link-args=-Wl,-rpath=${TERMUX_PREFIX}/lib"

  cargo build \
    --target aarch64-linux-android \
    --config target.aarch64-linux-android.linker=\'aarch64-linux-android33-clang\' \
    --locked \
    --release
}

package() {
  cd "$pkgname-$pkgver"
  install -Dm700 -t "${pkgdir}${TERMUX_PREFIX}/bin" target/aarch64-linux-android/release/pueue
  install -Dm700 -t "${pkgdir}${TERMUX_PREFIX}/bin" target/aarch64-linux-android/release/pueued
}
