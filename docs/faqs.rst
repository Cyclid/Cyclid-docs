##########################
Frequently Asked Questions
##########################

Contents
========

* :ref:`cyclid-faqs`
* :ref:`github-faqs`
* :ref:`ci-faqs`

.. _cyclid-faqs:

Cyclid
======

What is Cyclid?
---------------

Cyclid is an Open Source Continuous Integration/Continuous Delivery (CI/CD)
system. There are three main components: the continuous integration server
(:ref:`Cyclid <cyclid_server>`), the user interface (:ref:`Cyclid UI <cyclid_ui_server>`)
and a command line client (:ref:`Cyclid Client <cyclid_client>`)

How can I try Cyclid?
---------------------

There have four options for running Cyclid:

* Let us run it for you: just `sign up for hosted Cyclid <https://account.cyclid.io>`_.
* `Run it on Docker <https://github.com/Cyclid/Docker>`_.
* `Install it yourself <http://docs.cyclid.io/en/latest/server.html#installing>`_
  on your own cloud instance(s) or servers.
* Test Cyclid with a `pre-built Vagrant virtual machine <http://docs.cyclid.io/en/latest/vagrant.html>`_.

What Open Source license(s) does Cyclid use?
--------------------------------------------

The vast majority of the code is released under the 
`Apache 2.0 license <https://www.apache.org/licenses/LICENSE-2.0>`_.

The Cyclid logo is licensed for use with Cyclid under the 
`Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License <http://creativecommons.org/licenses/by-nc-nd/4.0/>`_.

A small amount of third party code is licensed under the 
`MIT license <https://opensource.org/licenses/MIT>`_.

What language is Cyclid written in?
-----------------------------------

Cyclid, Cyclid UI & Cyclid Client are all written in Ruby, and Cyclid UI
includes client side Javascript. The Cyclid REST API uses JSON for data
interchange.

Cyclid job definitions can be written in either JSON or YAML.

What cloud providers/virtualization platforms/container platforms does Cyclid support?
--------------------------------------------------------------------------------------

By design Cyclid is entirely agnostic about which platforms it supports.
Almost all the functionality is provided by plugins, which means in theory
Cyclid can support any cloud provider, virtualization platform or container
platform you would like to use.

Cyclid itself currently supports `Google Cloud Platform <https://cloud.google.com/>`_,
`Docker <https://www.docker.com/>`_ containers and provides official plugins
for `Digitalocean <https://www.digitalocean.com/>`_ cloud and `LXD <https://linuxcontainers.org/lxd/>`_
based containers. Support for other platforms is planned, and you can of
course also write your own plugins to support specific cloud providers or
platforms.

Why did you build Cyclid?
-------------------------

We feel that `Continuous Integration is not a solved problem <http://cyclid.io/ci-isnt-solved-problem/>`_,
and we want `something that works for modern DevOps tools and workflows <http://cyclid.io/continuous-integration-rest-us/>`_,
not just traditional software development.


.. _github-faqs:

Github
======

Why does Cyclid need such broad access to my Github account?
------------------------------------------------------------

This is a `known issue with Github OAuth permissions <https://github.com/dear-github/dear-github/issues/113>`_.

Because Cyclid supports both Public & Private Github repositories, even for
Free users, it requires the appropriate permissions with Github for both.
Sadly the Github OAuth permissions are not particularly granular, especially
those for Private repositories, and so it may appear that Cyclid is asking for
overly broad access to your Github account.

Although Cyclid must request these permissions from Github, in reality the
only operation the Github plugin itself performs is to update your Pull
Request status.

.. _ci-faqs:

Continuous Integration
======================

What is Continuous Integration/Continuous Delivery?
---------------------------------------------------

At it’s most simple, Continuous Integration/Continuous Delivery (CI/CD) is a
method of automating your software build & release process. Cyclid can build,
test & deploy changes to your software every time a developer commits their
changes to source control.

The `Wikipedia article <https://en.wikipedia.org/wiki/Continuous_integration>`_
is a good place to find more information.

I’m an Operations/DevOps/Site Reliability/Network/Database Engineer, should I care about CI/CD?
-----------------------------------------------------------------------------------------------

Yes! Thanks to concepts such as `Infrastructure as Code <https://en.wikipedia.org/wiki/Infrastructure_as_Code>`_
you probably deal with software every day, and Cyclid can help you to automate
the process of integrating, testing & deploying your changes.
