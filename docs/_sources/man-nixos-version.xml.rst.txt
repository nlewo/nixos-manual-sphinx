

:command:`nixos-version`
8
NixOS

:command:`nixos-version`
show the NixOS version

CMDSYN:  nixos-version --hash --revision --json

Description
^^^^^^^^^^^

This command shows the version of the currently active NixOS configuration.
For example:
::

    $ nixos-version
    16.03.1011.6317da4 (Emu)

The version consists of the following elements:

``16.03``

    The NixOS release, indicating the year and month in which it was
    released (e.g. March 2016).

``1011``

    The number of commits in the Nixpkgs Git repository between the start of
    the release branch and the commit from which this version was built.
    This ensures that NixOS versions are monotonically increasing. It is
    ``git`` when the current NixOS configuration was built
    from a checkout of the Nixpkgs Git repository rather than from a NixOS
    channel.

``6317da4``

    The first 7 characters of the commit in the Nixpkgs Git repository from
    which this version was built.

``Emu``

    The code name of the NixOS release. The first letter of the code name
    indicates that this is the N'th stable NixOS release; for example, Emu
    is the fifth release.

Options
^^^^^^^

This command accepts the following options:

.. _None:

--hash
______, .. _None:

--revision
__________

    Show the full SHA1 hash of the Git commit from which this configuration
    was built, e.g.
    ::

        $ nixos-version --hash
        6317da40006f6bc2480c6781999c52d88dde2acf

.. _None:

--json
______

    Print a JSON representation of the versions of NixOS and the
    top-level configuration flake.


