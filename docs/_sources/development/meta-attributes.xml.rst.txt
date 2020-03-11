

.. _sec-meta-attributes:

Meta Attributes
~~~~~~~~~~~~~~~

Like Nix packages, NixOS modules can declare meta-attributes to provide extra
information. Module meta attributes are defined in the
`meta.nix <https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/misc/meta.nix>`_
special module.

``meta`` is a top level attribute like
``options`` and ``config``. Available
meta-attributes are ``maintainers`` and
``doc``.

Each of the meta-attributes must be defined at most once per module file.

::

    { config, lib, pkgs, ... }:
    {
      options = {
        ...
      };

      config = {
        ...
      };

      meta = {
        maintainers = with lib.maintainers; [ ericsagnes ];
        doc = ./default.xml;
      };
    }

``maintainers`` contains a list of the module maintainers.

``doc`` points to a valid DocBook file containing the module
documentation. Its contents is automatically added to
:ref:`ch-configuration`. Changes to a module documentation
have to be checked to not break building the NixOS manual:

::

    $ nix-build nixos/release.nix -A manual


