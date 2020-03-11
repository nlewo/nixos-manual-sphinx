

:command:`nixos-enter`
8
NixOS

.. COMMENT: <refmiscinfo class="version"><xi:include href="version.txt" parse="text"/></refmiscinfo>

:command:`nixos-enter`
run a command in a NixOS chroot environment

CMDSYN:  nixos-enter --root root --system system -c shell-command --silent --help -- arguments

Description
^^^^^^^^^^^

This command runs a command in a NixOS chroot environment, that is, in a
filesystem hierarchy previously prepared using
:command:`nixos-install`.

Options
^^^^^^^

This command accepts the following options:

.. _None:

--root
______

    The path to the NixOS system you want to enter. It defaults to
    :file:`/mnt`.

.. _None:

--system
________

    The NixOS system configuration to use. It defaults to
    :file:`/nix/var/nix/profiles/system`. You can enter a
    previous NixOS configuration by specifying a path such as
    :file:`/nix/var/nix/profiles/system-106-link`.

.. _None:

--command
_________, .. _None:

-c
__

    The bash command to execute.

.. _None:

--silent
________

    Suppresses all output from the activation script of the target system.

.. _None:

--
__

    Interpret the remaining arguments as the program name and arguments to be
    invoked. The program is not executed in a shell.

Examples
^^^^^^^^

Start an interactive shell in the NixOS installation in
:file:`/mnt`:

::

    # nixos-enter /mnt

Run a shell command:

::

    # nixos-enter -c 'ls -l /; cat /proc/mounts'

Run a non-shell command:

::

    # nixos-enter -- cat /proc/mounts


