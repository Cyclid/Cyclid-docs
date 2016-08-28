=============
Cyclid Server
=============

Installation
============

These instructions cover installing & configuring your own Cyclid
server. If you don't want to go through the trouble of doing that, you
might want to try out the `Cyclid Vagrant
instance <http://wp-lb.cyclid.io/document/vagrant/>`__ instead.

Prerequisites
-------------

#. A machine running Linux. Cyclid has been developed to run on Ubuntu
   14.04, but other distributions and versions should work just as well.
#. Ruby 2.x. Cyclid has been developed against Ruby 2.3 but any 2.x
   version should work. If you use Ubuntu you might want to to consider
   installing Ruby 2.3 from the Brightbox RubyNG PPA.
#. A SQL database. Cyclid uses ActiveRecord, so in theory any database
   that is supported by ActiveRecord should work. I use MySQL so these
   instructions reflect that. You'll need a new (empty) database and a
   user which has create & update permissions.

Dependencies
------------

Cyclid itself has some dependencies. Some it can install & configure
itself, some will require you to install & configure them.

The Cyclid Server has the following additional dependencies

-  `Sidekiq <http://sidekiq.org/>`__
-  `Redis server <http://redis.io/>`__
-  Mist

Sidekiq & Redis
~~~~~~~~~~~~~~~

Sidekiq is used to run jobs in the background, and in turn depends on
Redis to queue data. Cyclid does not use Redis directly.

Mist
~~~~

Mist is a simple container & virtual machine scheduler which is used to
create build hosts for use by Cyclid. Cyclid is a client, and you'll
need to install & configure the Mist server separately (although if you
like, you can run the Mist server on the same machine as Cyclid). Follow
the instructions for installing Mist.

Application server
~~~~~~~~~~~~~~~~~~

You'll probably want to run Cyclid behind a reverse proxy, through an
application server. These instructions cover installing & configuring
`Unicorn <https://unicorn.bogomips.org/>`__ and Nginx with Cyclid.

Installing on Ubuntu
====================

These instructions assume you are installing Cyclid on an Ubuntu 14.04
machine, with the Brightbox RubyNG PPA enabled, and that both the Cyclid
API server & Cyclid UI server will run alongside each other on the same
server. 1. Install dependencies Install the dependencies for installing
the Gems that Cyclid requires:

.. raw:: html

   <div class="code">

::

    $ sudo apt-get install ruby2.3 ruby2.3-dev build-essential cmake mysql-client libmysqlclient-dev redis-server memcached
    $ sudo mkdir -p /var/lib/cyclid /var/run/cyclid

.. raw:: html

   </div>

 2. Install Cyclid

.. raw:: html

   <div class="code">

::

    $ sudo gem install cyclid cyclid-ui

.. raw:: html

   </div>

Rubygems will install the Ruby dependencies for Cyclid automatically,
which may include building native extensions for some Gems. 3. Create
the Cyclid configuration file

.. raw:: html

   <div class="code">

::

    $ sudo mkdir /etc/cyclid
    $ sudo vim /etc/cyclid/config

.. raw:: html

   </div>

The configuration file specifies the database connection and a few
configuration options to use:

.. raw:: html

   <div class="code">

::

    server:
      database: mysql://<username>:<password>@<server>/<database>
      log: stderr
      dispatcher: local
      builder: mist
    manage:
      api:
        server: http://localhost:8361
        client: http://<hostname>:8361

.. raw:: html

   </div>

Replace the MySQL username, password & hostname with ones suitable for
your database server. The user should have create & update privileges.
The database should already exist on the server (I.e.
``create database <database>;`` should already have been run). Replace
the API server hostname with the name of the server, which should be
resolvable by the client. See the complete `documentation for the
configuration
file <http://wp-lb.cyclid.io/document/configuration-file/>`__ for more
information. 4. Create the Cyclid database

.. raw:: html

   <div class="warning">

**Warning!** ``cyclid-db-init`` will drop any existing data from your
database! Do NOT run ``cyclid-db-init`` if you have an existing
database, as you WILL lose your data.

.. raw:: html

   </div>

.. raw:: html

   <div class="code">

::

    $ cyclid-db-init

.. raw:: html

   </div>

The database schema will be populated and the initial Admin user &
organization will be created. The initialization process will create a
random password & HMAC secret for the Admin user and print them out at
the end E.g.

.. raw:: html

   <div class="code">

::

    Admin secret: fe150f3939ed0419f32f8079482380f5cc54885a381904c15d861e8dc5989286
    Admin password: 9u%Y5ySl

.. raw:: html

   </div>

Make a note of the secret & password as you will require them to log in
to Cyclid! 5. Configure Sidekiq Create a file that Sidekiq can use to
run Cyclid background jobs:

.. raw:: html

   <div class="code">

::

    $ echo "require 'cyclid/app'" | sudo tee /var/lib/cyclid/sidekiq.rb

.. raw:: html

   </div>

Now start Sidekiq:

.. raw:: html

   <div class="code">

::

    $ sudo sidekiq -e production -d -P /var/run/cyclid/sidekiq.pid -L /var/log/sidekiq.log -r /var/lib/cyclid/sidekiq.rb

.. raw:: html

   </div>
