################
Cyclid UI Server
################

************
Installation
************

These instructions cover installing & configuring your own Cyclid UI
server. If you don't want to go through the trouble of doing that, you
might want to try out the :ref:`Cyclid Vagrant instance <vagrant>` instead.

Prerequisites
=============

#. A machine running Linux. Cyclid has been developed to run on Ubuntu
   14.04, but other distributions and versions should work just as well.
#. Ruby 2.x. Cyclid has been developed against Ruby 2.3 but any 2.x
   version should work. If you use Ubuntu you might want to to consider
   installing Ruby 2.3 from the Brightbox RubyNG PPA.

Dependencies
============

The server for the Cyclid user interface has an optional (but highly
recommended) dependency on `Memcached <https://memcached.org/>`__.

Memcached
---------

Memcached is an in-memory object cache. It is used by the Cyclid UI
server to cache user details. You can run the Cyclid UI server without
Memcached but this will result in more Cyclid API calls to retrieve user
data.

Application server
------------------

You'll probably want to run the Cyclid UI behind a reverse proxy,
through an application server. These instructions cover installing &
configuring `Unicorn <https://unicorn.bogomips.org/>`__ and Nginx with
the Cyclid UI.

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

    $ sudo apt-get install ruby2.3 ruby2.3-dev build-essential cmake memcached
    $ sudo mkdir -p /var/lib/cyclid /var/run/cyclid

Install Cyclid
--------------

::

    $ sudo gem install cyclid-ui

Rubygems will install the Ruby dependencies for Cyclid automatically,
which may include building native extensions for some Gems.

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

    $ mkdir -p /var/lib/cyclid-ui

Configure Unicorn for the Cyclid UI server
------------------------------------------

Create a Rack configuration file for Unicorn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    $ sudo vim /var/lib/cyclid-ui/config.ru

This is a standard Rack configuration file. The most basic configuration
for Cyclid is:

::

    require 'sinatra'
    require 'cyclid_ui/app'

    run Cyclid::UI::App

Configure Unicorn
^^^^^^^^^^^^^^^^^

Create the Unicorn configuration file:

::

    $ sudo vim /var/lib/cyclid-ui/unicorn.rb

    working_directory "/var/lib/cyclid-ui"
    pid "/var/run/unicorn.cyclid-ui.pid"

    stderr_path "/var/log/cyclid-ui/unicorn.cyclid-ui.log"
    stdout_path "/var/log/cyclid-ui/unicorn.cyclid-ui.log"

    listen "/var/run/unicorn.cyclid-ui.sock"

    worker_processes 4
    timeout 10

Start Unicorn
^^^^^^^^^^^^^

::

    $ sudo unicorn -D -E production -c /var/lib/cyclid-ui/unicorn.rb

Make the static assets available
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Cyclid UI server includes some static files (Images, Javascript, CSS
etc.) which Nginx must be able to find. The easiest way to do this is to
create a symbolic link from the Cyclid UI application directory to the
assets; Cyclid UI includes a command to help you find the location:

::

    $ sudo ln -s $(cyclid-ui-assets) /var/lib/cyclid-ui/public

Configure Nginx
---------------

You must configure Nginx to act as a reverse proxy to Unicorn.

Cyclid UI Nginx site configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    $ sudo vim /etc/nginx/sites-available/cyclid-ui

    upstream cyclid-ui {
      server unix:/var/run/unicorn.cyclid-ui.sock fail_timeout=0;
    }

    server {
      listen 80;

      server_name cyclid.example.com;
      root /var/lib/cyclid-ui;

      location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
        root /var/lib/cyclid-ui/public;
        expires max;
        add_header Cache-Control public;
        log_not_found off;
      }

      try_files $uri @cyclid-ui;

      location @cyclid-ui {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_pass http://cyclid-ui;
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
    $ sudo ln -s /etc/nginx/sites-available/cyclid-ui /etc/nginx/sites-enabled/cyclid-ui
    $ sudo service nginx restart

You should now be able to configure your client to connect to the
server, using the admin password & secret that were provided when you
created the database.

.. NOTE::
  You may want to run Unicorn under a process supervisor, rather than starting
  it directly as a daemon. We prefer `Runit <http://smarden.org/runit/>`__ for
  this but any process supervisor or init scheme should work.
