

.. _sec-writing-modules:

Writing NixOS Modules
---------------------

NixOS has a modular system for declarative configuration. This system
combines multiple *modules* to produce the full system
configuration. One of the modules that constitute the configuration is
:file:`/etc/nixos/configuration.nix`. Most of the others live in
the
`:file:`nixos/modules` <https://github.com/NixOS/nixpkgs/tree/master/nixos/modules>`_
subdirectory of the Nixpkgs tree.

Each NixOS module is a file that handles one logical aspect of the
configuration, such as a specific kind of hardware, a service, or network
settings. A module configuration does not have to handle everything from
scratch; it can use the functionality provided by other modules for its
implementation. Thus a module can *declare* options that
can be used by other modules, and conversely can *define*
options provided by other modules in its own implementation. For example, the
module
`:file:`pam.nix` <https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/security/pam.nix>`_
declares the option that allows other
modules (e.g.
`:file:`sshd.nix` <https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/services/networking/ssh/sshd.nix>`_)
to define PAM services; and it defines the option
(declared by
`:file:`etc.nix` <https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/system/etc/etc.nix>`_)
to cause files to be created in :file:`/etc/pam.d`.

.. _para-module-syn:

In :ref:`sec-configuration-syntax`, we saw the following structure
of NixOS modules:

::

    { config, pkgs, ... }:

    { *option definitions*
    }

This is actually an *abbreviated* form of module that only
defines options, but does not declare any. The structure of full NixOS
modules is shown in :ref:`ex-module-syntax`.

Structure of NixOS Modules
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    { config, pkgs, ... }:

    {
      imports =
        [ *paths of other modules*
        ];

      options = {
        *option declarations*
      };

      config = {
        *option definitions*
      };
    }

The meaning of each part is as follows.

This line makes the current Nix expression a function. The variable
``pkgs`` contains Nixpkgs, while ``config``
contains the full system configuration. This line can be omitted if there
is no reference to ``pkgs`` and ``config``
inside the module.

This list enumerates the paths to other NixOS modules that should be
included in the evaluation of the system configuration. A default set of
modules is defined in the file
:file:`modules/module-list.nix`. These don't need to be added
in the import list.

The attribute ``options`` is a nested set of
*option declarations* (described below).

The attribute ``config`` is a nested set of
*option definitions* (also described below).

:ref:`locate-example` shows a module that handles the regular
update of the “locate” database, an index of all files in the file
system. This module declares two options that can be defined by other modules
(typically the user’s :file:`configuration.nix`):
(whether the database should be
updated) and (when the update
should be done). It implements its functionality by defining two options
declared by other modules: (the set of all
systemd services) and (the list of commands
to be executed periodically by :command:`systemd`).

NixOS Module for the “locate” Service
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    { config, lib, pkgs, ... }:

    with lib;

    let
      cfg = config.services.locate;
    in {
      options.services.locate = {
        enable = mkOption {
          type = types.bool;
          default = false;
          description = ''
            If enabled, NixOS will periodically update the database of
            files used by the :command:`locate`  command.
          '';
        };

        interval = mkOption {
          type = types.str;
          default = "02:15";
          example = "hourly";
          description = ''
            Update the locate database at this interval. Updates by
            default at 2:15 AM every day.

            The format is described in
            systemd.time
    7.
          '';
        };

        # Other options omitted for documentation
      };

      config = {
        systemd.services.update-locatedb =
          { description = "Update Locate Database";
            path  = [ pkgs.su ];
            script =
              ''
                mkdir -m 0755 -p $(dirname ${toString cfg.output})
                exec updatedb \
                  --localuser=${cfg.localuser} \
                  ${optionalString (!cfg.includeStore) "--prunepaths='/nix/store'"} \
                  --output=${toString cfg.output} ${concatStringsSep " " cfg.extraFlags}
              '';
          };

        systemd.timers.update-locatedb = mkIf cfg.enable
          { description = "Update timer for locate database";
            partOf      = [ "update-locatedb.service" ];
            wantedBy    = [ "timers.target" ];
            timerConfig.OnCalendar = cfg.interval;
          };
      };
    }

.. toctree::

   option-declarations.xml.rst

.. toctree::

   option-types.xml.rst

.. toctree::

   option-def.xml.rst

.. toctree::

   assertions.xml.rst

.. toctree::

   meta-attributes.xml.rst

.. toctree::

   importing-modules.xml.rst

.. toctree::

   replace-modules.xml.rst

