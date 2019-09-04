.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. http://creativecommons.org/licenses/by/4.0
.. Copyright 2019 AT&T Intellectual Property.  All rights reserved.

.. _vvp-preload-gen:

Preload Generation
##################

Overview
--------

To maximize the value of ONAP, the `ONAP Heat requirements <https://onap.readthedocs.io/en/latest/submodules/vnfrqts/requirements.git/docs/Chapter5/Heat/index.html>`__
are defined to ensure that configuration that will vary per environment/instantiation
should be defined as inputs into the Heat templates via parameters
and excluded from ``.env`` files.  This ensures that when a VNF described by
a set of Heat templates is onboarded into SDC for modeling, the model remains
generic and reusable across multiple environments.

In order to instantiate a VNF and its VF Modules, ONAP must be able to
supply the instance specific configurations when a module is instantiated.  The
most common method to achieve this is to register a **preload** for
your VF module into SDNC.  The preload is a JSON artifact that:

1. Provides identifiers that map it to a specific VF Module in SDC
2. Provides values that will be passed into the Heat templates' ``parameters``

When ONAP instantiates the VF Module, SDNC will find the preload based on the
**SDC Model Identifiers**, and then map the parameter values in the preload to
the appropriate ``parameters`` in the Heat template.

The validation tool/scripts will generate a "blank" preload template in the
requested format (VNF-API or GR-API) that the end user will complete prior
to :ref:`registering the preload to SDNC <vvp_register_preload>`.

Optionally, the end user can specify a directory containing ``.env`` files
and other special files (``defaults.yaml``, CSAR package) that can generate
not just blank templates, but fully or partially completed preload templates.

This section will describe how to perform these actions.

Blank Preload Template Generation
---------------------------------

As part of normal validation, VVP will generate a blank preload template
for the user to complete.

If validating via the command line, then the
resulting blank templates will be in the
``<output-directory>/preloads/<preload-format>/`` directory. Where ``output-directory``
is either the default output directory under ``ice_validator`` or a custom
directory if the default was overridden by the ``--output-directory``
parameter.  If ``--preload-format`` was specified, then only that format will
be produced; otherwise all available formats will be produced.

If validating via the GUI, then a link to the preload templates will be
provided at the end of the validation.  The preload format will be in the
format selected in the GUI's settings.

**Populating the Preload Template**

.. note::

    The resulting JSON file(s) should be copied to a new directory to avoid
    being over-written by subsequent validations.

The preload template will be pre-populated based on the VMs, networks,
sub-networks, IPs, availability zones, and parameters in the Heat templates.
Every value that must be provided will be prefixed by ``VALUE FOR:`` and will
include either the parameter name that the value will map to or will describe
the value that should be provided if it is not related to a Heat parameter.

.. code-block:: json
    :caption: Example Partial VNF-API Preload

    {
        "availability-zones": [
            {
                "availability-zone": "VALUE FOR: availability_zone_0"
            },
            {
                "availability-zone": "VALUE FOR: availability_zone_1"
            }
        ],
        "vnf-networks": [
            {
                "network-role": "private",
                "network-name": "VALUE FOR: network name for private_net_name"
            },
            {
                "network-role": "oam",
                "network-name": "VALUE FOR: network name for oam_net_id"
            }
        ],
        "vnf-vms": ["OMITTED FOR THIS EXAMPLE"]
    }

There are instances where a parameter is defined ``comma-delimited-list`` in
Heat.  In these instances, the parameter name will be repeated for each value
that needs to be provided.  For example, if there are three virtual machines
defined in the template and the names are pulled from a parameter called
``{vm-type}_names``, then ``VALUE_FOR: {vm-type}_names`` will be repeated
three times in the template.

.. code-block:: json
    :caption: Example: comma-delimited-list parameters
    :emphasize-lines: 6-8

    {
        "vm-type": "admin",
        "vm-count": 3,
        "vm-names": {
            "vm-name": [
                "VALUE FOR: admin_names",
                "VALUE FOR: admin_names",
                "VALUE FOR: admin_names"
            ]
        }
    }

.. _vvp_preload_special_values:

Special Values
~~~~~~~~~~~~~~~~~~~~~

Some values are not supplied to the the Heat template directly, but instead
are used to either map the preload template to the SDC model **or** provide
instance specific identifiers such as a module or VNF name.  These will
still be prefixed with ``VALUE FOR:``.

