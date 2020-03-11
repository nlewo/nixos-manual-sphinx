

.. _sec-luks-file-systems:

LUKS-Encrypted File Systems
~~~~~~~~~~~~~~~~~~~~~~~~~~~

NixOS supports file systems that are encrypted using
*LUKS* (Linux Unified Key Setup). For example, here is how
you create an encrypted Ext4 file system on the device
:file:`/dev/disk/by-uuid/3f6b0024-3a44-4fde-a43a-767b872abe5d`:
::

    # cryptsetup luksFormat /dev/disk/by-uuid/3f6b0024-3a44-4fde-a43a-767b872abe5d

    WARNING!
    ========
    This will overwrite data on /dev/disk/by-uuid/3f6b0024-3a44-4fde-a43a-767b872abe5d irrevocably.

    Are you sure? (Type uppercase yes): YES
    Enter LUKS passphrase: \***
    Verify passphrase: \***

    # cryptsetup luksOpen /dev/disk/by-uuid/3f6b0024-3a44-4fde-a43a-767b872abe5d crypted
    Enter passphrase for /dev/disk/by-uuid/3f6b0024-3a44-4fde-a43a-767b872abe5d: \***

    # mkfs.ext4 /dev/mapper/crypted

To ensure that this file system is automatically mounted at boot time as
:file:`/`, add the following to
:file:`configuration.nix`:

.. parsed-literal::

    `boot.initrd.luks.devices.crypted.device <None>`_  = "/dev/disk/by-uuid/3f6b0024-3a44-4fde-a43a-767b872abe5d";
    :ref:`opt-fileSystems`."/".device = "/dev/mapper/crypted";

Should grub be used as bootloader, and :file:`/boot` is located
on an encrypted partition, it is necessary to add the following grub option:

.. parsed-literal::

    :ref:`opt-boot.loader.grub.enableCryptodisk`  = true;

.. _sec-luks-file-systems-fido2:

FIDO2
~~~~~

NixOS also supports unlocking your LUKS-Encrypted file system using a FIDO2 compatible token. In the following example, we will create a new FIDO2 credential
and add it as a new key to our existing device :file:`/dev/sda2`:

::

    # export FIDO2_LABEL="/dev/sda2 @ $HOSTNAME"
    # fido2luks credential "$FIDO2_LABEL"
    f1d00200108b9d6e849a8b388da457688e3dd653b4e53770012d8f28e5d3b269865038c346802f36f3da7278b13ad6a3bb6a1452e24ebeeaa24ba40eef559b1b287d2a2f80b7

    # fido2luks -i add-key /dev/sda2 f1d00200108b9d6e849a8b388da457688e3dd653b4e53770012d8f28e5d3b269865038c346802f36f3da7278b13ad6a3bb6a1452e24ebeeaa24ba40eef559b1b287d2a2f80b7
    Password:
    Password (again):
    Old password:
    Old password (again):
    Added to key to device /dev/sda2, slot: 2

To ensure that this file system is decrypted using the FIDO2 compatible key, add the following to :file:`configuration.nix`:

::

    `boot.initrd.luks.fido2Support <None>`_  = true;
    `boot.initrd.luks.devices."/dev/sda2".fido2.credential <None>`_  = "f1d00200108b9d6e849a8b388da457688e3dd653b4e53770012d8f28e5d3b269865038c346802f36f3da7278b13ad6a3bb6a1452e24ebeeaa24ba40eef559b1b287d2a2f80b7";

You can also use the FIDO2 passwordless setup, but for security reasons, you might want to enable it only when your device is PIN protected, such as `Trezor <https://trezor.io/>`_.

::

    `boot.initrd.luks.devices."/dev/sda2".fido2.passwordLess <None>`_  = true;


