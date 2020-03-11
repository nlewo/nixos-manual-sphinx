

:command:`nixos-option`
8
NixOS

.. COMMENT: <refmiscinfo class="version"><xi:include href="version.txt" parse="text"/></refmiscinfo>

:command:`nixos-option`
inspect a NixOS configuration

CMDSYN:  nixos-option -r --recursive -I path option.name

Description
^^^^^^^^^^^

This command evaluates the configuration specified in
:file:`/etc/nixos/configuration.nix` and returns the properties
of the option name given as argument.

When the option name is not an option, the command prints the list of
attributes contained in the attribute set.

Options
^^^^^^^

This command accepts the following options:

.. _None:

-r
__, .. _None:

--recursive
___________

    Print all the values at or below the specified path recursively.

.. _None:

-I
__
*path*

    This option is passed to the underlying
    :command:`nix-instantiate` invocation.

Environment
^^^^^^^^^^^

NIXOS_CONFIG

    Path to the main NixOS configuration module. Defaults to
    :file:`/etc/nixos/configuration.nix`.

Examples
^^^^^^^^

Investigate option values:
::

    $ nixos-option boot.loader
    This attribute set contains:
    generationsDir
    grub
    initScript
    $ nixos-option boot.loader.grub.enable
    Value:
    true

    Default:
    true

    Description:
    Whether to enable the GNU GRUB boot loader.

    Declared by:
    "/nix/var/nix/profiles/per-user/root/channels/nixos/nixpkgs/nixos/modules/system/boot/loader/grub/grub.nix"

    Defined by:
    "/nix/var/nix/profiles/per-user/root/channels/nixos/nixpkgs/nixos/modules/system/boot/loader/grub/grub.nix"

Bugs
^^^^

The author listed in the following section is wrong. If there is any other
bug, please report to Nicolas Pierron.

See also
^^^^^^^^

configuration.nix
5


