.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. http://creativecommons.org/licenses/by/4.0
.. Copyright 2019 AT&T Intellectual Property.  All rights reserved.

.. _vvp-installation:

Installation
============

The VNF Validation Platform (VVP) can be run from source as a normal
Python executable or a Docker image is provided.  This section will
describe how to setup and install the ``vvp/validation-scripts`` and
run from source.

Installation and configuration of Docker is beyond the scope of this document,
but you can refer to the :ref:`Docker Execution <vvp-docker-execution>`
instructions for more details on running the validations as from
the Docker image.

Pre-requisites
--------------

This document assumes you have the following system-level utilities
installed.

Please refer to the respective sites for these tools for the appropriate
installation instructions for your given operating system.

* `Python 3.6+ <https://www.python.org/downloads/>`__
* `Git <https://git-scm.com/>`__

Setup
------

The source code for VVP can be obtained from the `ONAP Gerrit site <https://gerrit.onap.org/r/#/admin/projects/vvp/validation-scripts>`__
or its `GitHub mirror <https://github.com/onap/vvp-validation-scripts>`__.

1.  Clone the source from your desired repository host:

    Choose **one** of the following ``git clone`` commands::

    > git clone https://github.com/onap/vvp-validation-scripts.git
    > git clone https://gerrit.onap.org/r/vvp/validation-scripts

2.  (*Optional*) If desired, you can create a virtual Python environment to
    avoid installing VVP's dependencies in your system level installation
    of Python::

    > python -m venv vvp
    > source vvp/activate

3. Install the required dependencies with the following command::

    > python pip install -r requirements.txt
