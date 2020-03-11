

.. _sec-obtaining:

Obtaining NixOS
---------------

NixOS ISO images can be downloaded from the
`NixOS download
page <http://nixos.org/nixos/download.html>`_. There are a number of installation options. If you happen to
have an optical drive and a spare CD, burning the image to CD and booting
from that is probably the easiest option. Most people will need to prepare a
USB stick to boot from. :ref:`sec-booting-from-usb` describes the
preferred method to prepare a USB stick. A number of alternative methods are
presented in the
`NixOS
Wiki <https://nixos.wiki/wiki/NixOS_Installation_Guide#Making_the_installation_media>`_.

As an alternative to installing NixOS yourself, you can get a running NixOS
system through several other means:

-

  Using virtual appliances in Open Virtualization Format (OVF) that can be
  imported into VirtualBox. These are available from the
  `NixOS download
  page <http://nixos.org/nixos/download.html>`_.

-

  Using AMIs for Amazon’s EC2. To find one for your region and instance
  type, please refer to the
  `list
  of most recent AMIs <https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/virtualisation/ec2-amis.nix>`_.

-

  Using NixOps, the NixOS-based cloud deployment tool, which allows you to
  provision VirtualBox and EC2 NixOS instances from declarative
  specifications. Check out the
  `NixOps homepage <https://nixos.org/nixops>`_ for
  details.


