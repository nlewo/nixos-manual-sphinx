

.. _sec-ipv6:

IPv6 Configuration
~~~~~~~~~~~~~~~~~~

IPv6 is enabled by default. Stateless address autoconfiguration is used to
automatically assign IPv6 addresses to all interfaces. You can disable IPv6
support globally by setting:

.. parsed-literal::

    :ref:`opt-networking.enableIPv6`  = false;

You can disable IPv6 on a single interface using a normal sysctl (in this
example, we use interface ``eth0``):

.. parsed-literal::

    :ref:`opt-boot.kernel.sysctl`."net.ipv6.conf.eth0.disable_ipv6" = true;

As with IPv4 networking interfaces are automatically configured via DHCPv6.
You can configure an interface manually:

::

    `networking.interfaces.eth0.ipv6.addresses <None>`_  = [ {
      address = "fe00:aa:bb:cc::2";
      prefixLength = 64;
    } ];

For configuring a gateway, optionally with explicitly specified interface:

.. parsed-literal::

    :ref:`opt-networking.defaultGateway6`  = {
      address = "fe00::1";
      interface = "enp0s3";
    };

See :ref:`sec-ipv4` for similar examples and additional
information.

