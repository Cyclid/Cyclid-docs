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


sudo
====

===== =======================================================
Name  Description
===== =======================================================
sudo  Run the command using ``sudo``
===== =======================================================

Run the command using ``sudo`` (if the remote user is not already ``root``).

Example
-------

Run ``gem install`` to install a Gem system-wide, using ``sudo``:

.. code::

  {
    "action" : "command",
    "cmd" : "gem install bundler",
    "sudo" : true
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

sudo
====

===== =======================================================
Name  Description
===== =======================================================
sudo  Run the script using ``sudo``
===== =======================================================

Run the script using ``sudo`` (if the remote user is not already ``root``).

Example
-------

A simple script defined as a single string, running as ``root`` using ``sudo``:

.. code:: json

  {
    "action" : "script",
    "script" : "#!/bin/sh\necho 'hello from a simple script'\necho 'I am %{username} of %{organization}'",
    "sudo" : true
  }


.. _log-plugin:

***
Log
***

The Log plugin writes a message to the build log. It supports the following
options.

message
=======

======= ==============================================================
Name    Description
======= ==============================================================
message Log message
======= ==============================================================

Specify the log message.

Example
-------

.. code:: json

  {
    "action" : "log",
    "message" : "Hello from Cyclid"
  }

.. _email-plugin:

*****
Email
*****

The Email plugin send an email notification. It supports the following options.

message
=======

======= ==============================================================
Name    Description
======= ==============================================================
message Email message body
======= ==============================================================

Specify the email message body.

to
==

======= ==============================================================
Name    Description
======= ==============================================================
to      Email recipiant address
======= ==============================================================

The email address to send the message to.


Example
-------

.. code:: json

  {
    "action" : "email",
    "message" : "This is an email from Cyclid",
    "to" : "user@example.com"
  }

subject
=======

======= ==============================================================
Name    Description
======= ==============================================================
subject Email message subject
======= ==============================================================

An optional subject. If no subject is specified the default of
``Cyclid notification`` is used.

Example
-------

.. code:: json

  {
    "action" : "email",
    "subject" : "Example message",
    "message" : "This is an email from Cyclid",
    "to" : "user@example.com"
  }

color
=====

======= ==============================================================
Name    Description
======= ==============================================================
color   Email body highlight color
======= ==============================================================

Email messages sent by Cyclid highlight the message subject; you can use the
``color`` option to set this color for different classes of emails E.g. a
failure message could set the color to red.

Example
-------

.. code:: json

  {
    "action" : "email",
    "color" : "red",
    "message" : "This is an email from Cyclid",
    "to" : "user@example.com"
  }

Configuration
=============

The email plugin supports the following configuration options.

See the :ref:`configuration-file` documentation for more information on
configuring plugins.

+----------+-----------+------------------+-------------------------------+
| Name     | Required? | Default          | Description                   |
+==========+===========+==================+===============================+
| server   | No        | localhost        | The SMTP relay server.        |
+----------+-----------+------------------+-------------------------------+
| port     | No        | 587              | SMTP server port.             |
+----------+-----------+------------------+-------------------------------+
| from     | No        | cyclid@cyclid.io | "From" address of the sender. |
+----------+-----------+------------------+-------------------------------+
| username | No        |                  | SMTP server username.         |
+----------+-----------+------------------+-------------------------------+
| password | No        |                  | SMTP server password.         |
+----------+-----------+------------------+-------------------------------+

.. _slack-plugin:

*****
Slack
*****

The Slack plugin send a Slack message notification. It supports the following
options.

subject
=======

======= ==============================================================
Name    Description
======= ==============================================================
subject Slack message subject
======= ==============================================================

The subject of the Slack message.

message
=======

======= ==============================================================
Name    Description
======= ==============================================================
message Slack message body
======= ==============================================================

The message body text of the Slack message.

color
=====

======= ==============================================================
Name    Description
======= ==============================================================
color   Slack message highlight color
======= ==============================================================

You can use the ``color`` option to select the highlight color of the Slack
message E.g. a failure notification can set the color to ``danger``. If no
color is specified the default of ``good`` is used.

Example
-------

Send a failure notification to the default Slack channel, with the color set
to ``danger``:

.. code:: json

  {
    "action": "slack",
    "subject": "%{job_name} failed",
    "message": "Job %{organization}/%{job_name} (job #%{job_id}) failed.",
    "color": "danger"
  }

url
===

The Slack API URL. By default the Slack API URL is configured
organization-wide, and this URL will be used when no URL is specified. However
if you need to send a notification to a different Slack group, you can
over-ride the default with the ``url`` option.

Example
-------

.. code:: json

  {
    "action": "slack",
    "url" : "https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX",
    "subject": "%{job_name} succeeded",
    "message": "Job %{organization}/%{job_name} (job #%{job_id}) completed successfully.",
  }

.. _simplecov-plugin:

*********
Simplecov
*********

The Simplecov plugin reads a Simplecov test coverage report. It supports the
following options.

path
====

===== ====================================================
Name  Description
===== ====================================================
path  Path to the coverage report.
===== ====================================================

The ``path`` option gives a fully qualified path to the generated Simplecov
coverage report, in JSON format.

The ``covered_percent`` metric from the report is added to the Job context in
the ``simplecov_coverage`` variable, as a percentage to 2 decimal places E.g.
if the generated report contains ``{"covered_percent":86.9795918367347}`` then
this will be rounded to ``86.98%`` by the plugin.

Your project should install & configure the Simplecov-JSON Gem to generate a
JSON coverage report.

Example
-------

.. code:: json

  {
    "action" : "simplecov",
    "path" : "%{workspace}/project/coverage.json"
  }

.. _cobertura-plugin:

*********
Cobertura
*********

The Cobertura plugin reads a Cobertura compatable test coverage report. It
supports the following options.

path
====

===== ====================================================
Name  Description
===== ====================================================
path  Path to the coverage report.
===== ====================================================

The ``path`` option gives a fully qualified path to the generated coverage
report, in XML format.

The ``line-rate`` metric from the report is added to the Job context in
the ``cobertura_line_rate`` variable, and the ``branch-rate`` metric is added
as ``cobertura_branch_rate``. Both metrics are provided as a percentage to 2
decimal places E.g. if the generated report contains
``<coverage line-rate="0.9" branch-rate="0.75">`` then ``cobertura_line_rate``
will be ``90%`` and ``cobertura_branch_rate`` will be ``75%``

The Cobertura plugin can be used to read any Cobertura compatible coverage
report.

Example
-------

.. code:: json

  {
    "action" : "cobertura",
    "path" : "%{workspace}/project/coverage.xml"
  }
