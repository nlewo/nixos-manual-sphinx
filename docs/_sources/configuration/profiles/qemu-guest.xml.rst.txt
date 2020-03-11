

.. _sec-profile-qemu-guest:

QEMU Guest
~~~~~~~~~~

This profile contains common configuration for virtual machines running under
QEMU (using virtio).

It makes virtio modules available on the initrd, sets the system time from
the hardware clock to work around a bug in qemu-kvm, and
`enables rngd <None>`_.

