#############################
Stages, Steps, Actions & Jobs
#############################

The user's view
===============

The user's viewpoint is quite simple. A job is defined with either JSON
or YAML:

.. code:: yaml

  ---
  name: Test project
  version: 1.0.0
  environment:
  - os: Ubuntu 14.04
    repos:
    - ppa:brightbox/ruby-ng
    packages:
    - build-essential
    - cmake
    - ruby2.2
    - ruby2.2-dev
  sources:
  - type: git
    url: https://github.com/example/Project.git
  stages:
  - name: 'Custom stage #1'
    steps:
    - action: command
      cmd: bundle install -p vendor/bundle
  - name: failure notification
    steps:
    - action: slack_notifier
      message: It's all gone Pete Tong
    - action: command
      cmd: push_notification.pl -p '1234' com.example.derp.tong
      path: /usr/bin
      env:
      - a_variable: 'thing'
        an_other_var: 'other thing'
  - name: success notification
    steps:
    - action: slack_notifier
      message: Yay, it worked!
  job:
  - stage: 'Custom stage #1'
    version: 1.0.0
    on_failure:
    - stage: failure notification
    on_success:
    - stage: foo
      version: 1.1
    # The 'foo' stage would be a pre-defined job and we can just over-ride attributes
    # like on_success
  - stage: foo
    version: 1.1
    on_success:
    - stage: success notification

The Job has our main parts to it:

1. An Environment, which defines how the host which is used to run the job is configured.
2. The Sources, which will be checked out onto the build host.
3. A series of Stages, which in turn have Steps, which define a list of Actions to be run.
4. The Job itself, which is a series of Stages to be run and what to do next when each stage succeeds or fails.

Stages can either be previously defined Stages from the database, or
defined "ad-hoc" in the Job. "Ad-hoc" stages and are not stored in the
database. We'll skip Environments for now.

The internal view
=================

The internal relationship is more complex.

*database diagram*

Organizations have one or more Stages, Stages have one or more Steps and
each Step defines one or more Actions. Jobs are more complicated: Jobs
are composable objects I.e. the Stages used in a Job may either be
defined in the database already, or defined ad-hoc in the Job itself. In
addition, Jobs can over-ride the on\_success & on\_failure handlers of
pre-defined jobs. Jobs may or may not be run local (I.e. on the same
host) to the API application. Jobs are asynchronous I.e. the API
application controller will return once a job is successfully *created*
and leave it to run in the background, and the client must poll the API
for the current job status.

The process for creating a Job, and all of it's moving parts, is:

1. A JSON or YAML document is submitted to the API. The API parses this
   into a Hash, creates a new JobView and passes it the Hash
   representing the Job definition.
2. For each ad-hoc Stage defined in the Job, the JobView creates a
   StageView object which is added to a list.
3. For each Action defined in the ad-hoc Stage, an Action object is
   created. The Action object is serialised into the StageView.
4. For each Stage defined in the job section:

  a. Check if the matching StageView has already been created from an
     ad-hoc job.
  b. If the Stage is not ad-hoc, find the matching Stage in the database
     and create a StageView from it.

5. Once the list of Stages is complete, serialise the StageViews.
6. The API controller serializes the JobView (The serialised StageViews,
   plus the Environment, plus some additional data about the Job) and
   passes it to the Dispatcher.
7. The Dispatcher dispatches the Job; what this means depends on the
   Dispatcher E.g. create a SideKiq worker to run the Job, or placing it
   on a queue to be consumed by a remote worker etc.
8. Create a JobRecord which records details about the job E.g. current
   status, when it was started, the user that requested it etc.
9. The Dispatcher returns the Job ID to the API controller, which in
   turn returns it to the client.

From this point on the job is now asynchronous and is processed in the
background. The client can poll the API to find out the latest job
status, retrieve the log data etc. We'll assume the job is running
locally under SideKiq:

1. Request a BuildHost from the Builder, passing in the Environment that
   was defined as part of the Job.
2. Create a Transport which can be used to communicate with the
   BuildHost.
3. Prepare the BuildHost (calling prepare() will do things like install
   the list of packages etc. required for the host, as defined by the
   Job)
4. Create a LogBuffer. This will store the combined log output from the
   job run.
5. Un-serialize each StageView in the Job.
6. For each StageView, un-serialize each Action: this yields an Action
   plugin object of some form which can be performed. Each plugin is
   passed the Transport & LogBuffer objects; plugins may or may not use
   either E.g. the 'command' plugin will use the Transport to execute a
   command on the BuildHost, and the output will be logged to the
   LogBuffer, but a notification plugin will run locally and may not log
   anything into the LogBuffer.
7. Once the Stages have all run (either successfully or otherwise), the
   worker is finished.

The worker will periodically update the JobRecord for the Job to
indicate the current state. The client may (or may not be) requesting
the latest job status.

The process for a remote job worker is not too different:

1. The Job is composed & serialized as above.
2. The Dispatcher creates a local worker & returns the Job ID to the API
   controller.
3. The local worker places the serialized job onto a queue and then
   waits for a message to tell it a remote worker has pulled it from the
   queue.
4. The remote worker sends messages via. the queue to the local worker,
   which updates the JobRecord status & appends log data into a
   LogBuffer.
5. The API client can poll the API for the Job status as before.

The local worker spends most of its time waiting for messages to arrive
and thus has much lower overhead. In addition the remote workers can be
mapped to API application *n:m* E.g. there can be more workers than
there are API applications.
