#############
Cyclid Server
#############


* :ref:`installing`
* :ref:`upgrading`

.. _installing:

**********
Installing
**********

These instructions cover installing & configuring your own Cyclid
server. If you don't want to go through the trouble of doing that, you
might want to try out the :ref:`Cyclid Vagrant instance <vagrant>` instead.

Prerequisites
=============

#. A machine running Linux. Cyclid has been developed to run on Ubuntu
   14.04, but other distributions and versions should work just as well.
#. Ruby 2.x. Cyclid has been developed against Ruby 2.3 but any 2.x
   version should work. If you use Ubuntu you might want to to consider
   installing Ruby 2.3 from the `Brightbox RubyNG PPA <https://launchpad.net/~brightbox/+archive/ubuntu/ruby-ng>`__.
#. A SQL database. Cyclid uses ActiveRecord, so in theory any database
   that is supported by ActiveRecord should work. I use MySQL so these
   instructions reflect that. You'll need a new (empty) database and a
   user which has create & update permissions.
#. A cloud provider, or container platform, that is supported by Cyclid
   or an available plugin.

Dependencies
============

Cyclid itself has some dependencies. Some it can install & configure
itself, some will require you to install & configure them.

The Cyclid Server has the following additional dependencies

-  `Sidekiq <http://sidekiq.org/>`__
-  `Redis server <http://redis.io/>`__

Sidekiq is used to run jobs in the background, and in turn depends on
Redis to queue data. Cyclid does not use Redis directly.

Application server
------------------

You'll probably want to run Cyclid behind a reverse proxy, through an
application server. These instructions cover installing & configuring
`Unicorn <https://unicorn.bogomips.org/>`__ and Nginx with Cyclid.

Installing on Ubuntu
====================

These instructions assume you are installing Cyclid on an Ubuntu 14.04
machine, with the Brightbox RubyNG PPA enabled, and that both the Cyclid
API server & Cyclid UI server will run alongside each other on the same
server.

Install dependencies
--------------------

Install the dependencies for installing the Gems that Cyclid requires:

::

    $ sudo apt-get install ruby2.3 ruby2.3-dev build-essential cmake mysql-client libmysqlclient-dev redis-server
    $ sudo mkdir -p /var/lib/cyclid /var/run/cyclid

Install Cyclid
--------------

::

    $ sudo gem install cyclid

Rubygems will install the Ruby dependencies for Cyclid automatically,
which may include building native extensions for some Gems.

You will also need to install any dependencies for your preferred database.
For example, if you are using a MySQL database, we recommend the ``mysql2``
adaptor:

::

    $ sudo gem install mysql2

If you're not using a MySQL database you should consult the documentation for
the appropriate ActiveRecord adapter for information on which dependencies
you'll need to install.

Create the Cyclid configuration file
------------------------------------

::

    $ sudo mkdir /etc/cyclid
    $ sudo vim /etc/cyclid/config

The configuration file specifies the database connection and a few
configuration options to use:

::

    server:
      database: mysql2://<username>:<password>@<server>/<database>
      log: stderr
      dispatcher: local
      builder: <Your preferred Builder plugin>

Replace the MySQL username, password & hostname with ones suitable for
your database server. The user should have create & update privileges.
The database should already exist on the server (I.e.
``create database <database>;`` should already have been run).

See the complete :ref:`documentation for the configuration file
<configuration-file>` for more information.

Create the Cyclid database
--------------------------

.. WARNING::
  ``cyclid-db-init`` will drop any existing data from your
  database! Do NOT run ``cyclid-db-init`` if you have an existing
  database, as you WILL lose your data.

::

    $ cyclid-db-init

The database schema will be populated and the initial Admin user &
organization will be created. The initialization process will create a
random password & HMAC secret for the Admin user and print them out at
the end E.g.

::

    Admin secret: fe150f3939ed0419f32f8079482380f5cc54885a381904c15d861e8dc5989286
    Admin password: 9u%Y5ySl

Make a note of the secret & password as you will require them to log in
to Cyclid!

Configure Sidekiq
-----------------

Create a file that Sidekiq can use to run Cyclid background jobs:

::

    $ echo "require 'cyclid/app'" | sudo tee /var/lib/cyclid/sidekiq.rb

Now start Sidekiq:

