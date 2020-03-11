

.. _sec-ssh:

Secure Shell Access
~~~~~~~~~~~~~~~~~~~

Secure shell (SSH) access to your machine can be enabled by setting:

.. parsed-literal::

    :ref:`opt-services.openssh.enable`  = true;

By default, root logins using a password are disallowed. They can be disabled
entirely by setting :ref:`opt-services.openssh.permitRootLogin` to
``"no"``.

You can declaratively specify authorised RSA/DSA public keys for a user as
follows:

.. COMMENT: FIXME: this might not work if the user is unmanaged.

::

    `users.users.alice.openssh.authorizedKeys.keys <None>`_  =
      [ "ssh-dss AAAAB3NzaC1kc3MAAACBAPIkGWVEt4..." ];


