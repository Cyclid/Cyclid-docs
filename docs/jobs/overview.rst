########
Overview
########

Jobs are defined in a single file. They can be written in either `JSON <http://www.json.org/>`_
or `YAML <http://yaml.org/>`_ format.

A Cyclid job defines:

1. Some information about the :ref:`Job <metainfo>` itself, including the namei
   & version.
2. The :ref:`Environment` to use to run the job. This includes the operating
   system type and version, and any extra packages to be installed.
3. The :ref:`Stages` to run, and the :ref:`Sequence` to run them in.
4. Any additional :ref:`Stages` that are specific to this job.
5. Additional information such as :ref:`Sources` to be checked out or encrypted
   :ref:`Secrets` that can be used when running the job.

Every time a job is run Cyclid provides a "context", which is information about
the running job. The :ref:`job context <context>` data can be used in the job
definition as variables.

Normally the job file is placed in the root of your project, with the name
``.cyclid.json`` for JSON formatted jobs, or ``.cyclid.yml`` (``cyclid.yaml``)
for YAML formatted jobs. If you use a webhook to automatically trigger Cyclid
jobs, it will look for one of these files in your project to run. If you don't
use webhooks, or want to define additional jobs that you'll run using the
:ref:`job-submit` command, you can use any name for the file: although it's easier
if you still use the ``.json`` or ``.yml`` file extensions.

********
Examples
********

Minimal job
===========

The absolutely most minimal job that could be run is:

.. highlight:: json
.. code:: json
  :name: Minimal job in JSON

  {
    "name" : "Minimal example",
    "sequence" : [
      {
        "stage" : "predefined"
      }      
    ]
  }

.. highlight:: yaml
.. code:: yaml
  :name: Minimal job in YAML

  ---
  name: Minimal example
  sequence:
  - stage: predefined

This job:

* Uses the default operating system (whatever is defined by the server).
* Does not install any additional packages.
* Defines no additional :ref:`Stages`.
* Defines no :ref:`Secrets` or :ref:`Sources` to use.
* Runs the single :ref:`Stages` ``predefined``, which must be created seperately
  on the server.

Representative job
==================

In reality your Cyclid jobs are more than likely going to look something more like the following:

.. highlight:: json
.. code:: json
  :name: Representative job in JSON

  {
    "name" : "Representative example",
    "environment" : {
      "os" : "ubuntu_14_04",
      "packages": [
        "build-essential",
        "cmake"
      ]
    },
    "secrets": {
      "github_token": "NST5WwAL7b0JFjO94C9q3mv5F3jus69dBr6o9gwGUG177kk7Y/5spP5P+um+VyhrPwJ44WCuwhr7wCR/UiuBZvoq89tsYXg7uEtCJ9uJP18WHHCW9iguTLyXUxDSfWihP2fPHvEka+8K8A2r1Z0FOvZjXSw4+E/COdUdWLQp4GQTHeRvZGV8FS/onoz5V/SYvozHkH6+tw+ZH0k4mkMKVGBl+VPH+RV4PbL9UfhY4/8ZMoaiSLiWg469a49W80qcimnfR3AP+v6vronoHg+d5mqWH+i0LpUeavMzoQnocQmD7axBs+lfVOKbKa2dPwPxLBdaxs2LPhL+ETwDMWCwaw=="
    },
    "sources": [
      {
        "type": "git",
        "url": "https://github.com/example/Project.git",
        "token": "%{github_token}"
      }
    ],
    "stages": [
      {
        "name": "prepare",
        "steps": [
          {
            "action": "command",
            "cmd": "make deps"
          }
        ]
      }
    ],
    "sequence" : [
      {
        "stage" : "prepare",
        "on_failure" : "failure",
        "on_success" : "build"
      },
      {
        "stage": "build",
        "on_failure": "failure",
        "on_success": "success"
      }
    ]
  }

.. highlight:: yaml
.. code:: yaml
  :name: Representative job in YAML

  ---
  name: Representative example
  environment:
    os: ubuntu_14_04
    packages:
    - build-essential
    - cmake
  secrets:
    github_token: NST5WwAL7b0JFjO94C9q3mv5F3jus69dBr6o9gwGUG177kk7Y/5spP5P+um+VyhrPwJ44WCuwhr7wCR/UiuBZvoq89tsYXg7uEtCJ9uJP18WHHCW9iguTLyXUxDSfWihP2fPHvEka+8K8A2r1Z0FOvZjXSw4+E/COdUdWLQp4GQTHeRvZGV8FS/onoz5V/SYvozHkH6+tw+ZH0k4mkMKVGBl+VPH+RV4PbL9UfhY4/8ZMoaiSLiWg469a49W80qcimnfR3AP+v6vronoHg+d5mqWH+i0LpUeavMzoQnocQmD7axBs+lfVOKbKa2dPwPxLBdaxs2LPhL+ETwDMWCwaw==
  sources:
  - type: git
    url: https://github.com/example/Project.git
    token: '%{github_token}'
  stages:
  - name: prepare
    steps:
    - action: command
      cmd: make deps
  sequence:
  - stage: prepare
    on_failure: failure
    on_success: build
  - stage: build
    on_failure: failure
    on_success: success

This job:

* Uses the Ubuntu 14.04 (Trusty) operating system.
* Installs the additional packages ``build-essential`` and ``cmake`` before the
  job is run.
* Defines the additional :ref:`Stage <stages>` ``prepare``
* Defines the :ref:`Source <sources>` from https://github.com/example/Project.git...
* ...along with the :ref:`Secret <secrets>` ``github_token``, the encrypted
  token to use when cloning the Source.
