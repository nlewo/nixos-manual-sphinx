

.. _sec-kernel-config:

Linux Kernel
------------

You can override the Linux kernel and associated packages using the option
. For instance, this selects the Linux
3.10 kernel:

.. parsed-literal::

    :ref:`opt-boot.kernelPackages`  = pkgs.linuxPackages_3_10;

Note that this not only replaces the kernel, but also packages that are
specific to the kernel version, such as the NVIDIA video drivers. This
ensures that driver packages are consistent with the kernel.

The default Linux kernel configuration should be fine for most users. You can
see the configuration of your current kernel with the following command:

::

    zcat /proc/config.gz

If you want to change the kernel configuration, you can use the
feature (see
:ref:`sec-customising-packages`). For instance, to enable support
for the kernel debugger KGDB:

::

    nixpkgs.config.packageOverrides = pkgs:
      { linux_3_4 = pkgs.linux_3_4.override {
          extraConfig =
            ''
              KGDB y
            '';
        };
      };

``extraConfig`` takes a list of Linux kernel configuration
options, one per line. The name of the option should not include the prefix
``CONFIG_``. The option value is typically
``y``, ``n`` or ``m`` (to build
something as a kernel module).

Kernel modules for hardware devices are generally loaded automatically by
:command:`udev`. You can force a module to be loaded via
:ref:`opt-boot.kernelModules`, e.g.

.. parsed-literal::

    :ref:`opt-boot.kernelModules`  = [ "fuse" "kvm-intel" "coretemp" ];

If the module is required early during the boot (e.g. to mount the root file
system), you can use :ref:`opt-boot.initrd.kernelModules`:

.. parsed-literal::

    :ref:`opt-boot.initrd.kernelModules`  = [ "cifs" ];

This causes the specified modules and their dependencies to be added to the
initial ramdisk.

Kernel runtime parameters can be set through
:ref:`opt-boot.kernel.sysctl`, e.g.

.. parsed-literal::

    :ref:`opt-boot.kernel.sysctl`."net.ipv4.tcp_keepalive_time" = 120;

sets the kernel’s TCP keepalive time to 120 seconds. To see the available
parameters, run :command:`sysctl -a`.

.. _sec-linux-config-customizing:

Customize your kernel
~~~~~~~~~~~~~~~~~~~~~

The first step before compiling the kernel is to generate an appropriate
``.config`` configuration. Either you pass your own config
via the ``configfile`` setting of
``linuxManualConfig``:
::

    custom-kernel = super.linuxManualConfig {
    inherit (super) stdenv hostPlatform;
    inherit (linux_4_9) src;
    version = "${linux_4_9.version}-custom";

    configfile = /home/me/my_kernel_config;
    allowImportFromDerivation = true;
    };

You can edit the config with this snippet (by default :command:`make
menuconfig` won't work out of the box on nixos):
::

    nix-shell -E 'with import <nixpkgs> {}; kernelToOverride.overrideAttrs (o: {nativeBuildInputs=o.nativeBuildInputs ++ [ pkgconfig ncurses ];})'

or you can let nixpkgs generate the configuration. Nixpkgs generates it via
answering the interactive kernel utility :command:`make config`. The
answers depend on parameters passed to
:file:`pkgs/os-specific/linux/kernel/generic.nix` (which you
can influence by overriding ``extraConfig, autoModules,
modDirVersion, preferBuiltin, extraConfig``).
::

    mptcp93.override ({
    name="mptcp-local";

    ignoreConfigErrors = true;
    autoModules = false;
    kernelPreferBuiltin = true;

    enableParallelBuilding = true;

    extraConfig = ''
    DEBUG_KERNEL y
    FRAME_POINTER y
    KGDB y
    KGDB_SERIAL_CONSOLE y
    DEBUG_INFO y
    '';
    });

.. _sec-linux-config-developing-modules:

Developing kernel modules
~~~~~~~~~~~~~~~~~~~~~~~~~

When developing kernel modules it's often convenient to run edit-compile-run
loop as quickly as possible. See below snippet as an example of developing
``mellanox`` drivers.

::

    $ nix-build '<nixpkgs>' -A linuxPackages.kernel.dev
    $ nix-shell '<nixpkgs>' -A linuxPackages.kernel
    $ unpackPhase
    $ cd linux-*
    $ make -C $dev/lib/modules/\*/build M=$(pwd)/drivers/net/ethernet/mellanox modules
    # insmod ./drivers/net/ethernet/mellanox/mlx5/core/mlx5_core.ko


