

.. _ch-profiles:

Profiles
--------

In some cases, it may be desirable to take advantage of commonly-used,
predefined configurations provided by nixpkgs, but different from those that
come as default. This is a role fulfilled by NixOS's Profiles, which come as
files living in :file:`<nixpkgs/nixos/modules/profiles>`.
That is to say, expected usage is to add them to the imports list of your
:file:`/etc/configuration.nix` as such:

::

      imports = [
       <nixpkgs/nixos/modules/profiles/profile-name.nix>
      ];

Even if some of these profiles seem only useful in the context of install
media, many are actually intended to be used in real installs.

What follows is a brief explanation on the purpose and use-case for each
profile. Detailing each option configured by each one is out of scope.

.. toctree::

   profiles/all-hardware.xml.rst

.. toctree::

   profiles/base.xml.rst

.. toctree::

   profiles/clone-config.xml.rst

.. toctree::

   profiles/demo.xml.rst

.. toctree::

   profiles/docker-container.xml.rst

.. toctree::

   profiles/graphical.xml.rst

.. toctree::

   profiles/hardened.xml.rst

.. toctree::

   profiles/headless.xml.rst

.. toctree::

   profiles/installation-device.xml.rst

.. toctree::

   profiles/minimal.xml.rst

.. toctree::

   profiles/qemu-guest.xml.rst

