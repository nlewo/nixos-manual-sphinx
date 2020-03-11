

.. _sec-declarative-package-mgmt:

Declarative Package Management
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

With declarative package management, you specify which packages you want on
your system by setting the option
:ref:`opt-environment.systemPackages`. For instance, adding the
following line to :file:`configuration.nix` enables the Mozilla
Thunderbird email application:

.. parsed-literal::

    :ref:`opt-environment.systemPackages`  = [ pkgs.thunderbird ];

The effect of this specification is that the Thunderbird package from Nixpkgs
will be built or downloaded as part of the system when you run
:command:`nixos-rebuild switch`.

.. note::

   Some packages require additional global configuration such as D-Bus or systemd service registration so adding them to :ref:`opt-environment.systemPackages` might not be sufficient. You are advised to check the `list of options <#ch-options>`_ whether a NixOS module for the package does not exist.

You can get a list of the available packages as follows:
::

    $ nix-env -qaP '\*' --description
    nixos.firefox   firefox-23.0   Mozilla Firefox - the browser, reloaded*...*

The first column in the output is the *attribute name*,
such as ``nixos.thunderbird``.

Note: the ``nixos`` prefix tells us that we want to get the
package from the ``nixos`` channel and works only in CLI tools.

In declarative configuration use ``pkgs`` prefix (variable).

To “uninstall” a package, simply remove it from
:ref:`opt-environment.systemPackages` and run
:command:`nixos-rebuild switch`.

.. toctree::

   customizing-packages.xml.rst

.. toctree::

   adding-custom-packages.xml.rst

