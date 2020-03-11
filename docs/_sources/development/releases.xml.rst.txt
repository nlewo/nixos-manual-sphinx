

.. _ch-releases:

Releases
--------

.. _release-process:

Release process
~~~~~~~~~~~~~~~

Going through an example of releasing NixOS 17.09:

.. _one-month-before-the-beta:

One month before the beta
~~~~~~~~~~~~~~~~~~~~~~~~~

-

  Send an email to the nix-devel mailinglist as a warning about upcoming
  beta "feature freeze" in a month.

-

  Discuss with Eelco Dolstra and the community (via IRC, ML) about what
  will reach the deadline. Any issue or Pull Request targeting the release
  should be included in the release milestone.

.. _at-beta-release-time:

At beta release time
~~~~~~~~~~~~~~~~~~~~

-

  `Create
  an issue for tracking Zero Hydra Failures progress. ZHF is an effort to
  get build failures down to zero. <https://github.com/NixOS/nixpkgs/issues/13559>`_

-

  ``git tag -a -s -m "Release 17.09-beta" 17.09-beta
  && git push origin 17.09-beta``

-

  From the master branch run ``git checkout -b
  release-17.09``.

-

  `Make sure a channel is created at http://nixos.org/channels/. <https://github.com/NixOS/nixos-org-configurations/pull/18>`_

-

  `Bump the system.defaultChannel attribute in
  nixos/modules/misc/version.nix <https://github.com/NixOS/nixpkgs/compare/bdf161ed8d21...6b63c4616790>`_

-

  `Update versionSuffix in
  nixos/release.nix <https://github.com/NixOS/nixpkgs/commit/d6b08acd1ccac0d9d502c4b635e00b04d3387f06>`_, use
  ``git rev-list --count 17.09-beta``
  to get the commit count.

-

  ``echo -n "18.03" > .version`` on master.

-

  `Pick a new name for the unstable branch. <https://github.com/NixOS/nixpkgs/commit/b8a4095003e27659092892a4708bb3698231a842>`_

-

  Create a new release notes file for the upcoming release + 1, in this
  case ``rl-1803.xml``.

-

  Create two Hydra jobsets: release-17.09 and release-17.09-small with
  ``stableBranch`` set to false.

-

  Remove attributes that we know we will not be able to support,
  especially if there is a stable alternative. E.g. Check that our
  Linux kernels'
  `projected end-of-life <https://www.kernel.org/category/releases.html>`_ are after our release projected
  end-of-life

-

  Edit changelog at
  ``nixos/doc/manual/release-notes/rl-1709.xml`` (double
  check desktop versions are noted)

  -

    Get all new NixOS modules ``git diff
    release-17.03..release-17.09 nixos/modules/module-list.nix|grep
    ^+``

  -

    Note systemd, kernel, glibc and Nix upgrades.

.. _during-beta:

During Beta
~~~~~~~~~~~

-

  Monitor the master branch for bugfixes and minor updates and cherry-pick
  them to the release branch.

.. _before-the-final-release:

Before the final release
~~~~~~~~~~~~~~~~~~~~~~~~

-

  Re-check that the release notes are complete.

-

  Release Nix (currently only Eelco Dolstra can do that).
  `Make sure fallback is updated. <https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/installer/tools/nix-fallback-paths.nix>`_

-

  `Update README.md with new stable NixOS version information. <https://github.com/NixOS/nixpkgs/commit/40fd9ae3ac8048758abdcfc7d28a78b5f22fe97e>`_

-

  Change ``stableBranch`` to ``true`` in Hydra and wait for
  the channel to update.

.. _at-final-release-time:

At final release time
~~~~~~~~~~~~~~~~~~~~~

-

  ``git tag -s -a -m "Release 15.09" 15.09``

-

  Update "Chapter 4. Upgrading NixOS" section of the manual to match
  new stable release version.

-

  Update the
  `NIXOS_SERIES <https://github.com/NixOS/nixos-homepage/commit/2a37975d5a617ecdfca94696242b6f32ffcba9f1>`_
  in the
  `nixos-homepage <https://github.com/NixOS/nixos-homepage>`_
  repository.

-

  Get number of commits for the release: ``git log
  release-14.04..release-14.12 --format=%an|wc -l``

-

  Commits by contributor: ``git log release-14.04..release-14.12
  --format=%an|sort|uniq -c|sort -rn``

-

  Create a new topic on `the
  Discourse instance <https://discourse.nixos.org/>`_ to announce the release with the above information.
  Best to check how previous email was formulated to see what needs to be
  included.

.. _release-managers:

Release Management Team
~~~~~~~~~~~~~~~~~~~~~~~

For each release there are two release managers. After each release the
release manager having managed two releases steps down and the release
management team of the last release appoints a new release manager.

This makes sure a release management team always consists of one release
manager who already has managed one release and one release manager being
introduced to their role, making it easier to pass on knowledge and
experience.

Release managers for the current NixOS release are tracked by GitHub team
`@NixOS/nixos-release-managers <https://github.com/orgs/NixOS/teams/nixos-release-managers/members>`_.

A release manager's role and responsibilities are:

-

  manage the release process

-

  start discussions about features and changes for a given release

-

  create a roadmap

-

  release in cooperation with Eelco Dolstra

-

  decide which bug fixes, features, etc... get backported after a release

.. _release-schedule:

Release schedule
~~~~~~~~~~~~~~~~

Date

Event

2016-07-25

Send email to nix-dev about upcoming branch-off

2016-09-01

``release-16.09`` branch and corresponding jobsets are created,
change freeze

2016-09-30

NixOS 16.09 released


