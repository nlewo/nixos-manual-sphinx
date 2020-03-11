

.. _ch-file-systems:

File Systems
------------

You can define file systems using the
configuration option. For instance, the following definition causes NixOS to
mount the Ext4 file system on device
:file:`/dev/disk/by-label/data` onto the mount point
:file:`/data`:

.. parsed-literal::

    :ref:`opt-fileSystems`."/data" =
      { device = "/dev/disk/by-label/data";
        fsType = "ext4";
      };

Mount points are created automatically if they don’t already exist. For
,
it’s best to use the topology-independent device aliases in
:file:`/dev/disk/by-label` and
:file:`/dev/disk/by-uuid`, as these don’t change if the
topology changes (e.g. if a disk is moved to another IDE controller).

You can usually omit the file system type
(),
since :command:`mount` can usually detect the type and load the
necessary kernel module automatically. However, if the file system is needed
at early boot (in the initial ramdisk) and is not ``ext2``,
``ext3`` or ``ext4``, then it’s best to
specify to ensure that the kernel module is
available.

.. note::

   System startup will fail if any of the filesystems fails to mount, dropping
   you to the emergency shell. You can make a mount asynchronous and
   non-critical by adding
   ```options <None>`_ = [
   "nofail" ];``.

.. toctree::

   luks-file-systems.xml.rst

