****
Jobs
****

-  name : **Required** : Name of the job.
-  version : *Optional* : If no version is given, version will be 1.0.0.
-  environment : **Required** : Defines the build host environment (OS,
   additional packages etc.).
-  secrets : *Optional* : A list of encrypted secrets to add to the job
   context.
-  sources : *Optional* : A list of sources to check out onto the build
   host.
-  stages : *Optional* : A list of ad-hoc stages (Stages which are not
   predefined in the database).
-  sequence : **Required** : A list of actions to perform as part of the
   stage, listed in execution order.

Examples
========

.. code:: json

    {
       "name" : "example",
       "environment" : {
          "os" : "ubuntu_14_04"
       },
       "sequence" : [
          {
             "stage" : "example",
             "on_failure" : "failure",
             "on_success" : "success"
          }
       ]
    }

.. code:: json

    {
       "name" : "larger example",
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

.. code:: yaml

    ---
      name: "larger example"
      environment: 
        os: "ubuntu_14_04"
        packages: 
          - "build-essential"
          - "cmake"
      secrets:
        github_token: "NST5WwAL7b0JFjO94C9q3mv5F3jus69dBr6o9gwGUG177kk7Y/5spP5P+um+VyhrPwJ44WCuwhr7wCR/UiuBZvoq89tsYXg7uEtCJ9uJP18WHHCW9iguTLyXUxDSfWihP2fPHvEka+8K8A2r1Z0FOvZjXSw4+E/COdUdWLQp4GQTHeRvZGV8FS/onoz5V/SYvozHkH6+tw+ZH0k4mkMKVGBl+VPH+RV4PbL9UfhY4/8ZMoaiSLiWg469a49W80qcimnfR3AP+v6vronoHg+d5mqWH+i0LpUeavMzoQnocQmD7axBs+lfVOKbKa2dPwPxLBdaxs2LPhL+ETwDMWCwaw=="
      sources:
        -
          type: git
          url: "https://github.com/example/Project.git"
          token: "%{github_token}"
      stages: 
        - 
          name: "prepare"
          steps: 
            - 
              action: "command"
              cmd: "make deps"
      sequence: 
        - 
          stage: "prepare"
          on_failure: "failure"
          on_success: "build"
        - 
          stage: "build"
          on_failure: "failure"
          on_success: "success"

Secrets
=======

-  key : **Required** : The name of the secret. The secret will be
   decoded and added to the job context using the key as its name.
-  value: **Required** : The encrypted secret. The secret must be
   encrypted with the organization's public RSA key.

You can define as many secret key:value pairs as you require, provided
the key is unique.

Sources
=======

-  type : **Required** : The SCM system to use to obtain the source.

Different SCM's may have different attributes.

Git
---

-  url : **Required** : Git URL to the remote repository. May be either
   an https:// or git:// URL.
-  token : *Optional* : OAuth token to use for authentication with
   private Github repositories.
-  branch : *Optional* : The remote branch to check out.

Environment
===========

-  os : **Required** : The base operating system name & version to use
   for the build host.

Different OS's may have different attributes.

Ubuntu & Debian
---------------

-  repos : *Optional* : Additional apt repositories or PPAs to add to
   the build host prior to installing any packages.
-  packages : *Optional* : A list of additional packages to be installed
   on the build host prior to starting the build.

******
Stages
******

-  name : **Required** : Must be unique across the organization.
-  version : *Optional* : If no version is given, version will be 1.0.0.
-  steps : **Required** : A list of actions to perform as part of the
   stage, listed in execution order.

Examples
========

.. code:: json

    {
     "name" : "example",
     "steps" : [
         {
           "action" : "command",
           "path" : "/var/log",
           "cmd" : "ls -l"
         }
       ]
    }

.. code:: json

    {
     "name" : "success_notifier",
     "version" : "1.2.3",
     "steps" : [
         {
           "action" : "slack_notification",
           "message" : "Everything is fine"
         },
         {
           "action" : "sensu_alert",
           "status" : 0
         }
       ]
    }

.. code:: yaml

    ---
      name: "success_notifier"
      version: "1.2.3"
      steps: 
        - 
          action: "slack_notification"
          message: "Everything is fine"
        - 
          action: "sensu_alert"
          status: 0

Step
====

-  action : **Required** : Name of the action to run. The name must map
   to an available Action plugin.

Other arguments are passed as-is to the Action plugin and are therefore
specific to the action being defined. For example, the 'command' Action:

-  cmd : **Required** : The command, with arguments, to run.
-  path : *Optional* : Working directory to run the command within.
-  env : *Optional* : An array of environment variables that will be
   defined when the command is run.
