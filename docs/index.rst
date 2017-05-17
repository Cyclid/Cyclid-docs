.. Cyclid documentation master file, created by
   sphinx-quickstart on Sat Aug 27 11:06:08 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to Cyclid's documentation!
==================================

This is the documentation for the `Cyclid CI <https://cyclid.io>`_ system. It
covers everything from installing & configuring Cyclid, to writing Cyclid jobs
and using the Cyclid command line client.

This `documentation <https://github.com/Cyclid/Cyclid-docs>`_ and
`Cyclid itself <https://github.com/Cyclid>`_ are Open Source; if you think this
documentation could be improved, we welcome contributions.

Contents
========

* :ref:`user-docs`
* :ref:`job-docs`
* :ref:`plugin-docs`
* :ref:`release-notes`
* :ref:`developer-docs`


.. _user-docs:

.. toctree::
   :maxdepth: 3
   :caption: User Documentation

   server
   manage
   configuration_file
   vagrant
   cli
   faqs


.. _job-docs:

.. toctree::
   :maxdepth: 3
   :caption: Writing Jobs

   jobs/overview
   jobs/stages
   jobs/sequence
   jobs/modifiers
   jobs/environments
   jobs/sources
   jobs/secrets


.. _plugin-docs:

.. toctree::
   :maxdepth: 3
   :caption: Plugins

   plugins/actions
   plugins/sources
   plugins/api
   plugins/builders
   plugins/provisioners


.. _release-notes:

.. toctree::
   :maxdepth: 2
   :caption: Release Notes

   release_notes/index

.. _developer-docs:

.. toctree::
   :maxdepth: 3
   :caption: Developer Documentation

   security/authentication
   internal/plugins
   internal/api_plugins
   internal/plugin_configs
   internal/jobs_jobrecords_logbuffers_notifiers_callbacks
   internal/stages_steps_actions_jobs
