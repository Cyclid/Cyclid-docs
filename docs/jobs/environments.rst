.. _environment-info:

############
Environments
############

The Environment specifies how the build image should be configured for the
running job. You can specify the operating system and version to use,
additional software repositories to configure and additional packages to
install before the job is run. Different Builders & Provisioners may support
additional options, such as the virtual machine size, or package repository
keys. 

.. _environment_example:

*******
Example
*******

.. highlight:: json
.. code:: json

  "environment" : {
    "os" : "ubuntu_14_04",
    "size" : "small",
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
Build host size
***************

===== ====================================================
Name  Description
===== ====================================================
size  Desired build host size
===== ====================================================

The ``size`` options selects on of five generic build host instance sizes:
``micro``, ``mini``, ``small``, ``medium`` and ``large``.

The Builder plugin can map these generic sizes to the actual virtual machine
type. The actual build host configuration may differ between Builders, so
you should consult the documentation for your :ref:`Builder <builders-plugin>`
for information on what each generic size means.

This option largely only makes sense for virtual machine Build hosts, and is
generally ignored for container type Build hosts.

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
