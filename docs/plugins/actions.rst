.. _actions-plugin:

#######
Actions
#######

.. contents::
   :depth: 2

.. _command-plugin:

*******
Command
*******

The Command plugin runs a command on the build image. It supports the
following options.

cmd
===

===== ====================================================
Name  Description
===== ====================================================
cmd   Command string to run on the build image.
===== ====================================================

The ``cmd`` option specifies the command to run. This can either be a complete
command complete with arguments, or just the name of the name of the command
(where the arguments will be passed with the ``arguments`` option)

The command must be in $PATH, or you must specify the absolute path to the
command binary.

A return code of 0 is considered success; any other return code is considered
a failure.

Example
-------

Run the command ``bundle`` from the default working directory:

.. code:: json

  {
    "action" : "command",
    "cmd" : "bundle install --path vendor/bundle"
  }

args
====

===== ====================================================
Name  Description
===== ====================================================
args  Command arguments.
===== ====================================================

An optional list of individual arguments to pass to the command.

Example
-------

Run the command ``bundle`` from the default working directory:

.. code:: json

  {
    "action" : "command",
    "cmd" : "bundle",
    "args": [
       "install",
       "--path",
       "vendor/bundle"
    ]
  }

env
===

===== =======================================================
Name  Description
===== =======================================================
env   Environment variables to set before running the command
===== =======================================================

Defines any environment variables that must be set when the command is run.
Each environment variable should specify the name and the value to set.

Example
-------

Set the $GEM_HOME environment variable when the ``bundle install`` command is
run:

.. code:: json

  {
    "action" : "command",
    "cmd" : "bundle install --path vendor/bundle",
    "env" : [
      { "GEM_HOME" : "/var/lib/gems" }
    ]
  }

path
====

===== ==============================================================
Name  Description
===== ==============================================================
path  Path to a directory on the build image to run the command from
===== ==============================================================

Specify the path to the working directory on the build image where the command
should be run.

If no path is specified the default is to run the command in the root directory
of the workspace.

Example
-------

Run the ``bundle install`` command from the ``Project`` directory in the
workspace:

.. code::

  {
    "action" : "command",
    "cmd" : "bundle install --path vendor/bundle",
    "path" : "%{workspace}/Project"
  }

.. _script-plugin:

******
Script
******

The Script plugin defines a script and runs it on the build image. It supports
the following options.

script
======

====== ==============================================================
Name   Description
====== ==============================================================
script Define the script to run.
====== ==============================================================

Defines the script. Smaller scripts can be defined as a single string,
seperated by literal ``\n`` characters, but scripts can also be defined as an
array of lines.

Example
-------

A simple script defined as a single string:

.. code:: json

  {
    "action" : "script",
    "script" : "#!/bin/sh\necho 'hello from a simple script'\necho 'I am %{username} of %{organization}'"
  }

A simple script defined as an array:

.. code:: json

  {
    "action": "script",
    "script": [
      "#!/bin/bash",
      "echo 'Hello from a multi-line script'",
      "echo 'I am %{username} of %{organization}'"
    ]
  }

env
===

===== =======================================================
Name  Description
===== =======================================================
env   Environment variables to set before running the script
===== =======================================================

Defines any environment variables that must be set when the script is run.
Each environment variable should specify the name and the value to set.

Example
-------

Set the $EXAMPLE environment variable when the script is run:

.. code:: json

  {
    "action": "script",
    "env" : [
      { "EXAMPLE" : "Hello from a script" }
    ],
    "script": [
      "#!/bin/bash",
      "echo $EXAMPLE"
    ]
  }

path
====

===== ==============================================================
Name  Description
===== ==============================================================
path  Path to a directory on the build image to run the script from
===== ==============================================================

Specify the path to the working directory on the build image where the script
should be run.

If no path is specified the default is to run the script in the root directory
of the workspace.

Example
-------

Run the script from the ``Project`` directory in the workspace:

.. code::

  {
    "action": "script",
    "path" : "%{workspace}/Project"
    "script": [
      "#!/bin/bash",
      "echo 'Hello from a multi-line script'",
      "echo 'I am %{username} of %{organization}'"
    ]
  }

.. _email-plugin:

*****
Email
*****

The Email plugin send an email notification. It supports the following options.

.. _slack-plugin:

*****
Slack
*****

The Slack plugin send a Slack message notification. It supports the following
options.
