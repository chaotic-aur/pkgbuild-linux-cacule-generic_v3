# Maintainer: Peter Jung ptr1337 <admin@ptr1337.dev>

### BUILD OPTIONS
# Set the next two variables to ANYTHING that is not null to enable them
### BUILD OPTIONS
# Set these variables to ANYTHING that is not null to enable them

# NUMA is optimized for multi-socket motherboards.
# A single multi-core CPU actually runs slower with NUMA enabled.
# See, https://bugs.archlinux.org/task/31187
_NUMAdisable=y
# Enable fsync
_fsync=y
#enable futex2
_futex2=y
#enable winesync
_winesync=y
### Set performance governor as default
_per_gov=y
### Disable Deadline I/O scheduler
_deadline_disable=y
### Disable Kyber I/O scheduler
_kyber_disable=y
### Running with a 2000 HZ, 1000HZ or 500HZ tick rate
_2k_HZ_ticks=
_1k_HZ_ticks=y
_500_HZ_ticks=
### Enable protect file mappings under memory pressure
_mm_protect=y
### Enable multigenerational LRU
_lru_enable=y
# Tweak kernel options prior to a build via nconfig
_makenconfig=

# Only compile active modules to VASTLY reduce the number of modules built and
# the build time.
#
# To keep track of which modules are needed for your specific system/hardware,
# give module_db a try: https://aur.archlinux.org/packages/modprobed-db
# This PKGBUILD reads the database kept if it exists
#
# More at this wiki page ---> https://wiki.archlinux.org/index.php/Modprobed-db
_localmodcfg=

# Use the current kernel's .config file
# Enabling this option will use the .config of the RUNNING kernel rather than
# the ARCH defaults. Useful when the package gets updated and you already went
# through the trouble of customizing your config options.  NOT recommended when
# a new kernel is released, but again, convenient for package bumps.
_use_current=

### IMPORTANT: Do no edit below this line unless you know what you're doing

_major=5.13
#_minor=0
_srcname=linux-${_major}
pkgbase=linux-cacule-generic_v3
#pkgver=${_major}.${_minor}
pkgver=${_major}
pkgrel=4
pkgdesc='Linux-CacULE Kernel by Hamad Marri and with some other patchsets'
arch=('x86_64')
url="https://github.com/hamadmarri/cacule-cpu-scheduler"
license=('GPL2')
makedepends=('kmod' 'bc' 'libelf' 'python-sphinx' 'python-sphinx_rtd_theme'
             'graphviz' 'imagemagick' 'pahole' 'cpio' 'perl' 'tar' 'xz')
