.. _environment-info:

############
Environments
############

The Environment specifies how the build image should be configured for the
running job. You can specify the operating system and version to use,
additional software repositories to configure and additional packages to
install before the job is run.

.. _environment_example:

*******
Example
*******

.. highlight:: json
.. code:: json

  "environment" : {
    "os" : "ubuntu_14_04",
    "repos" : [
      {
        "url" : "ppa:brightbox/ruby-ng"
      }
    ],
    "packages" : [
      "ruby2.3",
      "ruby2.3-dev",
      "build-essential",
      "cmake"
    ]
  }

****************
Operating system
****************

===== ====================================================
Name  Description
===== ====================================================
os    Name & version of the operating system to use
===== ====================================================

The ``os`` option defines which operating system, and which version of the
operating system, to use when creating the build image, for example
``ubuntu_14_04``, ``fedora_24``

The Cyclid server will map the requested operating system to an image. The
actual image used will depend on which Builder plugin is configured on the
server. In addition, the server must have a
:ref:`Provisioner <provisioners-plugin>` plugin that supports the requested
operating system.

For example, if your Cyclid server is configured to use Amazon Web Services to
provide build images and request an ``ubuntu_14_04`` image, the AWS Builder
may create an instance using the ``ami-1b0d920c`` AMI; but a different Cyclid
server configured to use LXD for its build images map download the pre-built
Ubuntu Trusty LXD image.

***************
Additional data
***************

Additional environment options can be provided. Exactly which options you can
use depends on which Provisioner is used I.e. they are operating system
dependent.

For example, in :ref:`the example above <environment_example>` the Ubuntu
provisioner allows you to define a list of additional Ubuntu PPA repositories,
and a list of packages to install into the build image.

See the documentation for :ref:`Provisioner plugins <provisioners-plugin>` for
more information on which options are supported for each provisioner.
