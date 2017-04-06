.. _provisioners-plugin:

###########
Provisoners
###########

.. contents::
   :depth: 2

.. _ubuntu:

******
Ubuntu
******

The Ubuntu provisioner supports the following environment options.

Repositories
============

===== ====================================================
Name  Description
===== ====================================================
repos A list of additional software (package) repositories
===== ====================================================

The ``repos`` option defines a list of additional software repositories which
are configured on the build image. Each repository definition is defined with
the following options:

+------------+-----------+----------------------------------------------------+
| Name       | Required? | Description                                        |
+============+===========+====================================================+
| url        | Yes       | | An Ubuntu software repository.                   |
|            |           | | Supported URLs are ppa://, http:// & https://    |
+------------+-----------+----------------------------------------------------+
| components | *Note 1*  | | The components to use for Debian style           |
|            |           | | repositories E.g. "main universe".               |
+------------+-----------+----------------------------------------------------+
| key_id     | *Note 1*  | | The GPG key ID to use for Debian style           |
|            |           | | repositories E.g. F5DA5F09C3173AA6               |
+------------+-----------+----------------------------------------------------+

*Notes*

1. Only required for Debian style repositories.

Example
-------

Add the BrightBox Ruby-NG PPA and install the Ruby 2.3 package

.. code-block:: none

  "environment" : {
    "os" : "ubuntu_14_04",
    "repos" : [
      {
        "url" : "ppa:brightbox/ruby-ng"
      }
    ],
    "packages" : [
      "ruby2.3"
    ]
  }


.. _debian:

******
Debian
******

The Debian provisioner supports the following environment options.

Repositories
============

===== ====================================================
Name  Description
===== ====================================================
repos A list of additional software (package) repositories
===== ====================================================

The ``repos`` option defines a list of additional software repositories which
are configured on the build image. Each repository definition is defined with
the following options:

+------------+-----------+----------------------------------------------------+
| Name       | Required? | Description                                        |
+============+===========+====================================================+
| url        | Yes       | | A Debian software repository.                    |
|            |           | | Supported URLs are http:// & https://            |
+------------+-----------+----------------------------------------------------+
| components | Yes       | | The components to use E.g. "main universe"       |
+------------+-----------+----------------------------------------------------+
| key_id     | Yes       | | The GPG key ID to use E.g. F5DA5F09C3173AA       |
+------------+-----------+----------------------------------------------------+

Example
-------

.. code-block:: none

  "environment" : {
    "os" : "debian_jessie",
    "repos" : [
      {
        "url" : "http://www.deb-multimedia.org",
        "components" : "stable main non-free",
        "key_id" : "5C808C2B65558117"
      }
    ],
    "packages" : [
      "ffmpeg"
    ]
  }


.. _fedora:

******
Fedora
******

The Fedora provisioner supports both YUM & DNF package managers. DNF will be
used for Fedora 22 and newer, and YUM will be used for Fedora 21 or older.

The Fedora provisioner supports the following environment options.

Repositories
============

===== ====================================================
Name  Description
===== ====================================================
repos A list of additional software (package) repositories
===== ====================================================

The ``repos`` option defines a list of additional software repositories which
are configured on the build image. Each repository definition is defined with
the following options:

+------------+-----------+----------------------------------------------------+
| Name       | Required? | Description                                        |
+============+===========+====================================================+
| url        | Yes       | | A Fedora software repository file or RPM that    |
|            |           | | configures a software repository.                |
|            |           | | Supported URLs are http:// & https://            |
+------------+-----------+----------------------------------------------------+
| key_url    | No        | | URL of the matching repository signing key       |
+------------+-----------+----------------------------------------------------+

Example
-------

Add the JPackage repository from a repository definition and install the ``ecj``
package:

.. code-block:: none

  "environment" : {
    "os" : "fedora_25",
    "repos" : [
      {
        "url": "http://www.jpackage.org/jpackage50.repo"
      }
    ],
    "packages" : [
      "ecj"
    ]
  }


Add the RPM Fusion repository from an RPM and signing key, and install the
``x265-devel`` package:

.. code-block:: none

  "environment" : {
    "os" : "fedora_25",
    "repos" : [
      {
        "url": "http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-25.noarch.rpm",
        "key_url": "http://ccrma.stanford.edu/planetccrma/apt/configuration/all/RPM-GPG-KEY.planetccrma.txt",
      }
    ],
    "packages" : [
      "x265-devel"
    ]
  }


.. _centos:

******
CentOS
******

The RHEL provisioner will normally use YUM, but will fall back to using RPM
directly for some operations on RHEL 5 and older.

The CentOS provisioner supports the following environment options.

Repositories
============

===== ====================================================
Name  Description
===== ====================================================
repos A list of additional software (package) repositories
===== ====================================================

The ``repos`` option defines a list of additional software repositories which
are configured on the build image. Each repository definition is defined with
the following options:

+------------+-----------+----------------------------------------------------+
| Name       | Required? | Description                                        |
+============+===========+====================================================+
| url        | Yes       | | A CentOS software repository file or RPM that    |
|            |           | | configures a software repository.                |
|            |           | | Supported URLs are http:// & https://            |
+------------+-----------+----------------------------------------------------+
| key_url    | No        | | URL of the matching repository signing key       |
+------------+-----------+----------------------------------------------------+

Example
-------

Add the JPackage repository from a repository definition and install the ``ecj``
package:

.. code-block:: none

  "environment" : {
    "os": "centos_7",
    "repos" : [
      {
        "url": "http://www.jpackage.org/jpackage50.repo"
      }
    ],
    "packages" : [
      "ecj"
    ]
  }


.. _rhel:

******************************
RHEL (Redhat Enterprise Linux)
******************************

The RHEL provisioner will normally use YUM, but will fall back to using RPM
directly for some operations on RHEL 5 and older.

The RHEL provisioner supports the following environment options.

Repositories
============

===== ====================================================
Name  Description
===== ====================================================
repos A list of additional software (package) repositories
===== ====================================================

The ``repos`` option defines a list of additional software repositories which
are configured on the build image. Each repository definition is defined with
the following options:

+------------+-----------+----------------------------------------------------+
| Name       | Required? | Description                                        |
+============+===========+====================================================+
| url        | Yes       | | A RHEL software repository file or RPM that      |
|            |           | | configures a software repository.                |
|            |           | | Supported URLs are http:// & https://            |
+------------+-----------+----------------------------------------------------+
| key_url    | No        | | URL of the matching repository signing key       |
+------------+-----------+----------------------------------------------------+

Example
-------

Add the JPackage repository from a repository definition and install the ``ecj``
package:

.. code-block:: none

  "environment" : {
    "os": "rhel_7",
    "repos" : [
      {
        "url": "http://www.jpackage.org/jpackage50.repo"
      }
    ],
    "packages" : [
      "ecj"
    ]
  }

