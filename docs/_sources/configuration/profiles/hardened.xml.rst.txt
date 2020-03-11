

.. _sec-profile-hardened:

Hardened
~~~~~~~~

A profile with most (vanilla) hardening options enabled by default,
potentially at the cost of features and performance.

This includes a hardened kernel, and limiting the system information
available to processes through the :file:`/sys` and
:file:`/proc` filesystems. It also disables the User Namespaces
feature of the kernel, which stops Nix from being able to build anything
(this particular setting can be overriden via
:ref:`opt-security.allowUserNamespaces`). See the
``profile source`` for further detail on which settings are altered.

