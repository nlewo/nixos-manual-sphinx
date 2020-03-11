

.. _sec-rebooting:

Rebooting and Shutting Down
---------------------------

The system can be shut down (and automatically powered off) by doing:
::

    # shutdown

This is equivalent to running :command:`systemctl poweroff`.

To reboot the system, run
::

    # reboot

which is equivalent to :command:`systemctl reboot`. Alternatively,
you can quickly reboot the system using ``kexec``, which
bypasses the BIOS by directly loading the new kernel into memory:
::

    # systemctl kexec

The machine can be suspended to RAM (if supported) using :command:`systemctl
suspend`, and suspended to disk using :command:`systemctl
hibernate`.

These commands can be run by any user who is logged in locally, i.e. on a
virtual console or in X11; otherwise, the user is asked for authentication.

