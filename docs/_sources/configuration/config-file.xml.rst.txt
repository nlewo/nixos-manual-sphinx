

.. _sec-configuration-file:

NixOS Configuration File
~~~~~~~~~~~~~~~~~~~~~~~~

The NixOS configuration file generally looks like this:

::

    { config, pkgs, ... }:

    { *option definitions*
    }

The first line (``{ config, pkgs, ... }:``) denotes that this
is actually a function that takes at least the two arguments
``config`` and ``pkgs``. (These are explained
later.) The function returns a *set* of option definitions
(``{ *...* }``). These definitions
have the form ``*name* =
*value*``, where
*name* is the name of an option and
*value* is its value. For example,

.. parsed-literal::

    { config, pkgs, ... }:

    { :ref:`opt-services.httpd.enable`  = true;
      :ref:`opt-services.httpd.adminAddr`  = "alice@example.org";
      `services.httpd.virtualHosts.localhost.documentRoot <None>`_  = "/webroot";
    }

defines a configuration with three option definitions that together enable
the Apache HTTP Server with :file:`/webroot` as the document
root.

Sets can be nested, and in fact dots in option names are shorthand for
defining a set containing another set. For instance,
:ref:`opt-services.httpd.enable` defines a set named
``services`` that contains a set named
``httpd``, which in turn contains an option definition named
``enable`` with value ``true``. This means that
the example above can also be written as:

::

    { config, pkgs, ... }:

    { services = {
        httpd = {
          enable = true;
          adminAddr = "alice@example.org";
          virtualHosts = {
            localhost = {
              documentRoot = "/webroot";
            };
          };
        };
      };
    }

which may be more convenient if you have lots of option definitions that
share the same prefix (such as ``services.httpd``).

NixOS checks your option definitions for correctness. For instance, if you
try to define an option that doesn’t exist (that is, doesn’t have a
corresponding *option declaration*),
:command:`nixos-rebuild` will give an error like:
::

    The option \`services.httpd.enable' defined in \`/etc/nixos/configuration.nix' does not exist.

Likewise, values in option definitions must have a correct type. For
instance, must be a Boolean
(``true`` or ``false``). Trying to give it a
value of another type, such as a string, will cause an error:
::

    The option value \`services.httpd.enable' in \`/etc/nixos/configuration.nix' is not a boolean.

Options have various types of values. The most important are:

Strings

    Strings are enclosed in double quotes, e.g.

    .. parsed-literal::

        :ref:`opt-networking.hostName`  = "dexter";

    Special characters can be escaped by prefixing them with a backslash
    (e.g. ``\\"``).

    Multi-line strings can be enclosed in *double single
    quotes*, e.g.

    .. parsed-literal::

        :ref:`opt-networking.extraHosts`  =
          ''
            127.0.0.2 other-localhost
            10.0.0.1 server
          '';

    The main difference is that it strips from each line a number of spaces
    equal to the minimal indentation of the string as a whole (disregarding
    the indentation of empty lines), and that characters like
    ``"`` and ``\\`` are not special (making it
    more convenient for including things like shell code). See more info
    about this in the Nix manual
    `here <https://nixos.org/nix/manual/#ssec-values>`_.

Booleans

    These can be ``true`` or ``false``, e.g.

    .. parsed-literal::

        :ref:`opt-networking.firewall.enable`  = true;
        :ref:`opt-networking.firewall.allowPing`  = false;

Integers

    For example,

    .. parsed-literal::

        :ref:`opt-boot.kernel.sysctl`."net.ipv4.tcp_keepalive_time" = 60;

    (Note that here the attribute name
    ``net.ipv4.tcp_keepalive_time`` is enclosed in quotes to
    prevent it from being interpreted as a set named ``net``
    containing a set named ``ipv4``, and so on. This is
    because it’s not a NixOS option but the literal name of a Linux kernel
    setting.)

Sets

    Sets were introduced above. They are name/value pairs enclosed in braces,
    as in the option definition

    .. parsed-literal::

        :ref:`opt-fileSystems`."/boot" =
          { device = "/dev/sda1";
            fsType = "ext4";
            options = [ "rw" "data=ordered" "relatime" ];
          };

Lists

    The important thing to note about lists is that list elements are
    separated by whitespace, like this:

    .. parsed-literal::

        :ref:`opt-boot.kernelModules`  = [ "fuse" "kvm-intel" "coretemp" ];

    List elements can be any other type, e.g. sets:

    ::

        swapDevices = [ { device = "/dev/disk/by-label/swap"; } ];

Packages

    Usually, the packages you need are already part of the Nix Packages
    collection, which is a set that can be accessed through the function
    argument ``pkgs``. Typical uses:

    .. parsed-literal::

        :ref:`opt-environment.systemPackages`  =
          [ pkgs.thunderbird
            pkgs.emacs
          ];

        :ref:`opt-services.postgresql.package`  = pkgs.postgresql_10;

    The latter option definition changes the default PostgreSQL package used
    by NixOS’s PostgreSQL service to 10.x. For more information on
    packages, including how to add new ones, see
    :ref:`sec-custom-packages`.


