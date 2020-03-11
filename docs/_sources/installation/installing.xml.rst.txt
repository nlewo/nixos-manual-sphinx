

.. _sec-installation:

Installing NixOS
----------------

.. _sec-installation-booting:

Booting the system
~~~~~~~~~~~~~~~~~~

NixOS can be installed on BIOS or UEFI systems. The procedure for a UEFI
installation is by and large the same as a BIOS installation. The
differences are mentioned in the steps that follow.

The installation media can be burned to a CD, or now more commonly, "burned"
to a USB drive (see :ref:`sec-booting-from-usb`).

The installation media contains a basic NixOS installation. When it’s
finished booting, it should have detected most of your hardware.

The NixOS manual is available on virtual console 8 (press Alt+F8 to access)
or by running :command:`nixos-help`.

You are logged-in automatically as ``nixos``.
The ``nixos`` user account has an empty password so you
can use :command:`sudo` without a password.

If you downloaded the graphical ISO image, you can run :command:`systemctl
start display-manager` to start the desktop environment. If you want to continue on the
terminal, you can use :command:`loadkeys` to switch to your
preferred keyboard layout. (We even provide neo2 via :command:`loadkeys de
neo`!)

.. _sec-installation-booting-networking:

Networking in the installer
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The boot process should have brought up networking (check :command:`ip
a`). Networking is necessary for the installer, since it will
download lots of stuff (such as source tarballs or Nixpkgs channel
binaries). It’s best if you have a DHCP server on your network. Otherwise
configure networking manually using :command:`ifconfig`.

To manually configure the network on the graphical installer, first disable
network-manager with :command:`systemctl stop NetworkManager`.

To manually configure the wifi on the minimal installer, run
:command:`wpa_supplicant -B -i interface -c <(wpa_passphrase 'SSID'
'key')`.

If you would like to continue the installation from a different machine you
need to activate the SSH daemon via :command:`systemctl start
sshd`. You then must set a password for either ``root`` or
``nixos`` with :command:`passwd` to be able to login.

.. _sec-installation-partitioning:

Partitioning and formatting
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The NixOS installer doesn’t do any partitioning or formatting, so you need
to do that yourself.

The NixOS installer ships with multiple partitioning tools. The examples
below use :command:`parted`, but also provides
:command:`fdisk`, :command:`gdisk`,
:command:`cfdisk`, and :command:`cgdisk`.

The recommended partition scheme differs depending if the computer uses
*Legacy Boot* or *UEFI*.

.. _sec-installation-partitioning-UEFI:

UEFI (GPT)
~~~~~~~~~~

Here's an example partition scheme for UEFI, using
:file:`/dev/sda` as the device.

.. note::

   You can safely ignore :command:`parted`'s informational message
   about needing to update /etc/fstab.

#.

   Create a *GPT* partition table.
   ::

       # parted /dev/sda -- mklabel gpt

#.

   Add the *root* partition. This will fill the disk
   except for the end part, where the swap will live, and the space left in
   front (512MiB) which will be used by the boot partition.
   ::

       # parted /dev/sda -- mkpart primary 512MiB -8GiB

#.

   Next, add a *swap* partition. The size required will
   vary according to needs, here a 8GiB one is created.
   ::

       # parted /dev/sda -- mkpart primary linux-swap -8GiB 100%

   .. note::

      The swap partition size rules are no different than for other Linux
      distributions.

#.

   Finally, the *boot* partition. NixOS by default uses
   the ESP (EFI system partition) as its */boot*
   partition. It uses the initially reserved 512MiB at the start of the
   disk.
   ::

       # parted /dev/sda -- mkpart ESP fat32 1MiB 512MiB# parted /dev/sda -- set 3 boot on

Once complete, you can follow with
:ref:`sec-installation-partitioning-formatting`.

.. _sec-installation-partitioning-MBR:

Legacy Boot (MBR)
~~~~~~~~~~~~~~~~~

Here's an example partition scheme for Legacy Boot, using
:file:`/dev/sda` as the device.

.. note::

   You can safely ignore :command:`parted`'s informational message
   about needing to update /etc/fstab.

#.

   Create a *MBR* partition table.
   ::

       # parted /dev/sda -- mklabel msdos

#.

   Add the *root* partition. This will fill the the disk
   except for the end part, where the swap will live.
   ::

       # parted /dev/sda -- mkpart primary 1MiB -8GiB

#.

   Finally, add a *swap* partition. The size required
   will vary according to needs, here a 8GiB one is created.
   ::

       # parted /dev/sda -- mkpart primary linux-swap -8GiB 100%

   .. note::

      The swap partition size rules are no different than for other Linux
      distributions.

