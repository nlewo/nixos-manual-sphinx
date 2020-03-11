

.. _sec-installing-behind-proxy:

Installing behind a proxy
~~~~~~~~~~~~~~~~~~~~~~~~~

To install NixOS behind a proxy, do the following before running
``nixos-install``.

#.

   Update proxy configuration in
   ``/mnt/etc/nixos/configuration.nix`` to keep the internet
   accessible after reboot.

   ::

       networking.proxy.default = "http://user:password@proxy:port/";
       networking.proxy.noProxy = "127.0.0.1,localhost,internal.domain";

#.

   Setup the proxy environment variables in the shell where you are running
   ``nixos-install``.

   ::

       # proxy_url="http://user:password@proxy:port/"
       # export http_proxy="$proxy_url"
       # export HTTP_PROXY="$proxy_url"
       # export https_proxy="$proxy_url"
       # export HTTPS_PROXY="$proxy_url"

.. note::

   If you are switching networks with different proxy configurations, use the
   ``nesting.clone`` option in
   ``configuration.nix`` to switch proxies at runtime. Refer to
   :ref:`ch-options` for more information.
   
