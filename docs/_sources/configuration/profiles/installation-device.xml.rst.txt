

.. _sec-profile-installation-device:

Installation Device
~~~~~~~~~~~~~~~~~~~

Provides a basic configuration for installation devices like CDs.
This enables redistributable firmware, includes the
`Clone Config profile <None>`_
and a copy of the Nixpkgs channel, so :command:`nixos-install`
works out of the box.

Documentation for `Nixpkgs <None>`_
and `NixOS <None>`_ are
forcefully enabled (to override the
`Minimal profile <None>`_ preference); the
NixOS manual is shown automatically on TTY 8, udisks is disabled.
Autologin is enabled as ``nixos`` user, while passwordless
login as both ``root`` and ``nixos`` is possible.
Passwordless :command:`sudo` is enabled too.
`wpa_supplicant <None>`_ is
enabled, but configured to not autostart.

It is explained how to login, start the ssh server, and if available,
how to start the display manager.

Several settings are tweaked so that the installer has a better chance of
succeeding under low-memory environments.

