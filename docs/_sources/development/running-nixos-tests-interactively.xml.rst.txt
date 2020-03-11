

.. _sec-running-nixos-tests-interactively:

Running Tests interactively
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The test itself can be run interactively. This is particularly useful when
developing or debugging a test:
::

    $ nix-build nixos/tests/login.nix -A driver$ ./result/bin/nixos-test-driver
    starting VDE switch for network 1>

You can then take any Python statement, e.g.
::

    > start_all()> test_script()> machine.succeed("touch /tmp/foo")> print(machine.succeed("pwd")) # Show stdout of command

The function :command:`test_script` executes the entire test script
and drops you back into the test driver command line upon its completion.
This allows you to inspect the state of the VMs after the test (e.g. to debug
the test script).

To just start and experiment with the VMs, run:
::

    $ nix-build nixos/tests/login.nix -A driver$ ./result/bin/nixos-run-vms

The script :command:`nixos-run-vms` starts the virtual machines
defined by test.

The machine state is kept across VM restarts in
:file:`/tmp/vm-state-```machinename``.

