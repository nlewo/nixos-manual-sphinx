

.. _sec-x11:

X Window System
---------------

The X Window System (X11) provides the basis of NixOS’ graphical user
interface. It can be enabled as follows:

.. parsed-literal::

    :ref:`opt-services.xserver.enable`  = true;

The X server will automatically detect and use the appropriate video driver
from a set of X.org drivers (such as ``vesa`` and
``intel``). You can also specify a driver manually, e.g.

.. parsed-literal::

    :ref:`opt-services.xserver.videoDrivers`  = [ "r128" ];

to enable X.org’s ``xf86-video-r128`` driver.

You also need to enable at least one desktop or window manager. Otherwise,
you can only log into a plain undecorated :command:`xterm` window.
Thus you should pick one or more of the following lines:

.. parsed-literal::

    :ref:`opt-services.xserver.desktopManager.plasma5.enable`  = true;
    :ref:`opt-services.xserver.desktopManager.xfce.enable`  = true;
    :ref:`opt-services.xserver.desktopManager.gnome3.enable`  = true;
    :ref:`opt-services.xserver.desktopManager.mate.enable`  = true;
    :ref:`opt-services.xserver.windowManager.xmonad.enable`  = true;
    :ref:`opt-services.xserver.windowManager.twm.enable`  = true;
    :ref:`opt-services.xserver.windowManager.icewm.enable`  = true;
    :ref:`opt-services.xserver.windowManager.i3.enable`  = true;

NixOS’s default *display manager* (the program that
provides a graphical login prompt and manages the X server) is LightDM. You
can select an alternative one by picking one of the following lines:

.. parsed-literal::

    :ref:`opt-services.xserver.displayManager.sddm.enable`  = true;
    :ref:`opt-services.xserver.displayManager.gdm.enable`  = true;

You can set the keyboard layout (and optionally the layout variant):

.. parsed-literal::

    :ref:`opt-services.xserver.layout`  = "de";
    :ref:`opt-services.xserver.xkbVariant`  = "neo";

The X server is started automatically at boot time. If you don’t want this
to happen, you can set:

.. parsed-literal::

    :ref:`opt-services.xserver.autorun`  = false;

The X server can then be started manually:
::

    # systemctl start display-manager.service

On 64-bit systems, if you want OpenGL for 32-bit programs such as in Wine,
you should also set the following:

.. parsed-literal::

    :ref:`opt-hardware.opengl.driSupport32Bit`  = true;

Auto-login
~~~~~~~~~~

The x11 login screen can be skipped entirely, automatically logging you into
your window manager and desktop environment when you boot your computer.

This is especially helpful if you have disk encryption enabled. Since you
already have to provide a password to decrypt your disk, entering a second
password to login can be redundant.

To enable auto-login, you need to define your default window manager and
desktop environment. If you wanted no desktop environment and i3 as your your
window manager, you'd define:

.. parsed-literal::

    :ref:`opt-services.xserver.displayManager.defaultSession`  = "none+i3";

Every display manager in NixOS supports auto-login, here is an example
using lightdm for a user ``alice``:

.. parsed-literal::

    :ref:`opt-services.xserver.displayManager.lightdm.enable`  = true;
    :ref:`opt-services.xserver.displayManager.lightdm.autoLogin.enable`  = true;
    :ref:`opt-services.xserver.displayManager.lightdm.autoLogin.user`  = "alice";

The options are named identically for all other display managers.

Proprietary NVIDIA drivers
~~~~~~~~~~~~~~~~~~~~~~~~~~

NVIDIA provides a proprietary driver for its graphics cards that has better
3D performance than the X.org drivers. It is not enabled by default because
it’s not free software. You can enable it as follows:

.. parsed-literal::

    :ref:`opt-services.xserver.videoDrivers`  = [ "nvidia" ];

Or if you have an older card, you may have to use one of the legacy drivers:

.. parsed-literal::

    :ref:`opt-services.xserver.videoDrivers`  = [ "nvidiaLegacy390" ];
    :ref:`opt-services.xserver.videoDrivers`  = [ "nvidiaLegacy340" ];
    :ref:`opt-services.xserver.videoDrivers`  = [ "nvidiaLegacy304" ];
    :ref:`opt-services.xserver.videoDrivers`  = [ "nvidiaLegacy173" ];

You may need to reboot after enabling this driver to prevent a clash with
other kernel modules.

Proprietary AMD drivers
~~~~~~~~~~~~~~~~~~~~~~~

AMD provides a proprietary driver for its graphics cards that has better 3D
performance than the X.org drivers. It is not enabled by default because
it’s not free software. You can enable it as follows:

.. parsed-literal::

    :ref:`opt-services.xserver.videoDrivers`  = [ "ati_unfree" ];

You will need to reboot after enabling this driver to prevent a clash with
other kernel modules.

.. note::

   For recent AMD GPUs you most likely want to keep either the defaults
   or ``"amdgpu"`` (both free).

Touchpads
~~~~~~~~~

Support for Synaptics touchpads (found in many laptops such as the Dell
Latitude series) can be enabled as follows:

.. parsed-literal::

    :ref:`opt-services.xserver.libinput.enable`  = true;

The driver has many options (see :ref:`ch-options`). For
instance, the following disables tap-to-click behavior:

.. parsed-literal::

    :ref:`opt-services.xserver.libinput.tapping`  = false;

Note: the use of ``services.xserver.synaptics`` is deprecated
since NixOS 17.09.

GTK/Qt themes
~~~~~~~~~~~~~

GTK themes can be installed either to user profile or system-wide (via
``environment.systemPackages``). To make Qt 5 applications
look similar to GTK2 ones, you can install ``qt5.qtbase.gtk``
package into your system environment. It should work for all Qt 5 library
versions.

Custom XKB layouts
~~~~~~~~~~~~~~~~~~

It is possible to install custom
`XKB <https://en.wikipedia.org/wiki/X_keyboard_extension>`_
keyboard layouts using the option
.
As a first example, we are going to create a layout based on the basic US
layout, with an additional layer to type some greek symbols by pressing the
right-alt key.

To do this we are going to create a ``us-greek`` file
with a ``xkb_symbols`` section.

::

    xkb_symbols "us-greek"
    {
      include "us(basic)"            // includes the base US keys
      include "level3(ralt_switch)"  // configures right alt as a third level switch

      key <LatA> { [ a, A, Greek_alpha ] };
      key <LatB> { [ b, B, Greek_beta  ] };
      key <LatG> { [ g, G, Greek_gamma ] };
      key <LatD> { [ d, D, Greek_delta ] };
      key <LatZ> { [ z, Z, Greek_zeta  ] };
    };

To install the layout, the filepath, a description and the list of
languages must be given:

.. parsed-literal::

    :ref:`opt-services.xserver.extraLayouts`.us-greek = {
      description = "US layout with alt-gr greek";
      languages   = [ "eng" ];
      symbolsFile = /path/to/us-greek;
    }

.. note::

   The name should match the one given to the
   ``xkb_symbols`` block.

The layout should now be installed and ready to use: try it by
running ``setxkbmap us-greek`` and type
``<alt>+a``. To change the default the usual

option can still be used.

A layout can have several other components besides
``xkb_symbols``, for example we will define new
keycodes for some multimedia key and bind these to some symbol.

Use the *xev* utility from
``pkgs.xorg.xev`` to find the codes of the keys of
interest, then create a ``media-key`` file to hold
the keycodes definitions

::

    xkb_keycodes "media"
    {
     <volUp>   = 123;
     <volDown> = 456;
    }

Now use the newly define keycodes in ``media-sym``:

::

    xkb_symbols "media"
    {
     key.type = "ONE_LEVEL";
     key <volUp>   { [ XF86AudioLowerVolume ] };
     key <volDown> { [ XF86AudioRaiseVolume ] };
    }

As before, to install the layout do

.. parsed-literal::

    :ref:`opt-services.xserver.extraLayouts`.media = {
      description  = "Multimedia keys remapping";
      languages    = [ "eng" ];
      symbolsFile  = /path/to/media-key;
      keycodesFile = /path/to/media-sym;
    };

.. note::

   The function ``pkgs.writeText <filename> <content>`` can be useful if you prefer to keep the layout definitions
   inside the NixOS configuration.

Unfortunately, the Xorg server does not (currently) support setting a
keymap directly but relies instead on XKB rules to select the matching
components (keycodes, types, ...) of a layout. This means that components
other than symbols won't be loaded by default. As a workaround, you
can set the keymap using ``setxkbmap`` at the start of the
session with:

.. parsed-literal::

    :ref:`opt-services.xserver.displayManager.sessionCommands`  = "setxkbmap -keycodes media";

If you are manually starting the X server, you should set the argument
``-xkbdir /etc/X11/xkb``, otherwise X won't find your layout files.
For example with :command:`xinit` run

::

    $ xinit -- -xkbdir /etc/X11/xkb

To learn how to write layouts take a look at the XKB
`documentation <https://www.x.org/releases/current/doc/xorg-docs/input/XKB-Enhancing.html#Defining_New_Layouts>`_. More example layouts can also be found
`here <https://wiki.archlinux.org/index.php/X_KeyBoard_extension#Basic_examples>`_.

