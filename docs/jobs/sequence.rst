.. _sequence-info:

########
Sequence
########

The Sequence defines how Cyclid runs your job pipeline, by chaining together
different Stages and telling Cyclid how to handle the success or failure of
each Stage.

*******
Example
*******

Run the ``prepare`` Stage, followed by the ``test`` Stage, and finally the
``build`` Stage. If the ``build`` Stage succeeds, run the ``success`` Stage.
If any of the Stages fail, run the ``failure`` Stage.

.. highlight:: json
.. code:: json

  "sequence" : [
    {
      "stage" : "prepare",
      "on_failure" : "failure"
    },
    {
      "stage" : "test",
      "on_failure" : "failure"
    },
    {
      "stage": "build",
      "on_success": "success",
      "on_failure": "failure"
    }
  ]

stage
=====

====== ====================================================
Name   Description
====== ====================================================
stage  The name of a Stage to run
====== ====================================================

The name of a Stage to run. Each Step in the Stage will be run until either
one fails, or they all succeed.

The named Stage must either be defined in the job itself, or defined on the
server where the job is running.

on_success
==========

========== ====================================================
Name       Description
========== ====================================================
on_success The name of a Stage to run
========== ====================================================

The name of a Stage to run if the Stage succeeds. If no ``on_success`` Stage is
defined, Cyclid will run the next Stage in the Sequence; if no more Stages are
defined in the Sequence, the job will stop.

on_failure
==========

========== ====================================================
Name       Description
========== ====================================================
on_failure The name of a Stage to run
========== ====================================================

The name of a Stage to run if the Stage fails. If no ``on_failure`` Stage is
defined, Cyclid will run the next Stage in the Sequence; if no more Stages are
defined in the Sequence, the job will stop.
