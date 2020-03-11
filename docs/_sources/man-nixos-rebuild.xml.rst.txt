

:command:`nixos-rebuild`
8
NixOS

.. COMMENT: <refmiscinfo class="version"><xi:include href="version.txt" parse="text"/></refmiscinfo>

:command:`nixos-rebuild`
reconfigure a NixOS machine

CMDSYN:  nixos-rebuild switch boot test build dry-build dry-activate edit build-vm build-vm-with-bootloader --upgrade --install-bootloader --no-build-nix --fast --rollback --builders builder-spec --flake flake-uri --profile-name -p name --show-trace -I path --verbose -v --max-jobs -j number --keep-failed -K --keep-going -k

Description
^^^^^^^^^^^

This command updates the system so that it corresponds to the
configuration specified in
:file:`/etc/nixos/configuration.nix` or
:file:`/etc/nixos/flake.nix`. Thus, every time you
modify the configuration or any other NixOS module, you must run
:command:`nixos-rebuild` to make the changes take
effect. It builds the new system in
:file:`/nix/store`, runs its activation script, and
stop and (re)starts any system services if needed. Please note that
user services need to be started manually as they aren't detected
by the activation script at the moment.

This command has one required argument, which specifies the desired
operation. It must be one of the following:

.. _None:

switch
______

    Build and activate the new configuration, and make it the boot default.
    That is, the configuration is added to the GRUB boot menu as the default
    menu entry, so that subsequent reboots will boot the system into the new
    configuration. Previous configurations activated with
    :command:`nixos-rebuild switch` or :command:`nixos-rebuild
    boot` remain available in the GRUB menu.

.. _None:

boot
____

    Build the new configuration and make it the boot default (as with
    :command:`nixos-rebuild switch`), but do not activate it. That
    is, the system continues to run the previous configuration until the
    next reboot.

.. _None:

test
____

    Build and activate the new configuration, but do not add it to the GRUB
    boot menu. Thus, if you reboot the system (or if it crashes), you will
    automatically revert to the default configuration (i.e. the
    configuration resulting from the last call to :command:`nixos-rebuild
    switch` or :command:`nixos-rebuild boot`).

.. _None:

build
_____

    Build the new configuration, but neither activate it nor add it to the
    GRUB boot menu. It leaves a symlink named :file:`result` in
    the current directory, which points to the output of the top-level
    “system” derivation. This is essentially the same as doing
    ::

        $ nix-build /path/to/nixpkgs/nixos -A system

    Note that you do not need to be ``root`` to run
    :command:`nixos-rebuild build`.

.. _None:

dry-build
_________

    Show what store paths would be built or downloaded by any of the
    operations above, but otherwise do nothing.

.. _None:

dry-activate
____________

    Build the new configuration, but instead of activating it, show what
    changes would be performed by the activation (i.e. by
    :command:`nixos-rebuild test`). For instance, this command will
    print which systemd units would be restarted. The list of changes is not
    guaranteed to be complete.

.. _None:

edit
____

    Opens :file:`configuration.nix` in the default editor.

.. _None:

build-vm
________

    Build a script that starts a NixOS virtual machine with the desired
    configuration. It leaves a symlink :file:`result` in the
    current directory that points (under
    :file:`result/bin/run-`)
    at the script that starts the VM. Thus, to test a NixOS configuration in
    a virtual machine, you should do the following:
    ::

        $ nixos-rebuild build-vm$ ./result/bin/run-\*-vm

    The VM is implemented using the ``qemu`` package. For
    best performance, you should load the ``kvm-intel`` or
    ``kvm-amd`` kernel modules to get hardware
    virtualisation.

    The VM mounts the Nix store of the host through the 9P file system. The
    host Nix store is read-only, so Nix commands that modify the Nix store
    will not work in the VM. This includes commands such as
    :command:`nixos-rebuild`; to change the VM’s configuration,
    you must halt the VM and re-run the commands above.

    The VM has its own ``ext3`` root file system, which is
    automatically created when the VM is first started, and is persistent
    across reboots of the VM. It is stored in
    ``./*hostname*.qcow2``.

    .. COMMENT: The entire file system hierarchy of the host is available in
                the VM under <filename>/hostfs</filename>.

.. _None:

build-vm-with-bootloader
________________________

    Like , but boots using the regular boot loader
    of your configuration (e.g., GRUB 1 or 2), rather than booting directly
    into the kernel and initial ramdisk of the system. This allows you to
    test whether the boot loader works correctly. However, it does not
    guarantee that your NixOS configuration will boot successfully on the
    host hardware (i.e., after running :command:`nixos-rebuild
    switch`), because the hardware and boot loader configuration in
    the VM are different. The boot loader is installed on an automatically
    generated virtual disk containing a :file:`/boot`
    partition, which is mounted read-only in the VM.

Options
^^^^^^^

This command accepts the following options:

.. _None:

--upgrade
_________

    Fetch the latest version of NixOS from the NixOS channel.

.. _None:

--install-bootloader
____________________

    Causes the boot loader to be (re)installed on the device specified by the
    relevant configuration options.

.. _None:

--no-build-nix
______________

    Normally, :command:`nixos-rebuild` first builds the
    ``nixUnstable`` attribute in Nixpkgs, and uses the
    resulting instance of the Nix package manager to build the new system
    configuration. This is necessary if the NixOS modules use features not
    provided by the currently installed version of Nix. This option disables
    building a new Nix.

.. _None:

--fast
______

    Equivalent to
    . This option is useful if you call
    :command:`nixos-rebuild` frequently (e.g. if you’re hacking on
    a NixOS module).

.. _None:

--rollback
__________

    Instead of building a new configuration as specified by
    :file:`/etc/nixos/configuration.nix`, roll back to the
    previous configuration. (The previous configuration is defined as the one
    before the “current” generation of the Nix profile
    :file:`/nix/var/nix/profiles/system`.)

.. _None:

--builders
__________
*builder-spec*

    Allow ad-hoc remote builders for building the new system. This requires
    the user executing :command:`nixos-rebuild` (usually root) to be
    configured as a trusted user in the Nix daemon. This can be achieved by
    using the ``nix.trustedUsers`` NixOS option. Examples
    values for that option are described in the ``Remote builds
    chapter`` in the Nix manual, (i.e. :command:`--builders
    "ssh://bigbrother x86_64-linux"`). By specifying an empty string
    existing builders specified in :file:`/etc/nix/machines` can
    be ignored: :command:`--builders ""` for example when they are
    not reachable due to network connectivity.

.. _None:

--profile-name
______________, .. _None:

-p
__

    Instead of using the Nix profile
    :file:`/nix/var/nix/profiles/system` to keep track of the
    current and previous system configurations, use
    :file:`/nix/var/nix/profiles/system-profiles/`.
    When you use GRUB 2, for every system profile created with this flag,
    NixOS will create a submenu named “NixOS - Profile
    '*name*'” in GRUB’s boot menu, containing
    the current and previous configurations of this profile.

    For instance, if you want to test a configuration file named
    :file:`test.nix` without affecting the default system
    profile, you would do:
    ::

        $ nixos-rebuild switch -p test -I nixos-config=./test.nix

    The new configuration will appear in the GRUB 2 submenu “NixOS -
    Profile 'test'”.

.. _None:

--build-host
____________

    Instead of building the new configuration locally, use the specified host
    to perform the build. The host needs to be accessible with ssh, and must
    be able to perform Nix builds. If the option
    is not set, the build will be copied back
    to the local machine when done.

    Note that, if is not specified, Nix will
    be built both locally and remotely. This is because the configuration
    will always be evaluated locally even though the building might be
    performed remotely.

    You can include a remote user name in the host name
    (*user@host*). You can also set ssh options by
    defining the NIX_SSHOPTS environment variable.

.. _None:

--target-host
_____________

    Specifies the NixOS target host. By setting this to something other than
    *localhost*, the system activation will happen
    on the remote host instead of the local machine. The remote host needs to
    be accessible over ssh, and for the commands ,
    and you need root access.

    If is not explicitly specified,
    will implicitly be set to the same value as
    . So, if you only specify
    both building and activation will take
    place remotely (and no build artifacts will be copied to the local
    machine).

    You can include a remote user name in the host name
    (*user@host*). You can also set ssh options by
    defining the NIX_SSHOPTS environment variable.

.. _None:

--use-remote-sudo
_________________

    When set, nixos-rebuild prefixes remote commands that run on
    the and
    systems with :command:`sudo`. Setting this option allows
    deploying as a non-root user.

.. _None:

--flake
_______
*flake-uri*[*name*]

    Build the NixOS system from the specified flake. It defaults to
    the directory containing the target of the symlink
    :file:`/etc/nixos/flake.nix`, if it exists. The
    flake must contain an output named
    ``nixosConfigurations.*name*``. If
    *name* is omitted, it default to the
    current host name.

In addition, :command:`nixos-rebuild` accepts various Nix-related
flags, including / ,
, ,
and /
. See the Nix manual for details.

Environment
^^^^^^^^^^^

NIXOS_CONFIG

    Path to the main NixOS configuration module. Defaults to
    :file:`/etc/nixos/configuration.nix`.

NIX_SSHOPTS

    Additional options to be passed to :command:`ssh` on the command
    line.

Files
^^^^^

:file:`/etc/nixos/flake.nix`

    If this file exists, then :command:`nixos-rebuild` will
    use it as if the option was given. This
    file may be a symlink to a :file:`flake.nix` in an
    actual flake; thus :file:`/etc/nixos` need not be a
    flake.

:file:`/run/current-system`

    A symlink to the currently active system configuration in the Nix store.

:file:`/nix/var/nix/profiles/system`

    The Nix profile that contains the current and previous system
    configurations. Used to generate the GRUB boot menu.

Bugs
^^^^

This command should be renamed to something more descriptive.


