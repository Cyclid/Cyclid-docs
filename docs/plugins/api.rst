.. _api-plugin:

##############
API extensions
##############

.. contents::
   :depth: 2

.. _google:

******
Github
******

The Github plugin provides API endpoints which are used for Github event
callbacks.

The Github plugin implements the Github OAuth2 authentication process which
allows users to obtain an OAuth2 token for their organization which can in
turn be used to authenticate Cyclid with Github, including cloneing private
repositories.

Configuration
=============

The Github plugin supports the following configuration options.

See the :ref:`configuration-file` documentation for more information on
configuring plugins.

+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| Name                  | Required? | Default                          | Description                                           |
+=======================+===========+==================================+=======================================================+
| api_url               | Yes       |                                  | Public URL of the Cyclid API server.                  |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| ui_url                | Yes       |                                  | Public URL of the Cyclid UI server.                   |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| client_id             | Yes       |                                  | Your registered Github client ID.                     |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+

You must `register your Cyclid server <https://github.com/settings/applications/new>`_
with Github to obtain a unique client_id which is used to identify your Cyclid server
when it performs API calls to Github.

The API URL is used to construct linkback URLs which are passed to Github; in
turn Github will call API endpoints via. this URL.

When you register your application you must provide the "Authorization
callback URL"; this URL **must** match the value you provide in api_url. For
example, if your Cyclid server is accessable from the URL
http://cyclid.example.com:8361 you must set the "Authorization callback
URL" to ``http://cyclid.example.com:8361``

The UI URL is used to construct the "details" link to the Cyclid UI for each
job.