options=('!strip')
_patchsource="https://raw.githubusercontent.com/ptr1337/linux-cacule-aur/master/patches/5.13"
_caculepatches="https://raw.githubusercontent.com/ptr1337/linux-cacule-aur/master/patches/CacULE"
source=(
  "https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-${_major}.tar.xz"
#  "https://cdn.kernel.org/pub/linux/kernel/v5.x/patch-${pkgver}.xz"
  "https://raw.githubusercontent.com/ptr1337/linux-cacule-aur/master/linux-cacule/config"
  "${_patchsource}/arch-patches/0001-ZEN-Add-sysctl-and-CONFIG-to-disallow-unprivileged-C.patch"
  "${_caculepatches}/v5.13/cacule-5.13.patch"
  "${_patchsource}/cpu-patches/0001-cpu-patches.patch"
  "${_patchsource}/futex-patches/0001-futex-resync-from-gitlab.collabora.com.patch"
  "${_patchsource}/futex2/0007-v5.13-futex2_interface.patch"
  "${_patchsource}/winesync/5.13-winesync.patch"
  "${_patchsource}/zen-patches/0001-zen-patches.patch"
  "${_patchsource}/lqx-patches/0001-zen-Allow-MSR-writes-by-default.patch"
  "${_patchsource}/bfq-patches/0001-bfq-patches.patch"
  "${_patchsource}/block-patches/0001-block-patches.patch"
  "${_patchsource}/fixes-miscellaneous/0001-fixes-miscellaneous.patch"
  "${_patchsource}/bbr2-patches/0001-bbr2-5.13-introduce-BBRv2.patch"
  "${_patchsource}/btrfs-patches/0001-btrfs-patches.patch"
  "${_patchsource}/android-patches/0001-android-export-symbold-and-enable-building-ashmem-an.patch"
  "${_patchsource}/pf-patches-v3/0001-pf-patches.patch"
  "${_patchsource}/lru-patches/0001-lru-patches.patch"
  "${_patchsource}/ntfs3-patches/0001-ntfs3-patches.patch"
  "${_patchsource}/security-2/hardened-patches.patch"
  "${_patchsource}/security-2/lrng-v41.patch"
  "${_patchsource}/security-patches/0001-security-patches.patch"
  "${_patchsource}/misc/nohzfull.patch"
  "${_patchsource}/misc/1000-tune-vm-mm-and-vfs-settings.patch"
  "${_patchsource}/misc/rcu-fixes-next.patch"
  "${_patchsource}/zstd-upstream-patches/0001-zstd-upstream-patches.patch"
  "${_patchsource}/clearlinux-patches/0001-clearlinux-patches.patch"
  "${_patchsource}/ksm-patches/0001-ksm-patches.patch"
  "${_patchsource}/v4l2loopback-patches/0001-v4l2loopback-patches.patch"
#  "${_patchsource}/initramfs-patches/0001-initramfs-patches.patch"
)

export KBUILD_BUILD_HOST=archlinux
export KBUILD_BUILD_USER=$pkgbase
export KBUILD_BUILD_TIMESTAMP="$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})"

