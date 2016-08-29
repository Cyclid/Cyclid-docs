.. _provisioners:

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


.. highlight:: json
.. code:: json

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


