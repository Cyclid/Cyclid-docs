.. _sources-info:

#######
Sources
#######

Sources define any additional source code, software or tools that your job
requires that must be cloned or checked out into the build image before the job
is run.

.. _sources_example:

*******
Example
*******

.. highlight:: json
.. code:: json

  "sources" : [
    {
       "type": "git",
       "url": "https://github.com/example/Project.git"
    }
  ]

****
Type
****

===== ====================================================
Name  Description
===== ====================================================
type  The source SCM system type
===== ====================================================

The ``type`` option defines the SCM system to use when cloning or checking out
sources, for example ``git`` or ``svn``.

***************
Additional data
***************

Additional options can be provided. Exactly which options you can use depends
on which Source plugin is used I.e. they are SCM system dependent.

For example, in :ref:`the example above <sources_example>` the Git plugin
allows you to define a repository URL, and an optional branch and API token.

See the documentation for :ref:`Source plugins <sources>` for more
information on which options are supported for each source plugin.