::

    $ sudo sidekiq -e production -d -P /var/run/cyclid/sidekiq.pid -L /var/log/sidekiq.log -r /var/lib/cyclid/sidekiq.rb

Configuring Unicorn & Nginx
===========================

Cyclid is a Ruby Sinatra application and can be run under any Rack
application server. The following instructions cover configuring Cyclid
to run with the `Unicorn <https://unicorn.bogomips.org/>`__ application
server with Nginx as a reverse proxy.

Prerequisites
-------------

Install Nginx & Unicorn
^^^^^^^^^^^^^^^^^^^^^^^

::

    $ sudo apt-get install nginx
    $ sudo gem install unicorn

Create the application directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    $ sudo mkdir -p /var/lib/cyclid
    
Create the log directory
^^^^^^^^^^^^^^^^^^^^^^^^

::

    $ sudo mkdir /var/log/cyclid

Configure Unicorn for the Cyclid API server
-------------------------------------------

Create a Rack configuration file for Unicorn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    $ sudo vim /var/lib/cyclid/config.ru

This is a standard Rack configuration file. The most basic configuration
for Cyclid is:

::

    require 'sinatra'
    require 'cyclid/app'

    run Cyclid::API::App

Configure Unicorn
^^^^^^^^^^^^^^^^^

Create the Unicorn configuration file:

::

    $ sudo vim /var/lib/cyclid/unicorn.rb

    working_directory "/var/lib/cyclid"
    pid "/var/run/unicorn.cyclid-api.pid"

    stderr_path "/var/log/cyclid/unicorn.cyclid-api.log"
    stdout_path "/var/log/cyclid/unicorn.cyclid-api.log"

    listen "/var/run/unicorn.cyclid-api.sock"

    worker_processes 4
    timeout 10

Start Unicorn
^^^^^^^^^^^^^

::

    $ sudo unicorn -D -E production -c /var/lib/cyclid/unicorn.rb

Configure Nginx
---------------

You must configure Nginx to act as a reverse proxy to Unicorn.

Cyclid API Nginx configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    $ sudo vim /etc/nginx/sites-available/cyclid-api

    upstream cyclid-api {
      server unix:/var/run/unicorn.cyclid-api.sock fail_timeout=0;
    }

    server {
      listen 8361;
      server_name cyclid.example.com;
      root /var/lib/cyclid;

      try_files $uri @cyclid-api;

      location @cyclid-api {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_pass http://cyclid-api;
      }

      error_page 500 502 503 504 /500.html;
      client_max_body_size 4G;
      keepalive_timeout 10;
    }

Replace the ``server_name`` with something more suitable for your
installation. Restart Nginx Enable the Nginx configuration & restart
Nginx:

::

    $ sudo rm /etc/nginx/sites-enabled/default
    $ sudo ln -s /etc/nginx/sites-available/cyclid-api /etc/nginx/sites-enabled/cyclid-api
    $ sudo service nginx restart

You should now be able to configure your client to connect to the
server, using the admin password & secret that were provided when you
created the database.

.. NOTE::
  You may want to run Sidekiq & Unicorn under a process supervisor, rather than
  starting them directly as daemons. We prefer
  `Runit <http://smarden.org/runit/>`__ for this but any process
  supervisor or init scheme should work.


.. _upgrading:

*********
Upgrading
*********

These instructions cover upgrading an existing Cyclid server. You should
consult the release notes for the version you new installing for any
instructions which are specific to that version.

Upgrade Cyclid
==============

::

    $ sudo gem install cyclid

Rubygems will install the Ruby dependencies for Cyclid automatically,
which may include building native extensions for some Gems.

You should also install any newer dependencies for your preferred database.
For example, if you are using a MySQL database, we recommend the ``mysql2``
adaptor:

::

    $ sudo gem install mysql2

If you're not using a MySQL database you should consult the documentation for
the appropriate ActiveRecord adapter for information on which dependencies
you'll need to install.

You should upgrade any Cyclid plugins which you have installed E.g.

::

    $ sudo gem install cyclid-example-plugin

Migrate the Cyclid database
===========================

.. WARNING::
  You should back up any databses before running ``cyclid-db-migrate``!

::

    $ cyclid-db-migrate

Any required migrations will be applied to the Cyclid database schema.
You only need to run ``cyclid-db-migrate`` once per. database.

Restart Cyclid & Sidekiq
========================

Restart both the Cyclid & Sidekiq processes.
