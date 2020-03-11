

.. _sec-instaling-virtualbox-guest:

Installing in a VirtualBox guest
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Installing NixOS into a VirtualBox guest is convenient for users who want to
try NixOS without installing it on bare metal. If you want to use a pre-made
VirtualBox appliance, it is available at
`the downloads
page <https://nixos.org/nixos/download.html>`_. If you want to set up a VirtualBox guest manually, follow these
instructions:

#.

   Add a New Machine in VirtualBox with OS Type "Linux / Other Linux"

#.

   Base Memory Size: 768 MB or higher.

#.

   New Hard Disk of 8 GB or higher.

#.

   Mount the CD-ROM with the NixOS ISO (by clicking on CD/DVD-ROM)

#.

   Click on Settings / System / Processor and enable PAE/NX

#.

   Click on Settings / System / Acceleration and enable "VT-x/AMD-V"
   acceleration

#.

   Click on Settings / Display / Screen and select VBoxVGA as Graphics Controller

#.

   Save the settings, start the virtual machine, and continue installation
   like normal

There are a few modifications you should make in configuration.nix. Enable
booting:

.. parsed-literal::

    :ref:`opt-boot.loader.grub.device`  = "/dev/sda";

Also remove the fsck that runs at startup. It will always fail to run,
stopping your boot until you press ``*``.

.. parsed-literal::

    :ref:`opt-boot.initrd.checkJournalingFS`  = false;

Shared folders can be given a name and a path in the host system in the
VirtualBox settings (Machine / Settings / Shared Folders, then click on the
"Add" icon). Add the following to the
``/etc/nixos/configuration.nix`` to auto-mount them. If you do
not add ``"nofail"``, the system will no boot properly. The
same goes for disabling ``rngd`` which is normally used to get
randomness but this does not work in virtual machines.

::

    { config, pkgs, ...} :
    {
      security.rngd.enable = false; // otherwise vm will not boot
      ...

      fileSystems."/virtualboxshare" = {
        fsType = "vboxsf";
        device = "nameofthesharedfolder";
        options = [ "rw" "nofail" ];
      };
    }

The folder will be available directly under the root directory.

