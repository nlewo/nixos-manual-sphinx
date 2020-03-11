

.. _sec-profile-graphical:

Graphical
~~~~~~~~~

Defines a NixOS configuration with the Plasma 5 desktop. It's used by the
graphical installation CD.

It sets :ref:`opt-services.xserver.enable`,
:ref:`opt-services.xserver.displayManager.sddm.enable`,
:ref:`opt-services.xserver.desktopManager.plasma5.enable`, and
:ref:`opt-services.xserver.libinput.enable` to true. It also
includes glxinfo and firefox in the system packages list.