Once complete, you can follow with
:ref:`sec-installation-partitioning-formatting`.

.. _sec-installation-partitioning-formatting:

Formatting
~~~~~~~~~~

Use the following commands:

-

  For initialising Ext4 partitions: :command:`mkfs.ext4`. It is
  recommended that you assign a unique symbolic label to the file system
  using the option ,
  since this makes the file system configuration independent from device
  changes. For example:
  ::

      # mkfs.ext4 -L nixos /dev/sda1

-

  For creating swap partitions: :command:`mkswap`. Again it’s
  recommended to assign a label to the swap partition: . For example:
  ::

      # mkswap -L swap /dev/sda2

-

  UEFI systems

      For creating boot partitions: :command:`mkfs.fat`. Again
      it’s recommended to assign a label to the boot partition:
      . For example:
      ::

          # mkfs.fat -F 32 -n boot /dev/sda3

-

  For creating LVM volumes, the LVM commands, e.g.,
  :command:`pvcreate`, :command:`vgcreate`, and
  :command:`lvcreate`.

-

  For creating software RAID devices, use :command:`mdadm`.

.. _sec-installation-installing:

Installing
~~~~~~~~~~

#.

   Mount the target file system on which NixOS should be installed on
   :file:`/mnt`, e.g.
   ::

       # mount /dev/disk/by-label/nixos /mnt

#.

   UEFI systems

       Mount the boot file system on :file:`/mnt/boot`, e.g.
       ::

           # mkdir -p /mnt/boot# mount /dev/disk/by-label/boot /mnt/boot

#.

   If your machine has a limited amount of memory, you may want to activate
   swap devices now (:command:`swapon
   *device*`). The installer (or rather,
   the build actions that it may spawn) may need quite a bit of RAM,
   depending on your configuration.
   ::

       # swapon /dev/sda2

#.

   You now need to create a file
   :file:`/mnt/etc/nixos/configuration.nix` that specifies the
   intended configuration of the system. This is because NixOS has a
   *declarative* configuration model: you create or edit a
   description of the desired configuration of your system, and then NixOS
   takes care of making it happen. The syntax of the NixOS configuration file
   is described in :ref:`sec-configuration-syntax`, while a list
   of available configuration options appears in
   :ref:`ch-options`. A minimal example is shown in
   :ref:`ex-config`.

   The command :command:`nixos-generate-config` can generate an
   initial configuration file for you:
   ::

       # nixos-generate-config --root /mnt

   You should then edit :file:`/mnt/etc/nixos/configuration.nix`
   to suit your needs:
   ::

       # nano /mnt/etc/nixos/configuration.nix

   If you’re using the graphical ISO image, other editors may be available
   (such as :command:`vim`). If you have network access, you can also
   install other editors — for instance, you can install Emacs by running
   ``nix-env -f '<nixpkgs>' -iA emacs``.

   BIOS systems

       You *must* set the option
       :ref:`opt-boot.loader.grub.device` to specify on which disk
       the GRUB boot loader is to be installed. Without it, NixOS cannot boot.

   UEFI systems

       You *must* set the option
       :ref:`opt-boot.loader.systemd-boot.enable` to
       ``true``. :command:`nixos-generate-config`
       should do this automatically for new configurations when booted in UEFI
       mode.

       You may want to look at the options starting with

       and

       as well.

   If there are other operating systems running on the machine before
   installing NixOS, the :ref:`opt-boot.loader.grub.useOSProber`
   option can be set to ``true`` to automatically add them to
   the grub menu.

   If you need to configure networking for your machine the configuration
   options are described in :ref:`sec-networking`. In particular,
   while wifi is supported on the installation image, it is not enabled by
   default in the configuration generated by
   :command:`nixos-generate-config`.

   Another critical option is , specifying the
   file systems that need to be mounted by NixOS. However, you typically
   don’t need to set it yourself, because
   :command:`nixos-generate-config` sets it automatically in
   :file:`/mnt/etc/nixos/hardware-configuration.nix` from your
   currently mounted file systems. (The configuration file
   :file:`hardware-configuration.nix` is included from
   :file:`configuration.nix` and will be overwritten by future
   invocations of :command:`nixos-generate-config`; thus, you
   generally should not modify it.) Additionally, you may want to look at
   `Hardware
   configuration for known-hardware <https://github.com/NixOS/nixos-hardware>`_ at this point or after
   installation.

   .. note::

      Depending on your hardware configuration or type of file system, you may
      need to set the option to
      include the kernel modules that are necessary for mounting the root file
      system, otherwise the installed system will not be able to boot. (If this
      happens, boot from the installation media again, mount the target file
      system on :file:`/mnt`, fix
      :file:`/mnt/etc/nixos/configuration.nix` and rerun
      :file:`nixos-install`.) In most cases,
      :command:`nixos-generate-config` will figure out the required
      modules.

