.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. http://creativecommons.org/licenses/by/4.0
.. Copyright 2019 AT&T Intellectual Property.  All rights reserved.


Release Notes
===============================


Version: 4.0.0 (Dublin)
-----------------------

:Release Date: TBD

**Removed Features**

- The VVP web application has been deprecated and is no longer supported
  as of the Dublin release.  The validation scripts continue to be supported
  and enhanced, but contributions to the web-related repositories are now locked
  and VVP will no longer be supported for deployment via ONAP Operations
  Manager (OOM).

  The following repositories are now locked as of this release:

    - ``vvp/ansibile-ice-bootstrap``
    - ``vvp/cms``
    - ``vvp/devkit``
    - ``vvp/engagementmgr``
    - ``vvp/gitlab``
    - ``vvp/image-scanner``
    - ``vvp/jenkins``
    - ``vvp/portal``
    - ``vvp/postgresql``
    - ``vvp/test-engine``

**New Features**

- A new :doc:`GUI application <humaninterfaces>` has been contributed and can
  be used to execute validations in a user-friendly way without using complex
  command line options.
- VVP is now packaged as a Docker container eliminating the need to run the
  application from source code.  See the :ref:`Docker Execution <vvp-docker-execution>`
  instructions for more details.


Version: 3.0.0
--------------

:Release Date: 2018-11-30

**New Features**

- Created mapping of validation scripts to VNF Guidelines
- Increase validation script test coverage
- Created HTML report generation in validation scripts repository

**Security Notes**

VVP code has been formally scanned during build time using NexusIQ and no Critical vulnerability was found.

Quick Links:

- `VVP project page <https://wiki.onap.org/display/DW/VNF+Validation+Program+Project>`_
- `Passing Badge information for VVP <https://bestpractices.coreinfrastructure.org/en/projects/1738>`_

Version: 2.0.0
--------------

:Release Date: 2018-06-07

**New Features**

- Initial release of VNF Validation Program (VVP) for Open Network Automation Platform (ONAP).
- This intitial releases is based on seed documents that came from Open-O and Open ECOMP.
- This release provides a process to allow VNFs to be incubated and validated against the ONAP Heat Requirements.

**Bug Fixes**
- None

**Known Issues**

- As of now, the VVP Project has been created to check Validity for VNFs using Heat Orchestration Templates.
- Only deployable using OOM, will be a standalone toolkit in the future.
- UWSGI webserver dependencies.

**Security Notes**

VVP code has been formally scanned during build time using NexusIQ and no Critical vulnerability was found.

Quick Links:

- `VVP project page <https://wiki.onap.org/display/DW/VNF+Validation+Program+Project>`_
- `Passing Badge information for VVP <https://bestpractices.coreinfrastructure.org/en/projects/1738>`_

**Upgrade Notes**

- Initial release - none

**Deprecation Notes**

- Initial release - none

**Other**

	NA

===========

End of Release Notes
