.. _api-plugins:

###########
API Plugins
###########

API plugins can extend the core Cyclid API within their own namespace,
and perform arbitrary actions. This can be used to create an API which
can be used by external applications as a callback, or to extend the
core functionality of Cyclid itself.

API plugins themselves are easy to write, but the mechanism that makes
them work is more complex. The main aim is to abstract the framework
(Sinatra) and internal details (ActiveRecord models etc.) from the
plugin.

Controller
==========

The API plugin base provides a Controller which defines Sinatra API
endpoints for GET, POST, PUT & DELETE actions. The controller methods
provide the internal boilerplate, for example validating that the
organization exists, parsing the body (for POST & PUT methods) and
retrieving the plugin configuration data. The method then invokes a
callback in an external Module that implements the logic to handle the
HTTP event.

API plugins do not and *should* not create their own subclass of the
Controller. Instead, they should provide a Module which implements the
callback methods the plugin wishes to handle.

Methods
=======

There are four callback methods, which map directly onto the HTTP verbs
that the API plugin controller supports:

.. code:: ruby

    module Methods
      def get(headers, config)
        ....
      end

      def post(data, headers, config)
        ...
      end

      def put(data, headers, config)
        ...
      end

      def delete(headers, config)
        ...
      end
    end

The API plugin base provides a set of methods which simply return an
HTTP 405 error ("Not implemented") to the caller. A plugin can then
implement only the callbacks it needs:

.. code:: ruby

    def ExampleMethods
      include Api::Methods

      def post(data, headers, config)
        ...
      end
    end

Here, the Example plugin only wishes to handle HTTP POST events, so only
provides it's own implementation for the post callback. By including the
Api::Methods 'base' module, the default get, put & delete callbacks will
be used (which simply return a 405 error).

Helpers
=======

The API plugin base provides some helper methods. These mostly wrap (and
therefore abstract) internal methods. These helpers are:

authorize
---------

-  method : **Required** : Authenticate the user and check that they are
   authorized to perform the operation. *method* is one of 'get',
   'post', 'put' or 'delete', which map to a READ, WRITE or ADMIN
   permission.

By default API plugin endpoints are not authenticated & authorized in
the same way as the rest of the Cyclid API is. If a plugin wishes to
apply authorization to an endpoint (I.e. a user must exist in Cyclid and
be a member of the organization) it can call the authorize helper.

return\_failure
---------------

-  code : **Required** : HTTP return code.
-  message : **Required**\ \* : Message to be returned as part of the
   response body.

Causes the HTTP request to fail with the given HTTP response code. The
message body is a JSON object which contains a code and the message.

http\_headers
-------------

An internal method which extracts & formats the raw HTTP request
headers, which are then passed to the callback method (See below).

HTTP headers
============

The API Controller processes the raw HTTP request headers and extracts
any ``HTTP_`` header. The header is "cleaned up" and added to the list
of headers which is then passed to the callback method. For example,
given the following raw HTTP headers:

::

    "HTTP_CONTENT_TYPE" => "application/json"
    "HTTP_X_EXAMPLE_ACTION" => "event"

would become the list of headers:

::

    "Content-Type" => "application/json"
    "X-Example-Action" => "event"

callback methods can then use the headers, if required.

This, again, abstracts the plugin from the raw implementation details
and allows Cyclid to safely pass HTTP headers to a plugin without
leaking any potentially sensitive information E.g. session signing
secret.

Joining Methods to a Controller
===============================

The API plugin class itself provides a single method that simply returns
a new Api::Controller instance. It passes in the Module that implements
its callback methods, and the two are joined together when the
Controller instance is registered in Sinatra.

.. code:: ruby

    class Example < Api
      def self.controller
        return ApiExtension::Controller.new(ApiExtension::ExampleMethods)
      end
    end

Methods, not Classes
====================

You may have noticed that the use of Modules and that the bulk of the
plugin itself is actually implemented on a Module, rather than a
subclass of the Api plugin class, as most of the other plugins are
implemented.

This complication stems from the fact each Sinatra Controller is a
Module, which you then ``register`` into the Sinatra application.
Sinatra then calls a ``registered`` callback method on the Module,
passing it the Sinatra Application instance. The callback can then
define the API endpoints against the application instance.

Because these are plugins, that requires us to dynamically create a new
Module *instance* that can in turn be registered by Sinatra; the
Controller class is actually a subclass of Module, which allows the
plugin to create the Module on the fly. Due to the fact that it's quite
difficult to create a "submodule" which can overload methods (as you
would with a subclass which can overload a method), the Controller
itself & the Methods are split in two; this then allows the plugin
create it's own module in the normal manner, without having to resort to
complex Ruby metaprogramming.
