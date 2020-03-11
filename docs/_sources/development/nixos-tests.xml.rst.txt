

.. _sec-nixos-tests:

NixOS Tests
-----------

When you add some feature to NixOS, you should write a test for it. NixOS
tests are kept in the directory
`nixos/tests <https://github.com/NixOS/nixpkgs/tree/master/nixos/tests>`_,
and are executed (using Nix) by a testing framework that automatically starts
one or more virtual machines containing the NixOS system(s) required for the
test.

.. toctree::

   writing-nixos-tests.xml.rst

.. toctree::

   running-nixos-tests.xml.rst

.. toctree::

   running-nixos-tests-interactively.xml.rst