.. note::

    Refer to the section below on :ref:`Preload Template Population <vvp_preload_population>`
    for alternate ways to populate this information.

The values are:

+-----------------+-------------+--------------+-------------------------------+
| Value           | VNF-API     | GR-API       | Source & Description          |
|                 | Name        | Name         |                               |
+=================+=============+==============+===============================+
| VNF Name        | vnf-name    | vnf-name     | Name of the VNF as it will    |
|                 |             |              | appear in A&AI. This is user  |
|                 |             |              | defined and does not need to  |
|                 |             |              | match a value in SDC.         |
+-----------------+-------------+--------------+-----------+-------------------+
| Virtual         | generic-vnf-| vnf-type     | Maps preload to a specific    |
| Function        | type        |              | instance of the VF model from |
| Instance        |             |              | SDC. This field is must be in |
|                 |             |              | the format of:                |
|                 |             |              | <Service Name>/<VF Instance   |
|                 |             |              | Name> and must match SDC      |
+-----------------+-------------+--------------+-----------+-------------------+
| VF              | vnf-type    | vf-module-   | Maps preload to the           |
| Module          |             | type         | ``vfModuleModelName`` from    |
| Model           |             |              | the VSP CSAR or the value     |
|                 |             |              | from SDC                      |
+-----------------+-------------+--------------+-----------+-------------------+


.. _vvp_preload_population:

Preload Template Population
---------------------------

Basic Usage - Single Environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

VVP can also generate fully or partially populated preload templates if a
an optional environment directory is provided as a source for the parameter
values.

When using the command line, the environment directory is provided using the
``--env-directory`` parameter.

When using the GUI, the environment directory is provided by first selecting
the **Create Preloads from Env Files** option in **Settings** and then providing
the directory in the **Env Files** field.

A template for the environment directory is created in the output directory
under ``preloads/<preload-format>/preload_env``. There will be an ``*.env`` file
for every VF module in the Heat template and a ``defaults.yaml``.  This
directory can be copied and updated to serve as a data source for populating
the preload templates.  Every value that needs to be updated will be set to
``CHANGEME``.

.. code-block:: yaml
    :caption: Example Partial env file (``base.env``)

    parameters:
      ctrl_net_id: CHANGEME
      ctrl_subnet_id: CHANGEME
      db_ha_floating_ip: CHANGEME
      db_ha_floating_v6_ip: CHANGEME
      svc_flavor_name: svc_flavor
      svc_image_name: svc_image
      db_name_0: CHANGEME

Each environment directory consists of three file types:

1. **Environment files (.env)** - One file per VF module with the base name of
   the env file matching the base name of the heat template it corresponds to.
2. **defaults.yaml** - Values specified in here will be used in all modules.
   This is a useful place to put values that will be the same in every VF
   module.  It is important to note that the environment files take precedence
   so if you specify a value in ``defaults.yaml``, then that value should be
   removed from the environment files.
3. **VSP CSAR** - Optionally the CSAR can be downloaded from the SDC Artifacts
   section, and put into the directory.  If present, then the
   :ref:`SDC Model Identifiers <vvp_preload_special_values>` will be pulled from
   the CSAR instead of them being manually specified

After template processing completes, the blank preload templates will still
be generated to the output directory.  The **populated** templates will be
generated the ``<env-directory>/preloads/<preload-format>`` directory.  If a
template was not fully populated, then it will be suffixed with ``_incomplete``
(ex: ``base_incomplete.json``)


Advanced Usage - Multiple Environments
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Using a single environment directory is the most basic use case, but you may
need to generate preloads for multiple environments where some values do not
change by environment while others do.

To enable this capability, environment directories can be nested and inherit
values from their parent directories.  The parent directory's environment files
and ``defaults.yaml`` files should contain the default values for every
environment.

These can be overridden in the child environment directories so
if you want to force each environment to provide a value, then do not specify
that value in any parent directory.

.. code-block:: none
    :caption: Example Nested Environment Directory

    env_directory/
    |--- vsp_name.csar          <-- Global CSAR for all enviroments (assumes shared SDC instance)
    |--- defaults.yaml          <-- Global defaults for all env and modules
    |--- base.env               <-- Global defaults for base modules
    |--- mod_one.env            <-- Global defaults for mod_one modules
    |--- env_one/
    |    |--- defaults.yaml     <-- env_one specific defaults for all modules
    |    |--- base.env          <-- env_one values for the base module
    |    |--- mod_one.env       <-- env_one values for mod_one
    |--- env_two/
         |--- defaults.yaml     <-- env_two specific defaults for all modules
         |--- base.env          <-- env_two values for the base module
         |--- mod_one.env       <-- env_two values for mod_one