* Runs the :ref:`Stages` ``prepare`` followed by ``build``. If either Stage
  fails, the Stage ``failure`` will be run. When the ``build`` Stage succeeds,
  it will run the Stage ``success``. The ``failure`` and ``success`` Stages
  must be created seperately on the server.

****************
Example projects
****************

We provide complete example projects that show how to use Cyclid with
different languages and types of project. The come complete with a working
Cyclid job file that you submit to Cyclid. They will work with a
:ref:`Vagrant <vagrant>` instance if you just want to experiment.

* `Example Ruby project <https://github.com/Cyclid/example-ruby-project>`_

********
Sections
********

.. _metainfo:

Job information
===============

Every job must have a name, and can optionally have a version. If you don't
specify a version, Cyclid will use "1.0.0" as the default version.

Example
-------

.. code:: json

  {
    "name" : "An example job",
    "version" : "0.1.2",
    ...
  }

.. _stages:

Stages
======

Stages are the key piece of any Cyclid job. Jobs are composed of one or more
Stages, and each Stage defines one or more Steps which perform actions.

Stages can be defined in the job itself, or can be defined on the Cyclid
server, where they can be referenced from and used by any job.

See :ref:`Stages, Steps & Actions <stages-steps-actions-info>` to see how they
relate to each other and how to use them to build your Cyclid job.

.. _sequence:

Sequence
========

The Sequence defines your Cyclid job pipeline. It lists the Stages to run, and
the order to run them in. It can also tell Cyclid what to do when a Stage
succeeds, or fails.

See :ref:`Sequences <sequence-info>` for more information on how to define the
job pipeline in your Cyclid job.

.. _environment:

Environment
===========

The Environment specifies how the build image should be configured for the
running job. You can specify the operating system and version to use,
additional software repositories to configure and additional packages to
install before the job is run.

See :ref:`Environments <environment-info>` for details on configuring
the job environment.

.. _sources:

Sources
=======

Sources define any additional source code, software or tools that your job
requires that must be cloned or checked out into the build image before the job
is run.

When Cyclid builds a job from a webhook it can automatically determine the
location of the source code for the project, but if you use the
:ref:`job submit <job-submit>` command or your project requires other
additional projects, you'll need to specify them under Sources.

For example Cyclid itself can be cloned from https://github.com/Cyclid/Cyclid.
It depends on both the Cyclid-core & Mist-client Gems. As Cyclid is being built
from source, we also want to clone Cyclid-core & Mist from Github, too. So the
Sources for the Cyclid job are:

.. highlight:: json
.. code:: json

  "sources" :
  [
    {
      "type" : "git",
      "url" : "https://github.com/Cyclid/Cyclid-core.git"
    },
    {
      "type" : "git",
      "url" : "https://github.com/Cyclid/Mist.git"
    }
  ]

See :ref:`Sources <sources-info>` for details on configuring sources.

.. _secrets:

Secrets
=======

Sometimes you may need to store sensitive data, such as a password or token, in
a job file. Each organization has an RSA keypair which can be used to securely
encrypt data which can then be decrypted by the server when the job is run.

You can view the organizations public key with the
:ref:`organization show <org-show>` command, and you can encrypt data with the
:ref:`secret encrypt <secret-encrypt>` command. Once you have encrypted the
secret, you can add it to the Secrets definition in your job.

See :ref:`Secrets <secrets-info>` for details on creating and using secrets.

.. _context:

************
Job contexts
************

Every time a job is run, Cyclid provides a context which contains various
pieces of information that can be inserted into the job using variables, as
the job runs.

Some of the context information is generated automatically, but will also
include any decrypted :ref:`Secrets` that are defined by the job.

An example job context might look something like the following:

.. highlight:: ruby
.. code:: ruby

  {
    "job_id"=>309,
    "job_name"=>"Cyclid",
    "job_version"=>"1.0.0",
    "organization"=>"admins",
    "os"=>"ubuntu_trusty",
    "distro"=>"ubuntu",
    "release"=>"trusty",
    "repos"=>[
      {"url"=>"ppa:brightbox/ruby-ng"}
    ],
    "packages"=>[
      "ruby2.3",
      "ruby2.3-dev",
      "build-essential",
      "git",
      "zlib1g-dev",
      "libsqlite3-dev",
      "mysql-client",
      "libmysqlclient-dev"
    ],
    "server"=>"builder01",
    "name"=>"mist-cd2cc1f51e353aa6ddd36946689b679c",
    "host"=>"192.168.1.66",
    "username"=>"build",
    "password"=>nil,
    "key"=>"~/.ssh/id_rsa_build",
    "workspace"=>"/home/build"
  }

Some information may be more immediately useful to the running job than
others, and other information may be available depending on which plugins are
in use. The job context may also change as the job runs; hence the name!

Data from the job context can be inserted into the job as variables inside of
strings using the ``%{ }`` operator. For example ``"The build image is called
%{name} and it is running %{distro} %{release}"`` would produce the canonical
string ``"The build image is called mist-cd2cc1f51e353aa6ddd36946689b679c and
it is running ubuntu trusty"``

Context variables can be used pretty much anywhere where you would use a
string with Sources, Stages or Steps (it doesn't make sense to use them in
Environments or Secrets!)

See :ref:`Contexts <context-info>` for a complete list of the available job context variables.
Plugins may also add additional job context variables; you should check the
plugin documentation for information on the variables they might add.
