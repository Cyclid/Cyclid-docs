.. _configuration-file:

##################
Configuration file
##################

Cyclid itself is very simple to configure: there's just a single
configuration file, ``/etc/cyclid/config``. The configuration file is in
YAML format and contains a few simple configuration options. The Cyclid
API server & Cyclid UI server use the same configuration file, with one
section for each server:

::

    server:
      database: "mysql://dbuser:dbpass@localhost/cyclid"
      log: /var/log/cyclid.log
      dispatcher: local
      builder: mist
    manage:
      log: /var/log/cyclid-ui.log
      memcached: localhost:11211
      api: http://localhost:8361

*************************
Cyclid API server options
*************************

+------------------+-----------------------------------------------------------------------------------------------------------------------------------------+
| **database**     | Provides the database connection details.                                                                                               |
+------------------+-----------------------------------------------------------------------------------------------------------------------------------------+
| **log**          | The path to the log file.                                                                                                               |
+------------------+-----------------------------------------------------------------------------------------------------------------------------------------+
| **dispatcher**   | Selects the Dispatcher plugin you want to use. This is almost always ``local``.                                                         |
+------------------+-----------------------------------------------------------------------------------------------------------------------------------------+
| **builder**      | Selects the Builder plugin you want to use. In this example we're using the ``mist`` plugin, which manages LXC containers for Cyclid.   |
+------------------+-----------------------------------------------------------------------------------------------------------------------------------------+

The database connection details in the Cyclid configuration file can be
any valid `ActiveRecord
connection <http://guides.rubyonrails.org/configuring.html#configuring-a-database>`__,
and can either be a URL or a list of individual connection details. For
example, if you wanted to connect to a local MySQL server via. a UNIX
socket, you could configure Cyclid with

::

    database:
      adapter: mysql
      host: localhost
      socket: /var/run/mysql.sock
      username: cyclid
      database: cyclid

************************
Cyclid UI server options
************************

+-----------------+-----------------------------------------------+
| **log**         | The path to the log file.                     |
+-----------------+-----------------------------------------------+
| **memcached**   | The Memcached connection details.             |
+-----------------+-----------------------------------------------+
| **api**         | Provides the Cyclid API connection details.   |
+-----------------+-----------------------------------------------+

The API connection details are used both the Cyclid UI server and the
client (web browser) to connect to the API server. Sometimes, the
address that the server should use and the address that the client
should use are different E.g. an internal and external address. If
required, you can specify two different URLs:

::

    api:
      server: http://internal.example.com:8361
      client: http://external.example.com:8361
