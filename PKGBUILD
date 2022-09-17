# Maintainer: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Maintainer: Pinghigh <pinghigh24678@outlook.com>
pkgbase=linux-tikogas
major=5.19
pkgrel=1
pkgdesc='Linux'
gitver=5.19.9
pkgver=v${gitver}
_srctag=v${pkgver%.*}-${pkgver##*.}
_branch=5.x
url="https://github.com/torvalds/linux"
arch=(x86_64)
license=(GPL2)
makedepends=(
  bc libelf pahole cpio perl tar xz clang lld initramfs kmod
  xmlto llvm-libs python-sphinx python-sphinx_rtd_theme graphviz imagemagick llvm git
)
options=('!strip')
_srcname="linux-${gitver}"
source=(
  "https://cdn.kernel.org/pub/linux/kernel/v${_branch}/linux-${gitver}.tar."{xz,sign}
  "clearlinux-linux::git+https://github.com/clearlinux-pkgs/linux"
  config        # the main kernel config file
  "linux-patches::git+https://github.com/xanmod/linux-patches"
  "https://raw.githubusercontent.com/zhmars/cjktty-patches/master/v5.x/cjktty-${major}.patch"
  "https://raw.githubusercontent.com/CachyOS/kernel-patches/master/5.19/sched/0001-prjc.patch"
  "https://raw.githubusercontent.com/graysky2/kernel_compiler_patch/master/more-uarches-for-kernel-5.17%2B.patch"
)
validpgpkeys=(
  'ABAF11C65A2970B130ABE3C479BE3E4300411886'  # Linus Torvalds
  # '647F28654894E3BD457199BE38DBBDC86092693E'  # Greg Kroah-Hartman
  # 'A2FF3A36AAA56654109064AB19802F8B0D70FC30'  # Jan Alexander Steffens (heftig)
  'C7E7849466FE2358343588377258734B41C31549'  # David Runge <dvzrv@archlinux.org>
)
sha256sums=('SKIP'
            'SKIP'
            'SKIP'
            '44013e78d70e0ed47ed5bcef7de4e9c315cad9ff3bd46234a5c23f0928d642f7'
            'SKIP'
            # 'fc28710ae5ca788ff3f6f5812b9156178a9b6a6c9229b5414656e306e5a3ff1d'
            'bc356a90997256b189153fd804a791d7a0fcd20fc9c0369396a2f5194ab32579'
            '52755f984d28054c026b79a455d60c184b9b3f978ca315a5483d812598b05a72'
            '2b0ffe6ee835c57043f6b762c8c4bbd51c4ab82be197ac3f2974cf075999d8d9'
            '289d6a24266fd992e4cc87065016ef22955ca0c98945a2594d38749cf479d04d')

export KBUILD_BUILD_HOST=archlinux
export KBUILD_BUILD_USER=$pkgbase
export KBUILD_BUILD_TIMESTAMP="$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})"

prepare() {
  cp clearlinux-linux/*.patch ./
  cp linux-patches/linux-${major}.y-xanmod/xanmod/*.patch ./
  cp linux-patches/linux-${major}.y-xanmod/wine/*.patch ./
  cd $_srcname

  echo "Setting version..."
  scripts/setlocalversion --save-scmversion
  echo "${pkgbase#linux}" > localversion.20-pkgname

  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    [[ $src = *.patch ]] || continue
    echo "Applying patch $src..."
    patch -Np1 < "../$src"
  done

  echo "Setting config..."
          cp ../config .config
          echo "Enabling LLVM THIN LTO..."
          scripts/config --enable LTO \
              --enable LTO_CLANG \
              --enable ARCH_SUPPORTS_LTO_CLANG \
              --enable ARCH_SUPPORTS_LTO_CLANG_THIN \
              --disable LTO_NONE \
              --enable HAS_LTO_CLANG \
              --disable LTO_CLANG_FULL \
              --enable LTO_CLANG_THIN \
              --enable HAVE_GCC_PLUGINS
          scripts/config --enable CONFIG_LTO
          scripts/config --enable CONFIG_LTO_CLANG
          scripts/config --enable CONFIG_ARCH_SUPPORTS_LTO_CLANG
          scripts/config --enable CONFIG_ARCH_SUPPORTS_LTO_CLANG_THIN
          scripts/config --enable CONFIG_HAS_LTO_CLANG
          scripts/config --disable CONFIG_LTO_NONE
          scripts/config --disable CONFIG_LTO_CLANG_FULL
          scripts/config --enable CONFIG_LTO_CLANG_THIN
          echo "Disabling NUMA from kernel config..."
          scripts/config --disable NUMA \
                --disable AMD_NUMA \
                --disable X86_64_ACPI_NUMA \
                --disable NODES_SPAN_OTHER_NODES \
                --disable NUMA_EMU \
                --disable NEED_MULTIPLE_NODES \
                --disable USE_PERCPU_NUMA_NODE_ID \
                --disable ACPI_NUMA \
                --disable ARCH_SUPPORTS_NUMA_BALANCING \
                --disable NODES_SHIFT \
                --undefine NODES_SHIFT \
                --disable NEED_MULTIPLE_NODES
          echo "Enabling Linux Random Number Generator ..."
          scripts/config --disable CONFIG_RANDOM_DEFAULT_IMPL
          scripts/config --enable CONFIG_LRNG
          scripts/config --enable CONFIG_LRNG_SHA256
          scripts/config --enable CONFIG_LRNG_COMMON_DEV_IF
          scripts/config --enable CONFIG_LRNG_DRNG_ATOMIC
          scripts/config --enable CONFIG_LRNG_SYSCTL
          scripts/config --enable CONFIG_LRNG_RANDOM_IF
          scripts/config --module CONFIG_LRNG_KCAPI_IF
          scripts/config --module CONFIG_LRNG_HWRAND_IF
          scripts/config --enable CONFIG_LRNG_DEV_IF
          scripts/config --enable CONFIG_LRNG_RUNTIME_ES_CONFIG
          scripts/config --enable CONFIG_LRNG_IRQ_DFLT_TIMER_ES
          scripts/config --disable CONFIG_LRNG_SCHED_DFLT_TIMER_ES
          scripts/config --enable CONFIG_LRNG_TIMER_COMMON
          scripts/config --disable CONFIG_LRNG_COLLECTION_SIZE_256
          scripts/config --disable CONFIG_LRNG_COLLECTION_SIZE_512
          scripts/config --enable CONFIG_LRNG_COLLECTION_SIZE_1024
          scripts/config --disable CONFIG_LRNG_COLLECTION_SIZE_2048
          scripts/config --disable CONFIG_LRNG_COLLECTION_SIZE_4096
          scripts/config --disable CONFIG_LRNG_COLLECTION_SIZE_8192
          scripts/config --set-val CONFIG_LRNG_COLLECTION_SIZE 1024
          scripts/config --enable CONFIG_LRNG_HEALTH_TESTS
          scripts/config --set-val CONFIG_LRNG_RCT_CUTOFF 31
          scripts/config --set-val CONFIG_LRNG_APT_CUTOFF 325
          scripts/config --enable CONFIG_LRNG_IRQ
          scripts/config --enable CONFIG_LRNG_CONTINUOUS_COMPRESSION_ENABLED
          scripts/config --disable CONFIG_LRNG_CONTINUOUS_COMPRESSION_DISABLED
          scripts/config --enable CONFIG_LRNG_ENABLE_CONTINUOUS_COMPRESSION
          scripts/config --enable CONFIG_LRNG_SWITCHABLE_CONTINUOUS_COMPRESSION
          scripts/config --set-val CONFIG_LRNG_IRQ_ENTROPY_RATE 256
          scripts/config --enable CONFIG_LRNG_JENT
          scripts/config --set-val CONFIG_LRNG_JENT_ENTROPY_RATE 16
          scripts/config --enable CONFIG_LRNG_CPU
          scripts/config --set-val CONFIG_LRNG_CPU_FULL_ENT_MULTIPLIER 1
          scripts/config --set-val CONFIG_LRNG_CPU_ENTROPY_RATE 8
          scripts/config --enable CONFIG_LRNG_SCHED
          scripts/config --set-val CONFIG_LRNG_SCHED_ENTROPY_RATE 4294967295
          scripts/config --enable CONFIG_LRNG_DRNG_CHACHA20
          scripts/config --module CONFIG_LRNG_DRBG
          scripts/config --module CONFIG_LRNG_DRNG_KCAPI
          scripts/config --enable CONFIG_LRNG_SWITCH
          scripts/config --enable CONFIG_LRNG_SWITCH_HASH
          scripts/config --module CONFIG_LRNG_HASH_KCAPI
          scripts/config --enable CONFIG_LRNG_SWITCH_DRNG
          scripts/config --module CONFIG_LRNG_SWITCH_DRBG
          scripts/config --module CONFIG_LRNG_SWITCH_DRNG_KCAPI
          scripts/config --enable CONFIG_LRNG_DFLT_DRNG_CHACHA20
          scripts/config --disable CONFIG_LRNG_DFLT_DRNG_DRBG
          scripts/config --disable CONFIG_LRNG_DFLT_DRNG_KCAPI
          scripts/config --enable CONFIG_LRNG_TESTING_MENU
          scripts/config --disable CONFIG_LRNG_RAW_HIRES_ENTROPY
          scripts/config --disable CONFIG_LRNG_RAW_JIFFIES_ENTROPY
          scripts/config --disable CONFIG_LRNG_RAW_IRQ_ENTROPY
          scripts/config --disable CONFIG_LRNG_RAW_RETIP_ENTROPY
          scripts/config --disable CONFIG_LRNG_RAW_REGS_ENTROPY
          scripts/config --disable CONFIG_LRNG_RAW_ARRAY
          scripts/config --disable CONFIG_LRNG_IRQ_PERF
          scripts/config --disable CONFIG_LRNG_RAW_SCHED_HIRES_ENTROPY
          scripts/config --disable CONFIG_LRNG_RAW_SCHED_PID_ENTROPY
          scripts/config --disable CONFIG_LRNG_RAW_SCHED_START_TIME_ENTROPY
          scripts/config --disable CONFIG_LRNG_RAW_SCHED_NVCSW_ENTROPY
          scripts/config --disable CONFIG_LRNG_SCHED_PERF
          scripts/config --disable CONFIG_LRNG_ACVT_HASH
          scripts/config --disable CONFIG_LRNG_RUNTIME_MAX_WO_RESEED_CONFIG
          scripts/config --disable CONFIG_LRNG_TEST_CPU_ES_COMPRESSION
          scripts/config --enable CONFIG_LRNG_SELFTEST
          scripts/config --disable CONFIG_LRNG_SELFTEST_PANIC2
          scripts/config --disable ZRAM_DEF_COMP_LZORLE \
              --enable ZRAM_DEF_COMP_ZSTD \
              --set-str ZRAM_DEF_COMP zstd \
              --disable ZSWAP_COMPRESSOR_DEFAULT_LZ4 \
              --enable ZSWAP_COMPRESSOR_DEFAULT_ZSTD \
              --set-str ZSWAP_COMPRESSOR_DEFAULT zstd \
              --enable ZRAM_ENTROPY \
              --set-val ZRAM_ENTROPY_THRESHOLD 100000
          echo "Enabling zram ZSTD compression..."
          scripts/config --disable ZRAM_DEF_COMP_LZORLE \
              --enable ZRAM_DEF_COMP_ZSTD \
              --set-str ZRAM_DEF_COMP zstd \
              --disable ZSWAP_COMPRESSOR_DEFAULT_LZ4 \
              --enable ZSWAP_COMPRESSOR_DEFAULT_ZSTD \
              --set-str ZSWAP_COMPRESSOR_DEFAULT zstd \
              --enable ZRAM_ENTROPY \
              --set-val ZRAM_ENTROPY_THRESHOLD 100000
          scripts/config --disable CC_OPTIMIZE_FOR_PERFORMANCE \
              --enable CC_OPTIMIZE_FOR_PERFORMANCE_O3
          msg2 "Enable AMD Processor P-State driver"
          scripts/config --enable CONFIG_X86_AMD_PSTATE
          msg2 "Add anbox support"
          scripts/config --enable CONFIG_ASHMEM
          # CONFIG_ION is not set
          scripts/config --enable CONFIG_ANDROID
          scripts/config --enable CONFIG_ANDROID_BINDER_IPC
          scripts/config --enable CONFIG_ANDROID_BINDERFS
          scripts/config --set-str CONFIG_ANDROID_BINDER_DEVICES "binder,hwbinder,vndbinder"
          # CONFIG_ANDROID_BINDER_IPC_SELFTEST is not set
          msg2 "Enable PERF_EVENTS_AMD_BRS"
          scripts/config --enable CONFIG_PERF_EVENTS_AMD_BRS
          msg2 "Enable Winesync"
          scripts/config --enable CONFIG_WINESYNC

          msg2 "Enable CONFIG_SCHED_ALT, this feature enable alternative CPU scheduler"
          scripts/config --enable CONFIG_SCHED_ALT
          echo "Enable CJK TTY"
          scripts/config --enable FONT_CJK_16x16
          msg2 "Enable PDS CPU scheduler"
          scripts/config --disable CONFIG_SCHED_BMQ
          scripts/config --enable CONFIG_SCHED_PDS
          msg2 "Enable Winesync"
          scripts/config --enable CONFIG_WINESYNC
  make oldconfig && make prepare
  diff -u ../config .config || :

  make -s kernelrelease > version
  echo "Prepared $pkgbase version $(<version)"
}

build() {
  cd $_srcname
  CFLAGS="-march=znver2 -O3"
  CXXFLAGS="${CFLAGS}"
  make LLVM=1 LLVM_IAS=1 CFLAGS="${CFLAGS}" CXXFLAGS="${CFLAGS}" -j16
}

_package() {
  pkgdesc="The $pkgdesc kernel and modules"
  depends=()
  optdepends=('wireless-regdb: to set the correct wireless channels of your country'
              'linux-firmware: firmware images needed for some devices')
  provides=(VIRTUALBOX-GUEST-MODULES WIREGUARD-MODULE)
  replaces=(virtualbox-guest-modules-arch wireguard-arch)

  cd $_srcname
  local kernver="$(<version)"
  local modulesdir="$pkgdir/usr/lib/modules/$kernver"

  echo "Installing boot image..."
  # systemd expects to find the kernel here to allow hibernation
  # https://github.com/systemd/systemd/commit/edda44605f06a41fb86b7ab8128dcf99161d2344
  install -Dm644 "$(make -s image_name)" "$modulesdir/vmlinuz"

  # Used by mkinitcpio to name the kernel
  echo "$pkgbase" | install -Dm644 /dev/stdin "$modulesdir/pkgbase"

  echo "Installing modules..."
  make INSTALL_MOD_PATH="$pkgdir/usr" INSTALL_MOD_STRIP=1 \
    DEPMOD=/doesnt/exist modules_install  # Suppress depmod

  # remove build and source links
  rm "$modulesdir"/{source,build}
}

_package-headers() {
  pkgdesc="Headers and scripts for building modules for the $pkgdesc kernel"
  depends=(pahole)

  cd $_srcname
  local builddir="$pkgdir/usr/lib/modules/$(<version)/build"

  echo "Installing build files..."
  install -Dt "$builddir" -m644 .config Makefile Module.symvers System.map \
    localversion.* version vmlinux
  install -Dt "$builddir/kernel" -m644 kernel/Makefile
  install -Dt "$builddir/arch/x86" -m644 arch/x86/Makefile
  cp -t "$builddir" -a scripts

  # required when STACK_VALIDATION is enabled
  install -Dt "$builddir/tools/objtool" tools/objtool/objtool

  # required when DEBUG_INFO_BTF_MODULES is enabled
  install -Dt "$builddir/tools/bpf/resolve_btfids" tools/bpf/resolve_btfids/resolve_btfids

  echo "Installing headers..."
  cp -t "$builddir" -a include
  cp -t "$builddir/arch/x86" -a arch/x86/include
  install -Dt "$builddir/arch/x86/kernel" -m644 arch/x86/kernel/asm-offsets.s

  install -Dt "$builddir/drivers/md" -m644 drivers/md/*.h
  install -Dt "$builddir/net/mac80211" -m644 net/mac80211/*.h

  # https://bugs.archlinux.org/task/13146
  install -Dt "$builddir/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # https://bugs.archlinux.org/task/20402
  install -Dt "$builddir/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "$builddir/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "$builddir/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  # https://bugs.archlinux.org/task/71392
  install -Dt "$builddir/drivers/iio/common/hid-sensors" -m644 drivers/iio/common/hid-sensors/*.h

  echo "Installing KConfig files..."
  find . -name 'Kconfig*' -exec install -Dm644 {} "$builddir/{}" \;

  echo "Removing unneeded architectures..."
  local arch
  for arch in "$builddir"/arch/*/; do
    [[ $arch = */x86/ ]] && continue
    echo "Removing $(basename "$arch")"
    rm -r "$arch"
  done

  echo "Removing documentation..."
  rm -r "$builddir/Documentation"

  echo "Removing broken symlinks..."
  find -L "$builddir" -type l -printf 'Removing %P\n' -delete

  echo "Removing loose objects..."
  find "$builddir" -type f -name '*.o' -printf 'Removing %P\n' -delete

  echo "Stripping build tools..."
  local file
  while read -rd '' file; do
    case "$(file -bi "$file")" in
      application/x-sharedlib\;*)      # Libraries (.so)
        strip -v $STRIP_SHARED "$file" ;;
      application/x-archive\;*)        # Libraries (.a)
        strip -v $STRIP_STATIC "$file" ;;
      application/x-executable\;*)     # Binaries
        strip -v $STRIP_BINARIES "$file" ;;
      application/x-pie-executable\;*) # Relocatable binaries
        strip -v $STRIP_SHARED "$file" ;;
    esac
  done < <(find "$builddir" -type f -perm -u+x ! -name vmlinux -print0)

  echo "Stripping vmlinux..."
  strip -v $STRIP_STATIC "$builddir/vmlinux"

  echo "Adding symlink..."
  mkdir -p "$pkgdir/usr/src"
  ln -sr "$builddir" "$pkgdir/usr/src/$pkgbase"
}

pkgname=("$pkgbase" "$pkgbase-headers")
for _p in "${pkgname[@]}"; do
  eval "package_$_p() {
    $(declare -f "_package${_p#$pkgbase}")
    _package${_p#$pkgbase}
  }"
done

# vim:set ts=8 sts=2 sw=2 et:
