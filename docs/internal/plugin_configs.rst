#####################
Plugin configurations
#####################

Plugin configs provide a mechanism for Plugins to create & store their
own configuration data without having to extend the core database
schema, or have any knowledge at all of the underlying schema. Plugin
configuration is specific to each organization, and each organization
can have its own configuration for any given plugin.

The Plugin configuration also provides an API that can be used to get &
set the configuration for each plugin.

API
===

The plugin config API provides two endpoints: one to GET the plugin
configuration, and one to PUT the plugin configuration. For example:

::

    $ curl -u admin http://api.dev.cyclid.io/organizations/test/configs/api/github
    {"id":1,"plugin":"github","version":"1.0.0","config":{"repository_tokens":[{"url":"http://github.com/example/Project","token":"abcdefg"},{"url":"http://github.com/example/Other","token":"uvwxyz"}],"hmac_secret":null},"organization_id":2,"schema":[{"name":"repository_tokens","type":"hash-list","description":"Repository OAuth tokens","default":[]},{"name":"hmac_secret","type":"string","description":"Github HMAC signing secret","default":null}]}

The data includes some metadata about the plugin, the configuration data
itself and a schema.

If no config has yet been created for the plugin when an API call is
made, a default configuration will be created and stored in the
database.

Configuration data schema
-------------------------

Because each plugin has its own configuration data, the data is stored
as JSON in a single database TEXT column; this means the configuration
data itself is effectively schemaless. In order to allow clients to
understand the configuration data presented by the plugin, the plugin
configuration also includes a schema which describes it. This data can
be used by the client E.g. a web UI can use the schema data to generate
a form that the user can modify.

.. code:: json

    "schema" : [
       {
          "type" : "hash-list",
          "default" : [],
          "description" : "Repository OAuth tokens",
          "name" : "repository_tokens"
       },
       {
          "type" : "string",
          "default" : null,
          "name" : "hmac_secret",
          "description" : "Github HMAC signing secret"
       }
    ]

Each item in the schema data describes an entry in the configuration
data:

-  name : **Required** : The JSON key name.
-  type : **Required** : The data type. This is not a direct mapping to
   the JSON data type (see below).
-  description : **Required** : A human readable description of the
   configuration data.

Currently defined data types are:

-  string : A single or multi-line string.
-  integer : A whole number.
-  boolean : A ``true`` or ``false`` value.
-  list : A simple list (array) of items.
-  hash-list : A list (array) of objects.

Other types may be added over time.

Implementation
==============

The plugin Base class implements the following methods:

-  get\_config
-  set\_config
-  update\_config
-  default\_config
-  config\_schema

get\_config
-----------

-  org : **Required** : The organization to retrieve the configuration
   for.

Retrieves the plugin config for the given organization from the
database. If no configuration exists yet, creates a new entry in the
database. The new database entry contains the data returned by
default\_config.

set\_config
-----------

-  new\_config : **Required** : Updated configuration data.
-  org : **Required** : The organization to store the configuration for.

Merges the new configuration data with the current configuration and
stores it in the database. The configuration is merged by calling
update\_config with a copy of the current and new configurations.

update\_config
--------------

-  current : **Required** : A copy of the current plugin configuration
   data.
-  new : **Required** : The new configuration to be applied.

Validates the new configuration data and merges it with the current
data. The method can implement logic for each configuration item; I.e a
single item which can only have a single entry will be over-written, but
a list may have new entries appended to it.

The merged configuration data is returned to set\_config to be stored in
the database.

default\_config
---------------

Returns a sensible default configuration, as a hash. The absolute
minimum default is an empty hash.

config\_schema
--------------

Returns the schema definition for the configuration (See above), as a
hash.

Implementing configuration in a plugin
======================================

Every plugin has an API endpoint for configuration data. If the plugin
does not implement any callbacks, the plugin will simply have an empty
configuration and any changes will be ignored.

A plugin which wishes to implement a configuration must provide
implementations of the ``update_config``, ``default_config`` and
``config_schema`` methods.

.. code:: ruby

    class Example < Api
      class << self
        def update_config(current, new)
          current.merge(new)
        end

        def default_config
          {wax: true}
        end

        def config_schema
          schema = []
          schema << { name: 'wax',
                      type: 'boolean',
                      default: true,
                      description: 'Whether to apply wax (true) or buff the wax off (false)' }
          schema
        end
      end
    end
