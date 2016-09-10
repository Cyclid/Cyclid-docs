#######
Plugins
#######

************
Plugin types
************

The majority of the functionality in Cyclid is implemented in a series
of Plugins. There are currently 7 different types of Plugin:

-  Action
-  API
-  Builder
-  Dispatcher
-  Provisioner
-  Source
-  Transport

Action
======

Each action within a Step maps to an Action plugin. For example, the
Step:

.. code:: json

    {
      "action" : "command",
      "cmd" : "bundle install"
    }

defines an action that will use the "command" plugin to run a command on
the build host. A different Action plugin may send a notification
message to a chat system, for example.

API
===

API plugins can extend the Cyclid API to provide additional
functionality E.g. a callback endpoint for Github, or a chatbot
integration. :ref:`API plugins are some of the most complex
<api-plugins>` Cyclid plugins.

Builder
=======

Builders create build hosts that can be used to run jobs. A cloud
Builder may call an external cloud API to create a new virtual instance,
the details of which it can then pass to the Job runner to perform the
build. For example, there could an Amazon Web Services (AWS) Builder
which calls the AWS API, or an OpenStack Builder which calls an
OpenStack native API, or a Builder which creates LXC containers on
physical hardware.

Dispatcher
==========

Dispatchers queue jobs to be run, and provide a method for the job
runner to return information to Cyclid as it runs. The most simple
Dispatcher is the 'local' plugin, which creates an asynchronous process
which runs on the same host as the API.

Dispatcher plugins also create Notifiers :ref:`that are used by the Job
Runner to update the database <internal-jobs-jobrecords>` as the Job runs.

Provisioner
===========

Provisioners work alongside Builders to create & prepare build hosts. A
Provisioner plugin knows about an operating system or distribution, and
is called to perform any additional configuration required after the
Builder has created a build host. For example, the Ubuntu Provisioner
can add additional apt repositories and install additional packages on
an Ubuntu based host. A Redhat Provisioner would likewise be able to
install additional yum repositories and install RPM packages on a Redhat
based build host.

Source
======

Source plugins can checkout/clone sources from a Source Code Management
(SCM) system. For example, a 'git' plugin can clone a Git repository, an
'svn' plugin can clone a Subversion repository etc.

Transport
=========

Transports are used to connect to build hosts and run commands. The most
obvious example is an SSH transport, but E.g. a Windows build host may
require a WinRM transport plugin.
