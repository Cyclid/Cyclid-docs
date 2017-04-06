.. _builders-plugin:

########
Builders
########

.. contents::
   :depth: 2

.. _google:

******
Google
******

The Google Builder plugin creates creates Google Compute Engine (GCE) virtual
machines build hosts using Google Cloud.

Configuration
=============

The Google builder supports the following plugin configuration options.

See the :ref:`configuration-file` documentation for more information on
configuring plugins.

+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| Name                  | Required? | Default                          | Description                                           |
+=======================+===========+==================================+=======================================================+
| project               | Yes       |                                  | The Google Cloud project name.                        |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| client_email          | Yes       |                                  | The Google Cloud service account email address.       |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| json_key_location     | Yes       |                                  | Path to the JSON key file for the service account.    |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| zone                  | No        | us-central-1a                    | Google Compute zone to create instances in.           |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| machine_type          | No        | f1-micro                         | Instance machine type.                                |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| network               | No        | default                          | Network name.                                         |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| username              | No        | build                            | Username to bootstrap and run builds with.            |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| ssh_private_key       | No        | ``/etc/cyclid/id_rsa_build``     | Path to the build users private key.                  |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| ssh_public_key        | No        | ``/etc/cyclid/id_rsa_build.pub`` | Path to the build users public key.                   |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| instance_name         | No        | cyclid-build                     | Cyclid build host name prefix.                        |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+

The client_email & key file found at json_key_location are used to authenticate
with Google for your specified project. See https://cloud.google.com/storage/docs/authentication#service_accounts
for more information on how to create your service account & associated JSON
key file.

SSH keys
--------

By default the keypair `/etc/cyclid/id_rsa_build` (private) and `/etc/cyclid/id_rsa_build.pub`
(public) will be used to log into instances. You can set paths to a different
keypair using the ssh_private_key and ssh_public_key options.

Example
-------

Create f1-micro sized instances in the 'cyclid' project in the europe-west1-b
region:

.. code:: yaml

  server:
    ...
    builder: google
    ...
    plugins:
      google:
        project: cyclid
        client_email: 123456789099-compute@developer.gserviceaccount.com
        json_key_location: /var/lib/google/gcp.json
        zone: europe-west1-b


.. _docker:

******
Docker
******

The Docker Builder plugin creates creates container build hosts using Docker.

Configuration
=============

The Docker builder supports the following plugin configuration options.

See the :ref:`configuration-file` documentation for more information on
configuring plugins.

+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| Name                  | Required? | Default                          | Description                                           |
+=======================+===========+==================================+=======================================================+
| api                   | No        | unix:///var/run/docker.sock      | Your Docker daemon socket.                            |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+
| instance_name         | No        | cyclid-build                     | Cyclid build host name prefix.                        |
+-----------------------+-----------+----------------------------------+-------------------------------------------------------+

Example
-------

Create Docker instances on a remote server:

.. code:: yaml

  server:
    ...
    builder: docker
    ...
    plugins:
      docker:
        api: tcp://example.com:5422 

