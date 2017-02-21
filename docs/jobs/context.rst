.. _context-info:

########
Contexts
########

Cyclid defines the following Job context variables for every job. Cyclid or
some plugins might define some other internal-only context variables which
are not documented here.

+----------------+-----------------------------------------------------------------------------+
| Name           | Comment                                                                     |
+================+=============================================================================+
| distro         | The Operating SYstem or Distribution name of the build host E.g. ``ubuntu`` |
+----------------+-----------------------------------------------------------------------------+
| job_id         | The current Job ID.                                                         |
+----------------+-----------------------------------------------------------------------------+
| job_name       | The name of the current Job.                                                |
+----------------+-----------------------------------------------------------------------------+
| job_version    | The version of the current Job                                              | 
+----------------+-----------------------------------------------------------------------------+
| organization   | The current organization that the Job is running under.                     |
+----------------+-----------------------------------------------------------------------------+
| os             | The combined OS & release of the build host E.g. ``ubuntu_trusty``          |
+----------------+-----------------------------------------------------------------------------+
| packages       | The list of additional packages installed on the build host.                |
+----------------+-----------------------------------------------------------------------------+
| release        | The OS or Distro version of the build host E.g. ``trusty``                  |
+----------------+-----------------------------------------------------------------------------+
| repos          | A list of additional repositories defined in the Job environment.           |
+----------------+-----------------------------------------------------------------------------+
| username       | The current build host user that the job is running under.                  |
+----------------+-----------------------------------------------------------------------------+
| workspace      | Path to the build host working directory E.g. ``/home/build```              |
+----------------+-----------------------------------------------------------------------------+
