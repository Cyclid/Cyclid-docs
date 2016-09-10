.. _sources-plugin:

#######
Sources
#######

.. contents::
   :depth: 2

.. _git:

***
Git
***

The Git plugin can clone repositories from a remote Git server. It supports
the following options.

url
===

===== ====================================================
Name  Description
===== ====================================================
url   Location of a repository on remote Git server
===== ====================================================

The ``url`` option provides the location to a Git repository which should be
cloned into the build image before the job starts. Supported URLs are git://,
http:// and https://

Repositories are cloned into the working directory of the build image, with the
default repository name E.g. if you clone the Cyclid repository from
``https://github.com/Cyclid/Cyclid`` the source will be located in
``%{workspace}/Cyclid``

Example
-------

Clone the Cyclid repository into the working directory:

.. code:: json

  {
    "type" : "git",
    "url" : "https://github.com/Cyclid/Cyclid"
  }

branch
======

======= ====================================================
Name    Description
======= ====================================================
branch  Remote branch to clone
======= ====================================================

By default the Git plugin will clone the repository with the master branch
checked out. The ``branch`` option can be used to specify that a different
upstream branch should be checked out.

Example
-------

Clone the Cyclid repository into the working directory and switch to the
"example" branch:

.. code:: json

  {
    "type" : "git",
    "url" : "https://github.com/Cyclid/Cyclid",
    "branch" : "example"
  }

token
=====

====== ====================================================
Name   Description
====== ====================================================
token  Github API token
====== ====================================================

The ``token`` option can be used to supply an authentication token which is
used to clone a private Github repository.

.. WARNING::
  You should encrypt any such tokens and place them in the
  :ref:`Secrets` section of your job, and use the decrypted token from the job
  context variable, as shown.

Example
-------

Clone a private repository into the working directory using an API key that
has been provided as a secret named "github_token":

.. code:: json

  {
    "type" : "git",
    "url" : "https://github.com/Cyclid/Cyclid",
    "token" : "%{github_token}"
  }
