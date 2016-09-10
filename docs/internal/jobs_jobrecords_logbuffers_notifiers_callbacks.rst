.. _internal-jobs-jobrecords:

###################################################
Jobs, JobRecords, LogBuffers, Notifiers & Callbacks
###################################################

Internal classes
================

Jobs
----

Jobs are passed in as JSON or YAML documents and describe various items
which are needed to successfully run a job; they do not exist, as is, in
the database.

Jobs are run by a job Runner, which contains all of the logic necessary
to run jobs consistently. The actual job Runner instance may be local
(within the context of the API application) or remote (running as a
standalone process on a different host).

JobRecords
----------

JobRecords are just what they sound like: a record in the database of a
job that was submitted, and the metadata related to that job:

-  started : The time & date of when the job was submitted.
-  ended : The time & date of when the job finished, successfully or
   otherwise.
-  status : The *current* status of the job; for jobs that have
   finished, this is either succeeded or failed. Jobs that are in
   progress may have other states, such as waiting.
-  log : The log output from the job.

Every time a job is submitted, a new JobRecord is created.

LogBuffers
----------

During the process of running a job, data will be generated from various
different sources. A LogBuffer collates all of that data into a single
place, and also reflect data to various other sources. For example,
every time new log output it written to a LogBuffer, the LogBuffer also
updates the log in the JobRecord.

Notifiers
---------

Job runners may run remotely, in which case they may have no access to
the database and the related ActiveRecord models. Because of this a job
Runner may not be able to simply write directly to a LogBuffer or update
the status of the JobRecord.

Notifiers abstract the JobRecord & LogBuffer objects from the Runner;
the Notifier knows how to do this on behalf of the runner. In the case
of a local job Runner, the Notifier can simply update the JobRecord &
LogBuffer itself directly, but with a remote job Runner the Notifier may
instead place a message onto a queue, which would be consumed by a
background process running in the API application context which would
then update the JobRecord & LogBuffer.

Callbacks
---------

Because Jobs run asynchronously, it can be useful to connect arbitrary
code to Notifier events which can perform some self-defined action. For
example, during a Job run, the Github plugin can update the Pull Request
status via. the Github API.

Plugins can create their own Callback instance, which contains hooks
which will be called by the Notifier when various events (status
changes, something is written to the JobRecord log, completion) occur.
The Callback instance can then run whatever arbitrary code it wishes.

Relationship
============

The only object which knows if a Runner is local or remote is the
Dispatcher. The Dispatcher plugin also defines a suitable Notifier (and
Worker, if required). The process that creates the Runner will also
create a matching Notifier, ensuring that the Runner can always
communicate & update the database via. a suitable Notifier. Callbacks,
if used, are created externally and passed to the Dispatcher.
