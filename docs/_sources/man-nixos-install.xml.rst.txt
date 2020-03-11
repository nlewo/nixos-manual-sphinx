

:command:`nixos-install`
8
NixOS

.. COMMENT: <refmiscinfo class="version"><xi:include href="version.txt" parse="text"/></refmiscinfo>

:command:`nixos-install`
install bootloader and NixOS

CMDSYN:  nixos-install --verbose -v --print-build-logs -L -I path --root root --system path --channel channel --no-channel-copy --no-root-passwd --no-bootloader --max-jobs -j number --cores number --option name value --show-trace --help

Description
^^^^^^^^^^^

This command installs NixOS in the file system mounted on
:file:`/mnt`, based on the NixOS configuration specified in
:file:`/mnt/etc/nixos/configuration.nix`. It performs the
following steps:

-

  It copies Nix and its dependencies to
  :file:`/mnt/nix/store`.

-

  It runs Nix in :file:`/mnt` to build the NixOS configuration
  specified in :file:`/mnt/etc/nixos/configuration.nix`.

-

  It installs the current channel nixos in the target channel
  profile (unless is specified).

-

  It installs the GRUB boot loader on the device specified in the option
  (unless
  is specified), and generates a GRUB
  configuration file that boots into the NixOS configuration just
  installed.

-

  It prompts you for a password for the root account (unless
  is specified).

This command is idempotent: if it is interrupted or fails due to a temporary
problem (e.g. a network issue), you can safely re-run it.

Options
^^^^^^^

This command accepts the following options:

.. _None:

--verbose
_________
/ .. _None:

-v
__

    Increases the level of verbosity of diagnostic messages
    printed on standard error.  For each Nix operation, the information
    printed on standard output is well-defined; any diagnostic
    information is printed on standard error, never on standard
    output.

    Please note that this option may be specified repeatedly.

.. _None:

--print-build-logs
__________________
/ .. _None:

-L
__

    Print the full build logs of :command:`nix build` to stderr.

.. _None:

--root
______

    Defaults to :file:`/mnt`. If this option is given, treat the
    directory *root* as the root of the NixOS
    installation.

.. _None:

--system
________

    If this option is provided, :command:`nixos-install` will install
    the specified closure rather than attempt to build one from
    :file:`/mnt/etc/nixos/configuration.nix`.

    The closure must be an appropriately configured NixOS system, with boot
    loader and partition configuration that fits the target host. Such a
    closure is typically obtained with a command such as :command:`nix-build
    -I nixos-config=./configuration.nix '<nixpkgs/nixos>' -A system
    --no-out-link`

.. _None:

--channel
_________

    If this option is provided, do not copy the current
    nixos channel to the target host. Instead, use the
    specified derivation.

.. _None:

-I
__

    Add a path to the Nix expression search path. This option may be given
    multiple times. See the NIX_PATH environment variable for information on
    the semantics of the Nix search path. Paths added through
    *-I* take precedence over NIX_PATH.

.. _None:

--max-jobs
__________, .. _None:

-j
__

    Sets the maximum number of build jobs that Nix will perform in parallel
    to the specified number. The default is ``1``. A higher
    value is useful on SMP systems or to exploit I/O latency.

.. _None:

--cores
_______

    Sets the value of the NIX_BUILD_CORES environment variable
    in the invocation of builders. Builders can use this variable at their
    discretion to control the maximum amount of parallelism. For instance, in
    Nixpkgs, if the derivation attribute
    ``enableParallelBuilding`` is set to
    ``true``, the builder passes the
    flag to GNU Make. The
    value ``0`` means that the builder should use all
    available CPU cores in the system.

.. _None:

--option
________
*name* *value*

    Set the Nix configuration option *name* to
    *value*.

.. _None:

--show-trace
____________

    Causes Nix to print out a stack trace in case of Nix expression
    evaluation errors.

.. _None:

--help
______

    Synonym for :command:`man nixos-install`.

Examples
^^^^^^^^

A typical NixOS installation is done by creating and mounting a file system
on :file:`/mnt`, generating a NixOS configuration in
:file:`/mnt/etc/nixos/configuration.nix`, and running
:command:`nixos-install`. For instance, if we want to install NixOS
on an ``ext4`` file system created in
:file:`/dev/sda1`:
::

    $ mkfs.ext4 /dev/sda1$ mount /dev/sda1 /mnt$ nixos-generate-config --root /mnt$ # edit /mnt/etc/nixos/configuration.nix$ nixos-install$ reboot