#.

   Do the installation:
   ::

       # nixos-install

   This will install your system based on the configuration you provided.
   If anything fails due to a configuration problem or any other issue
   (such as a network outage while downloading binaries from the NixOS
   binary cache), you can re-run :command:`nixos-install` after
   fixing your :file:`configuration.nix`.

   As the last step, :command:`nixos-install` will ask you to set the
   password for the ``root`` user, e.g.
   ::

       setting root password...
       Enter new UNIX password: \***
       Retype new UNIX password: \***

   .. note::

      For unattended installations, it is possible to use
      :command:`nixos-install --no-root-passwd` in order to disable
      the password prompt entirely.

#.

   If everything went well:
   ::

       # reboot

#.

   You should now be able to boot into the installed NixOS. The GRUB boot
   menu shows a list of *available configurations*
   (initially just one). Every time you change the NixOS configuration (see
   `Changing Configuration <None>`_
   ), a new item is added to the menu. This allows you to easily roll back to
   a previous configuration if something goes wrong.

   You should log in and change the ``root`` password with
   :command:`passwd`.

   You’ll probably want to create some user accounts as well, which can be
   done with :command:`useradd`:
   ::

       $ useradd -c 'Eelco Dolstra' -m eelco$ passwd eelco

   You may also want to install some software. For instance,
   ::

       $ nix-env -qaP \\*

   shows what packages are available, and
   ::

       $ nix-env -f '<nixpkgs>' -iA w3m

   installs the ``w3m`` browser.

.. _sec-installation-summary:

Installation summary
~~~~~~~~~~~~~~~~~~~~

To summarise, :ref:`ex-install-sequence` shows a typical
sequence of commands for installing NixOS on an empty hard drive (here
:file:`/dev/sda`). :ref:`ex-config` shows a
corresponding configuration Nix expression.

Example partition schemes for NixOS on :file:`/dev/sda` (MBR)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
::

    # parted /dev/sda -- mklabel msdos# parted /dev/sda -- mkpart primary 1MiB -8GiB# parted /dev/sda -- mkpart primary linux-swap -8GiB 100%

Example partition schemes for NixOS on :file:`/dev/sda` (UEFI)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
::

    # parted /dev/sda -- mklabel gpt# parted /dev/sda -- mkpart primary 512MiB -8GiB# parted /dev/sda -- mkpart primary linux-swap -8GiB 100%# parted /dev/sda -- mkpart ESP fat32 1MiB 512MiB# parted /dev/sda -- set 3 boot on

Commands for Installing NixOS on :file:`/dev/sda`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

With a partitioned disk.
::

    # mkfs.ext4 -L nixos /dev/sda1# mkswap -L swap /dev/sda2# swapon /dev/sda2# mkfs.fat -F 32 -n boot /dev/sda3        # (for UEFI systems only)# mount /dev/disk/by-label/nixos /mnt# mkdir -p /mnt/boot                      # (for UEFI systems only)# mount /dev/disk/by-label/boot /mnt/boot # (for UEFI systems only)# nixos-generate-config --root /mnt# nano /mnt/etc/nixos/configuration.nix# nixos-install# reboot

NixOS Configuration
^^^^^^^^^^^^^^^^^^^

.. parsed-literal::

    { config, pkgs, ... }: {
      imports = [
        # Include the results of the hardware scan.
        ./hardware-configuration.nix
      ];

      :ref:`opt-boot.loader.grub.device`  = "/dev/sda";   # (for BIOS systems only)
      :ref:`opt-boot.loader.systemd-boot.enable`  = true; # (for UEFI systems only)

      # Note: setting fileSystems is generally not
      # necessary, since nixos-generate-config figures them out
      # automatically in hardware-configuration.nix.
      #`fileSystems."/".device <None>`_  = "/dev/disk/by-label/nixos";

      # Enable the OpenSSH server.
      services.sshd.enable = true;
    }

.. _sec-installation-additional-notes:

Additional installation notes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. toctree::

   installing-usb.xml.rst

.. toctree::

   installing-pxe.xml.rst

.. toctree::

   installing-virtualbox-guest.xml.rst

.. toctree::

   installing-from-other-distro.xml.rst

.. toctree::

   installing-behind-a-proxy.xml.rst


