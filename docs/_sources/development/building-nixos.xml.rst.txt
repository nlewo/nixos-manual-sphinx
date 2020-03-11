

.. _sec-building-cd:

Building Your Own NixOS CD
--------------------------

Building a NixOS CD is as easy as configuring your own computer. The idea is
to use another module which will replace your
:file:`configuration.nix` to configure the system that would be
installed on the CD.

Default CD/DVD configurations are available inside
:file:`nixos/modules/installer/cd-dvd`.
::

    $ git clone https://github.com/NixOS/nixpkgs.git$ cd nixpkgs/nixos$ nix-build -A config.system.build.isoImage -I nixos-config=modules/installer/cd-dvd/installation-cd-minimal.nix default.nix

Before burning your CD/DVD, you can check the content of the image by
mounting anywhere like suggested by the following command:
::

    # mount -o loop -t iso9660 ./result/iso/cd.iso /mnt/iso


