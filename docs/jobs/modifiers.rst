.. _modifiers-info:

#########
Modifiers
#########

********
Overview
********

The ``only_if`` and ``not_if`` modifiers can be used to decide if a Stage
should be run as part of your Sequence. The modifers offer a complete range of
operators that you can use for comparisons, and you can compare strings,
integers, floating point numbers and even percentages

If the result of the comparision is ``true`` (``only_if``) or ``false``
(``not_if``) the Stage will be run as part of the Sequence. If the Stage is
not run it is skipped, the stage is considered successful, and the next Stage
or the Stage defined in the ``on_success`` handler is run.

Example
=======

Run the ``prepare`` Stage, followed by the ``test`` Stage. Only run run the
``build`` Stage if the Job context variable ``github_branch`` is ``master``:

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
      "only_if" : "%{github_branch} === 'master'"
      "on_success": "success",
      "on_failure": "failure"
    }
  ]

**********
Statements
**********

only_if
=======

``only_if`` will cause a Stage to be run if the comparision evaluates to
``true`` E.g. ``1 eq 1``, ``'a' != 'b'``.

not_if
======

``not_if`` will cause a Stage to be run if the comparision evaluates to
``false`` E.g. ``8 eq 9``, ``'x' != 'y'``.


*********
Operators
*********

+----------+---------------------------------------------------+
| Operator | Description                                       |
+----------+---------------------------------------------------+
| ==       | Case insensitive string equals                    |
+----------+---------------------------------------------------+
| ===, eq  | Case sensitive string equals, number equals       |
+----------+---------------------------------------------------+
| !=, ne   | Not equal                                         |
+----------+---------------------------------------------------+
| <, lt    | Less than                                         |
+----------+---------------------------------------------------+
| >, gt    | Greater than                                      |
+----------+---------------------------------------------------+
| <=, le   | Less than or equal to                             |
+----------+---------------------------------------------------+
| >=, ge   | Greater than or equal to                          |
+----------+---------------------------------------------------+

Strings must be enclosed within single quotes (``'``).

You can compare strings, integers, floating point numbers and even
percentages, so all of the following are valid comparisons:

.. code::

  'this' != 'that'
  1 lt 2
  99% > 80%
  3.14 gt 2.71

Job context variables can be used in statements:

.. code::

  '%{github_branch}' == 'master'
  '%{release}' != 'trusty'

