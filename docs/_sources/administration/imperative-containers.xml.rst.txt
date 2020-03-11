

.. _sec-imperative-containers:

Imperative Container Management
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We’ll cover imperative container management using
:command:`nixos-container` first. Be aware that container management
is currently only possible as ``root``.

You create a container with identifier ``foo`` as follows:
::

    # nixos-container create foo

This creates the container’s root directory in
:file:`/var/lib/containers/foo` and a small configuration file
in :file:`/etc/containers/foo.conf`. It also builds the
container’s initial system configuration and stores it in
:file:`/nix/var/nix/profiles/per-container/foo/system`. You can
modify the initial configuration of the container on the command line. For
instance, to create a container that has :command:`sshd` running,
with the given public key for ``root``:
::

    # nixos-container create foo --config '
    :ref:`opt-services.openssh.enable` = true;
    `users.users.root.openssh.authorizedKeys.keys <None>`_ = \["ssh-dss AAAAB3N…"];
    '

By default the next free address in the ``10.233.0.0/16`` subnet will be chosen
as container IP. This behavior can be altered by setting ``--host-address`` and
``--local-address``:
::

    # nixos-container create test --config-file test-container.nix \\
    --local-address 10.235.1.2 --host-address 10.235.1.1

Creating a container does not start it. To start the container, run:
::

    # nixos-container start foo

This command will return as soon as the container has booted and has reached
``multi-user.target``. On the host, the container runs within
a systemd unit called
``container@*container-name*.service``.
Thus, if something went wrong, you can get status info using
:command:`systemctl`:
::

    # systemctl status container@foo

If the container has started successfully, you can log in as root using the
:command:`root-login` operation:
::

    # nixos-container root-login foo
    \[root@foo:~]#

Note that only root on the host can do this (since there is no
authentication). You can also get a regular login prompt using the
:command:`login` operation, which is available to all users on the
host:
::

    # nixos-container login foo
    foo login: alice
    Password: \***

With :command:`nixos-container run`, you can execute arbitrary
commands in the container:
::

    # nixos-container run foo -- uname -a
    Linux foo 3.4.82 #1-NixOS SMP Thu Mar 20 14:44:05 UTC 2014 x86_64 GNU/Linux

There are several ways to change the configuration of the container. First,
on the host, you can edit
``/var/lib/container/*name*/etc/nixos/configuration.nix``,
and run
::

    # nixos-container update foo

This will build and activate the new configuration. You can also specify a
new configuration on the command line:
::

    # nixos-container update foo --config '
    :ref:`opt-services.httpd.enable` = true;
    :ref:`opt-services.httpd.adminAddr` = "foo@example.org";
    :ref:`opt-networking.firewall.allowedTCPPorts` = [ 80 ];
    '

    # curl http://$(nixos-container show-ip foo)/
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">…

However, note that this will overwrite the container’s
:file:`/etc/nixos/configuration.nix`.

Alternatively, you can change the configuration from within the container
itself by running :command:`nixos-rebuild switch` inside the
container. Note that the container by default does not have a copy of the
NixOS channel, so you should run :command:`nix-channel --update`
first.

Containers can be stopped and started using ``nixos-container
stop`` and ``nixos-container start``, respectively, or
by using :command:`systemctl` on the container’s service unit. To
destroy a container, including its file system, do
::

    # nixos-container destroy foo