Preload templates will be generated in the **leaf** directories of the environment
directory.  In this example, preload templates will be generated in ``env_one``
and ``env_two``, but **not** ``env_directory``.

The value supplied to the preload will follow the following order of precedence
with the value being looked up from each of the following in turn and halting
once the value is found:

1. Corresponding ``.env`` file for the module in the environment specific
   directory (ex: ``env_one/base.env``)
2. The ``defaults.yaml`` file in the environment specific directory (ex:
   ``env_one/defaults.yaml``)
3. The CSAR file in the environment specific directory (NOTE: only the
   **special values** are looked up from the CSAR
4. If not found in the environment specific, then the parent directory will be
   searched using the same precedence (1-3).

This lookup chain will continue until the root environment directory is reached.
If no value is found, then the template will continue revert the value from
the blank template (i.e. ``VALUE FOR: {parameter-name}``)

The environment directories can be nested as deeply as needed to map your needs.
For example you could have a global directory, production and test environments,
and then multiple sub-environments in each test nd production directory.

**Example:**

.. code-block:: none
    :caption: Example Nested Environment Directory

    env_directory/
    |--- vsp_name.csar
    |--- defaults.yaml
    |--- base.env
    |--- mod_one.env
    |--- production/
    |    |--- defaults.yaml
    |    |--- base.env
    |    |--- mod_one.env
    |    |--- prod_one/
    |    |   |--- defaults.yaml
    |    |   |--- base.env
    |    |   |--- mod_one.env
    |    |--- prod_two/
    |        |--- defaults.yaml
    |        |--- base.env
    |        |--- mod_one.env
    |--- test/
         |--- defaults.yaml
         |--- base.env
         |--- mod_one.env
         |--- test_one/
         |   |--- defaults.yaml
         |   |--- base.env
         |   |--- mod_one.env
         |--- test_two/
             |--- defaults.yaml
             |--- base.env
             |--- mod_one.env

Alternate Method for Specifying Special Values
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you wish to populate the special values without providing a CSAR, then add
the following parameters and values to either your ``defaults.yaml`` file or
the appropriate ``.env`` file.

* ``vnf-name`` - Corresponds to VNF Name in :ref:`special values <vvp_preload_special_values>`
* ``vnf-type`` - Corresponds to Virtual Function Instance in :ref:`special values <vvp_preload_special_values>`
* ``vf-module-model-name`` - Corresponds to VF Module Model Name in :ref:`special values <vvp_preload_special_values>`


.. _vvp_register_preload:

Registering Preload with SDNC
-----------------------------

At the time of this writing, the ONAP documentation does not provide a good
source of documentation on how to provide preloads to SDNC.  The options known
are to either use a REST ``POST`` call to the following APIs based on format:

.. note::

    Host and port numbers may vary in your ONAP environment

* **GR-API** - https://sdnc.api.simpledemo.onap.org:30267/restconf/operations/GENERIC-RESOURCE-API:preload-vf-module-topology-operation
* **VNF-API** - https://sdnc.api.simpledemo.onap.org:30267/restconf/operations/GENERIC-RESOURCE-API:preload-vf-module-topology-operation

The ONAP-CLI project could also be used, but it only supports the **VNF-API**
as of the writing of this document.

Customizing Preload Generation
------------------------------

VVP's preload generation capability leverages a plugin mechanism to enable additional
preload formats to be added.  This can be useful if you define an intermediary
format such as a spreadsheet to capture the preload information.

Preload plugins are discovered using the following method.

1. The ``sys.path`` is scanned to find any top level modules that begin with
   ``preload_``
2. If found, then any implementations of ``AbstractPreloadGenerator`` are
   registered as available formats
3. You can selectively disable formats by specifying them as excluded in the
   ``vvp-config.yaml`` file.

Please refer to the ``preload_vnfapi.VnfApiPreloadGenerator`` and
``preload_grapi.GrApiPreloadGenerator`` for examples of how to implement a
generator.
