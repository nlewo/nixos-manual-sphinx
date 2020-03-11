

.. _sec-writing-documentation:

Writing NixOS Documentation
---------------------------

As NixOS grows, so too does the need for a catalogue and explanation of its
extensive functionality. Collecting pertinent information from disparate
sources and presenting it in an accessible style would be a worthy
contribution to the project.

.. _sec-writing-docs-building-the-manual:

Building the Manual
~~~~~~~~~~~~~~~~~~~

The DocBook sources of the :ref:`book-nixos-manual` are in the
`:file:`nixos/doc/manual` <https://github.com/NixOS/nixpkgs/tree/master/nixos/doc/manual>`_
subdirectory of the Nixpkgs repository.

You can quickly validate your edits with :command:`make`:

::

    $ cd /path/to/nixpkgs/nixos/doc/manual
    $ make

Once you are done making modifications to the manual, it's important to
build it before committing. You can do that as follows:

::

    nix-build nixos/release.nix -A manual.x86_64-linux

When this command successfully finishes, it will tell you where the manual
got generated. The HTML will be accessible through the
:file:`result` symlink at
:file:`./result/share/doc/nixos/index.html`.

.. _sec-writing-docs-editing-docbook-xml:

Editing DocBook XML
~~~~~~~~~~~~~~~~~~~

For general information on how to write in DocBook, see
`DocBook
5: The Definitive Guide <http://www.docbook.org/tdg5/en/html/docbook.html>`_.

Emacs nXML Mode is very helpful for editing DocBook XML because it validates
the document as you write, and precisely locates errors. To use it, see
:ref:`sec-emacs-docbook-xml`.

`Pandoc <http://pandoc.org>`_ can generate DocBook XML
from a multitude of formats, which makes a good starting point.

Pandoc invocation to convert GitHub-Flavoured MarkDown to DocBook 5 XML
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
::

    pandoc -f markdown_github -t docbook5 docs.md -o my-section.md

Pandoc can also quickly convert a single :file:`section.xml` to
HTML, which is helpful when drafting.

Sometimes writing valid DocBook is simply too difficult. In this case,
submit your documentation updates in a
`GitHub
Issue <https://github.com/NixOS/nixpkgs/issues/new>`_ and someone will handle the conversion to XML for you.

.. _sec-writing-docs-creating-a-topic:

Creating a Topic
~~~~~~~~~~~~~~~~

You can use an existing topic as a basis for the new topic or create a topic
from scratch.

Keep the following guidelines in mind when you create and add a topic:

-

  The NixOS
  `book <http://www.docbook.org/tdg5/en/html/book.html>`_
  element is in :file:`nixos/doc/manual/manual.xml`. It
  includes several
  `parts <http://www.docbook.org/tdg5/en/html/book.html>`_
  which are in subdirectories.

-

  Store the topic file in the same directory as the part to
  which it belongs. If your topic is about configuring a NixOS module, then
  the XML file can be stored alongside the module definition
  :file:`nix` file.

-

  If you include multiple words in the file name, separate the words with a
  dash. For example: :file:`ipv6-config.xml`.

-

  Make sure that the xml:id value is unique. You can use
  abbreviations if the ID is too long. For example:
  ``nixos-config``.

-

  Determine whether your topic is a chapter or a section. If you are
  unsure, open an existing topic file and check whether the main element is
  chapter or section.

.. _sec-writing-docs-adding-a-topic:

Adding a Topic to the Book
~~~~~~~~~~~~~~~~~~~~~~~~~~

Open the parent XML file and add an ``xi:include`` element to
the list of chapters with the file name of the topic that you created. If
you created a section, you add the file to the chapter
file. If you created a chapter, you add the file to the
part file.

If the topic is about configuring a NixOS module, it can be automatically
included in the manual by using the ``meta.doc`` attribute.
See :ref:`sec-meta-attributes` for an explanation.


