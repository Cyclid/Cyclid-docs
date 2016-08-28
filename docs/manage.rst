================
Cyclid UI Server
================

Installation
============

These instructions cover installing & configuring your own Cyclid UI
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

Dependencies
------------

The server for the Cyclid user interface has an optional (but highly
recommended) dependency on `Memcached <https://memcached.org/>`__.

Memcached
~~~~~~~~~

Memcached is an in-memory object cache. It is used by the Cyclid UI
server to cache user details. You can run the Cyclid UI server without
Memcached but this will result in more Cyclid API calls to retrieve user
data.

Application server
~~~~~~~~~~~~~~~~~~

You'll probably want to run the Cyclid UI behind a reverse proxy,
through an application server. These instructions cover installing &
configuring `Unicorn <https://unicorn.bogomips.org/>`__ and Nginx with
the Cyclid UI.