prepare() {
    cd ${_srcname}

    ### Add upstream patches
#    echo "Add upstream patches"
#    patch -Np1 -i ../patch-${pkgver}

    ### Setting version
    echo "Setting version..."
    scripts/setlocalversion --save-scmversion
    echo "-$pkgrel" > localversion.10-pkgrel
    echo "${pkgbase#linux}" > localversion.20-pkgname

    local src
        for src in "${source[@]}"; do
        src="${src%%::*}"
        src="${src##*/}"
        [[ $src = *.patch ]] || continue
        echo "Applying patch $src..."
        patch -Np1 < "../$src"
    done

    ### Setting config
        echo "Setting config..."
      cp "${srcdir}"/config .config
      make olddefconfig
      ### CPU_ARCH SCRIPT ##
      scripts/config --disable CONFIG_GENERIC_CPU
      scripts/config --enable CONFIG_GENERIC_CPU3

      ### Optionally set tickrate to 2000HZ
        if [ -n "$_2k_HZ_ticks" ]; then
          echo "Setting tick rate to 2k..."
          scripts/config --disable CONFIG_HZ_300
          scripts/config --enable CONFIG_HZ_2000
          scripts/config --set-val CONFIG_HZ 2000
        fi

      ### Optionally set tickrate to 1000
  	     if [ -n "$_1k_HZ_ticks" ]; then
  		    echo "Setting tick rate to 1k..."
          scripts/config --disable CONFIG_HZ_300
          scripts/config --enable CONFIG_HZ_1000
          scripts/config --set-val CONFIG_HZ 1000
  	     fi

      ### Optionally set tickrate to 500HZ
        if [ -n "$_500_HZ_ticks" ]; then
          echo "Setting tick rate to 500HZ..."
          scripts/config --disable CONFIG_HZ_300
          scripts/config --enable CONFIG_HZ_500
          scripts/config --set-val CONFIG_HZ 500
        fi

    ### Optionally disable NUMA for 64-bit kernels only
      # (x86 kernels do not support NUMA)
        if [ -n "$_NUMAdisable" ]; then
          echo "Disabling NUMA from kernel config..."
          scripts/config --disable CONFIG_NUMA
        fi

        if [ -n "$_fsync" ]; then
          echo "Enable Fsync support"
          scripts/config --enable CONFIG_FUTEX
          scripts/config --enable CONFIG_FUTEX_PI
        fi

        if [ -n "$_futex2" ]; then
          echo "Enable Futex2 support"
          scripts/config --enable CONFIG_FUTEX2
        fi

        if [ -n "$_winesync" ]; then
          echo "Enable winesync support"
          scripts/config --module CONFIG_WINESYNC
        fi

    ### Set performance governor
        if [ -n "$_per_gov" ]; then
          echo "Setting performance governor..."
    		  scripts/config --disable CONFIG_CPU_FREQ_DEFAULT_GOV_SCHEDUTIL
    		  scripts/config --enable CONFIG_CPU_FREQ_DEFAULT_GOV_PERFORMANCE
    		  echo "Disabling uneeded governors..."
    		  scripts/config --enable CONFIG_CPU_FREQ_GOV_ONDEMAND
    		  scripts/config --disable CONFIG_CPU_FREQ_GOV_CONSERVATIVE
    		  scripts/config --disable CONFIG_CPU_FREQ_GOV_USERSPACE
    		  scripts/config --disable CONFIG_CPU_FREQ_GOV_SCHEDUTIL
        fi

    ### Disable Deadline I/O scheduler
    	   if [ -n "$_deadline_disable" ]; then
    		   echo "Disabling Deadline I/O scheduler..."
    		   scripts/config --disable CONFIG_MQ_IOSCHED_DEADLINE
    	   fi

    ### Disable Kyber I/O scheduler
    	   if [ -n "$_kyber_disable" ]; then
    		    echo "Disabling Kyber I/O scheduler..."
    		    scripts/config --disable CONFIG_MQ_IOSCHED_KYBER
    	    fi


      ### Enable protect file mappings under memory pressure
          if [ -n "$_mm_protect" ]; then
          	echo "Enabling protect file mappings under memory pressure..."
          	scripts/config --enable CONFIG_UNEVICTABLE_FILE
          	scripts/config --set-val CONFIG_UNEVICTABLE_FILE_KBYTES_LOW 262144
          	scripts/config --set-val CONFIG_UNEVICTABLE_FILE_KBYTES_MIN 131072
          fi

              ### Enable multigenerational LRU
          if [ -n "$_lru_enable" ]; then
          	echo "Enabling multigenerational LRU..."
          	scripts/config --enable CONFIG_HAVE_ARCH_PARENT_PMD_YOUNG
          	scripts/config --enable CONFIG_LRU_GEN
          	scripts/config --set-val CONFIG_NR_LRU_GENS 7
          	scripts/config --set-val CONFIG_TIERS_PER_GEN 4
          	scripts/config --enable CONFIG_LRU_GEN_ENABLED
          	scripts/config --disable CONFIG_LRU_GEN_STATS
          fi

    ### Enabling ZSTD COMPRESSION ##
          echo "Set module compression to ZSTD"
         scripts/config --enable CONFIG_MODULE_COMPRESS
          scripts/config --disable CONFIG_MODULE_COMPRESS_XZ
          scripts/config --enable CONFIG_MODULE_COMPRESS_ZSTD
    #      scripts/config --set-val CONFIG_MODULE_COMPRESS_ZSTD_LEVEL 19
    #      scripts/config --disable CONFIG_KERNEL_ZSTD_LEVEL_ULTRA

      ### Enabling Cacule-Config ##
          echo "Enable CacULE CPU scheduler..."
          scripts/config --enable CONFIG_CACULE_SCHED
          scripts/config --enable CONFIG_FAIR_GROUP_SCHED
          scripts/config --enable CONFIG_SCHED_AUTOGROUP
          scripts/config --disable CONFIG_EXPERT
          scripts/config --disable CONFIG_SCHED_DEBUG
          scripts/config --disable CONFIG_SCHED_INFO
          scripts/config --disable CONFIG_SCHEDSTATS
          scripts/config --disable CONFIG_DEBUG_KERNEL
          scripts/config --enable CONFIG_RT_GROUP_SCHED
          echo "Enabling Full Tickless"
          scripts/config --disable CONFIG_HZ_PERIODIC
          scripts/config --disable CONFIG_NO_HZ_IDLE
          scripts/config --enable CONFIG_NO_HZ_FULL
          scripts/config --enable CONFIG_NO_HZ
          scripts/config --enable CONFIG_NO_HZ_COMMON
          scripts/config --enable CONFIG_CONTEXT_TRACKING
          scripts/config --disable CONFIG_CONTEXT_TRACKING_FORCE
          echo "Enabling KBUILD_CFLAGS -O3..."
          scripts/config --disable CONFIG_CC_OPTIMIZE_FOR_PERFORMANCE
          scripts/config --enable CONFIG_CC_OPTIMIZE_FOR_PERFORMANCE_O3
          echo "Enable PREEMPT"
          scripts/config --disable CONFIG_PREEMPT_NONE
          scripts/config --disable CONFIG_PREEMPT_VOLUNTARY
          scripts/config --enable CONFIG_PREEMPT
          scripts/config --enable CONFIG_PREEMPT_COUNT
          scripts/config --enable CONFIG_PREEMPTION
          scripts/config --enable CONFIG_PREEMPT_DYNAMIC
          echo "Enable NTFS3"
          scripts/config --module CONFIG_NTFS_FS
          scripts/config --enable CONFIG_NTFS_RW
          scripts/config --enable CONFIG_NTFS_DEBUG
          scripts/config --module CONFIG_NTFS3_FS
          scripts/config --enable CONFIG_NTFS3_64BIT_CLUSTER
          scripts/config --enable CONFIG_NTFS3_LZX_XPRESS
          scripts/config --enable CONFIG_NTFS3_FS_POSIX_ACL
          echo "Enable Anbox"
          scripts/config --module  CONFIG_ASHMEM
          scripts/config --enable  CONFIG_ANDROID_BINDER_IPC_SELFTEST
          scripts/config --enable  CONFIG_ANDROID
          scripts/config --enable  CONFIG_ANDROID_BINDER_IPC
          scripts/config --enable  CONFIG_ANDROID_BINDERFS
          scripts/config --set-str CONFIG_ANDROID_BINDER_DEVICES binder,hwbinder,vndbinder
          echo "Disabling TCP_CONG_CUBIC..."
          scripts/config --module CONFIG_TCP_CONG_CUBIC
          scripts/config --disable CONFIG_DEFAULT_CUBIC
          echo "Enabling TCP_CONG_BBR2..."
          scripts/config --enable CONFIG_TCP_CONG_BBR2
          scripts/config --enable CONFIG_DEFAULT_BBR2
          scripts/config --set-str CONFIG_DEFAULT_TCP_CONG bbr2
          echo "Enable VHBA-Module"
          scripts/config --module CONFIG_VHBA
          scripts/config --disable CONFIG_GCC_PLUGINS
          # General Setup
          scripts/config --disable CONFIG_BSD_PROCESS_ACCT
          scripts/config --disable CONFIG_TASK_XACCT
          scripts/config --enable CONFIG_PSI
          scripts/config --disable CONFIG_MEMCG
          scripts/config --enable CONFIG_CGROUP_CPUACCT
          scripts/config --disable CONFIG_CGROUP_DEBUG
          scripts/config --disable CONFIG_CHECKPOINT_RESTORE
          scripts/config --disable CONFIG_SLAB_MERGE_DEFAULT
          scripts/config --disable CONFIG_SLAB_FREELIST_HARDENED
          scripts/config --disable CONFIG_SLUB_CPU_PARTIAL
          scripts/config --disable CONFIG_PROFILING

          # Processor type and features
          scripts/config --disable CONFIG_X86_5LEVEL
          scripts/config --disable CONFIG_KEXEC
          scripts/config --disable CONFIG_KEXEC_FILE
          scripts/config --disable CONFIG_CRASH_DUMPs
          scripts/config --disable CONFIG_KPROBES
          # Kernel hacking
          scripts/config --disable CONFIG_FTRACE
          scripts/config --disable CONFIG_DEBUG_KERNEL
          scripts/config --disable CONFIG_PAGE_EXTENSION
          scripts/config --set-val CONFIG_RCU_CPU_STALL_TIMEOUT 4
          scripts/config --disable CONFIG_PRINTK_TIME
          scripts/config --disable CONFIG_DEBUG_INFO
          scripts/config --disable CONFIG_ENABLE_MUST_CHECK
          scripts/config --disable CONFIG_STRIP_ASM_SYMS
          scripts/config --disable CONFIG_UNUSED_SYMBOLS
          scripts/config --disable CONFIG_DEBUG_FS
          scripts/config --disable CONFIG_OPTIMIZE_INLINING
          scripts/config --disable CONFIG_DEBUG_SECTION_MISMATCH
          scripts/config --disable CONFIG_SECTION_MISMATCH_WARN_ONLY
          scripts/config --disable CONFIG_STACK_VALIDATION
          scripts/config --disable CONFIG_DEBUG_FORCE_WEAK_PER_CPU
          scripts/config --disable CONFIG_MAGIC_SYSRQ
          scripts/config --disable CONFIG_MAGIC_SYSRQ_SERIAL
          scripts/config --disable CONFIG_PAGE_EXTENSION
          scripts/config --disable CONFIG_DEBUG_PAGEALLOC
          scripts/config --disable CONFIG_PAGE_OWNER
          scripts/config --disable CONFIG_DEBUG_MEMORY_INIT
          scripts/config --disable CONFIG_HARDLOCKUP_DETECTOR
          scripts/config --disable CONFIG_SOFTLOCKUP_DETECTOR
          scripts/config --disable CONFIG_DETECT_HUNG_TASK
          scripts/config --disable CONFIG_WQ_WATCHDOG
          scripts/config --set-val CONFIG_PANIC_TIMEOUT 10
          scripts/config --disable CONFIG_SCHEDSTATS
          scripts/config --disable CONFIG_SCHED_STACK_END_CHECK
          scripts/config --disable CONFIG_STACKTRACE
          scripts/config --disable CONFIG_DEBUG_BUGVERBOSE
          scripts/config --disable CONFIG_RCU_TRACE
          scripts/config --disable CONFIG_FAULT_INJECTION
          scripts/config --disable CONFIG_LATENCYTOP
          scripts/config --disable CONFIG_PROVIDE_OHCI1394_DMA_INIT
          scripts/config --disable RUNTIME_TESTING_MENU
          scripts/config --disable CONFIG_MEMTEST
          scripts/config --disable CONFIG_KGDB
          scripts/config --disable CONFIG_EARLY_PRINTK
          scripts/config --disable CONFIG_DOUBLEFAULT


    ### Optionally use running kernel's config
    # code originally by nous; http://aur.archlinux.org/packages.php?ID=40191
    if [ -n "$_use_current" ]; then
        if [[ -s /proc/config.gz ]]; then
            echo "Extracting config from /proc/config.gz..."
            # modprobe configs
            zcat /proc/config.gz > ./.config
        else
            warning "Your kernel was not compiled with IKCONFIG_PROC!"
            warning "You cannot read the current config!"
            warning "Aborting!"
            exit
        fi
    fi

    ### Optionally load needed modules for the make localmodconfig
    # See https://aur.archlinux.org/packages/modprobed-db
    if [ -n "$_localmodcfg" ]; then
        if [ -e $HOME/.config/modprobed.db ]; then
            echo "Running Steven Rostedt's make localmodconfig now"
            make LSMOD=$HOME/.config/modprobed.db localmodconfig
        else
            echo "No modprobed.db data found"
            exit
        fi
    fi

    make -s kernelrelease > version
    echo "Prepared $pkgbase version $(<version)"

    [[ -z "$_makenconfig" ]] || make nconfig

    ### Save configuration for later reuse
    cp -Tf ./.config "${startdir}/config-${pkgver}-${pkgrel}${pkgbase#linux}"
}

