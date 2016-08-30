.. _stages-steps-actions:

#######################
Stages, Steps & Actions
#######################

Stages are the key piece of any Cyclid job. Jobs are composed of one or more
Stages, and each Stage defines one or more Steps which perform Actions. By
building Stages with Steps of Actions, and then connecting those Stages
together, you can create complex and powerful job pipelines.

.. _steps:

*****
Steps
*****

Steps are the simple building blocks of your pipeline. Each step is a single
atomic action that either succeeds or fails.

Each Step defines the type of Action to take, and the additional data to perform
that action. Some actions may run on the build image, directly affecting the
build process, or some may run on the server to perform an ancillary action
such as sending a notification email. Each Action is actually a plugin, so new
Actions can be defined by adding plugins.

Example
=======

.. highlight:: json
.. code:: json

  {
    "action": "command",
    "cmd": "make deps"
  }

action
======

====== ====================================================
Name   Description
====== ====================================================
action The action to take for the step
====== ====================================================

See the documentation for :ref:`Action plugins <actions>` for more information
on which Actions are available and what options are supported for each action.

.. _stages:

******
Stages
******

Stages collect together one or more Steps into a single unit. Each Step in a
Stage is run in the order that they are defined. If the Step succeeds, the
next Step in the Stage is run. If a Step fails, then execution of any further
Steps stops, and the Stage fails.

For example, given the following (but rather contrived) example:

.. code:: json

  {
    "name" : "always-fail",
    "steps" : [
      {
        "action" : "command",
        "cmd" : "echo 'At the begining'"
      },
      {
        "action" : "command",
        "cmd" : "/bin/false"
      },
      {
        "action" : "command",
        "cmd" : "echo 'We got to the end'"
      }
    ]
  }

The first Step will run successfully, echoing ``At the begining``. Because the
first Step succeeded, the next Step will run. This Step will always fail
(because ``/bin/false`` will always fail): when this happens, the Stage will
stop and the third Step will never run.

Example
=======

A simple Stage, with a single Step that runs a command:

.. code:: json

  {
    "name" : "prepare",
    "steps" : [
      {
        "action": "command",
        "cmd": "make deps"
      }
    ]
  }

A more complex Stage, with multiple Steps:

.. code:: json

  {
    "name" : "bundle-install",
    "steps" : [
      {
        "action" : "command",
        "cmd" : "sudo gem install bundler --no-ri --no-doc"
      },
      {
        "action" : "command",
        "cmd": "bundle install --path vendor/bundle",
        "path" : "%{workspace}/Example"
      }
    ]
  }

name
====

====== ====================================================
Name   Description
====== ====================================================
name   The name of the stage
====== ====================================================

Every Stage must have a name. This name should be unique. Names should not
contain spaces and should ideally be descriptive and human readable.

.. INFO::
  If you define a Stage in your job definition with the same name as a Stage
  that has been defined on the server, the Stage in your job will take
  precedence. You should NOT rely on this behaviour: it may change at any time
  in the future.

steps
=====

====== ====================================================
Name   Description
====== ====================================================
steps  A list of Steps to be run by this Stage
====== ====================================================

A Stage must define at least one Step to run. There is no limit on the number
of Steps you can define in a single Stage, but you should probably try to keep
the number low and split large lists of Steps up into multiple Stages with
fewer Steps in each Stage.

See the section on :ref:`Steps` for more information on how to define Steps.