build() {
    cd $_srcname
    make all
}

_package() {
    pkgdesc="The $pkgdesc kernel and modules"
    depends=('coreutils' 'kmod' 'initramfs')
    optdepends=('crda: to set the correct wireless channels of your country'
                'linux-firmware: firmware images needed for some devices'
                'modprobed-db: Keeps track of EVERY kernel module that has ever been probed - useful for those of us who make localmodconfig')
    provides=(VIRTUALBOX-GUEST-MODULES WIREGUARD-MODULE)

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
    make INSTALL_MOD_PATH="$pkgdir/usr" modules_install

    # remove build and source links
    rm "$modulesdir"/{source,build}
}

_package-headers() {
    pkgdesc="Headers and scripts for building modules for the $pkgdesc kernel"

    cd ${_srcname}
    local builddir="$pkgdir/usr/lib/modules/$(<version)/build"

    echo "Installing build files..."
    install -Dt "$builddir" -m644 .config Makefile Module.symvers System.map \
        localversion.* version vmlinux
    install -Dt "$builddir/kernel" -m644 kernel/Makefile
    install -Dt "$builddir/arch/x86" -m644 arch/x86/Makefile
    cp -t "$builddir" -a scripts

    # add objtool for external module building and enabled VALIDATION_STACK option
    install -Dt "$builddir/tools/objtool" tools/objtool/objtool

    # add xfs and shmem for aufs building
    mkdir -p "$builddir"/{fs/xfs,mm}

    echo "Installing headers..."
    cp -t "$builddir" -a include
    cp -t "$builddir/arch/x86" -a arch/x86/include
    install -Dt "$builddir/arch/x86/kernel" -m644 arch/x86/kernel/asm-offsets.s

    install -Dt "$builddir/drivers/md" -m644 drivers/md/*.h
    install -Dt "$builddir/net/mac80211" -m644 net/mac80211/*.h

    # http://bugs.archlinux.org/task/13146
    install -Dt "$builddir/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

    # http://bugs.archlinux.org/task/20402
    install -Dt "$builddir/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
    install -Dt "$builddir/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
    install -Dt "$builddir/drivers/media/tuners" -m644 drivers/media/tuners/*.h

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

md5sums=('76c60fb304510a7bbd9c838790bc5fe4'
         '2ec080047599485c19b50300c9d07aa8'
         '9bb46b8ce45259c238c5233b8394d70b'
         '8fab6f0acf86d138a283c4dd044198ed'
         '7640a753a7803248543675a6edc75e08'
         '85f4be6562ee033b83814353a12b61bd'
         '2c0375b3cc9690a0f0f3d3e49df54d10'
         '9573b92353399343db8a691c9b208300'
         '1217799f33d6ba822152a0e2fb6f2e34'
         '09a9e83b7b828fae46fd1a4f4cc23c28'
         'e16eb528e701193bc8cb1facc6b27231'
         '396c84c4a6557db27f9c3bbfa656ac3e'
         '9bbbd88f0303ccd59064648eaaf80edd'
         '12cdc30bc3e2a17825b23b63bd6a5e7a'
         '63078800040b2a9a9f19c59c4ebf5b23'
         '81f27f12e20971c7d7fc3a53ffb6842c'
         'ed46a39e062f07693f52981fbd7350b7'
         '3f302dbaceea020abd40f6e9f23b75df'
         '86825a0c5716a1d9c6a39f9d3886b1bf'
         '8b756f75c5600b1ea7df5e11ac738df5'
         '20e92e9d958ca8fec6ff441bb97aa9f1'
         '9977ba0e159416108217a45438ebebb4'
         'c68e4fd9b4a55ee730a34bb39ae325ad'
         '0632f49f076c90b6d6098cad7b5a88ac'
         'eff4bb43a8defb7ed08b9c1e403291ea'
         '9e5114dba6da65e8d444aa225b109a21'
         'c360b8c17d778f98a54fa7cddf348566'
         'ce9beff503ee9e6ce6fd983c1bbbdd9e'
         'ef7748efcae55f7db8961227cbae3677')
