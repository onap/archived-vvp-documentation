.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. http://creativecommons.org/licenses/by/4.0
.. Copyright 2019 AT&T Intellectual Property.  All rights reserved.

Heat Validation via Command Line
================================

.. note::
    This section assumes you have already acquired and setup the validation
    scripts as described in the :ref:`VVP Installation <vvp-installation>`
    section.

Basic Script Execution
----------------------

1.  The scripts must be executed from the ``ice_validator`` directory.
    Navigate to the directory first::

    > cd <vvp-directory>/ice_validator

2.  To run the script with default options, issue the following command where
    ``<Directory>`` is the directory containing the Heat templates::

    /path/to/validation-scripts/ice_validator>$ pytest tests --template-directory=<Directory>``

3.  Test results will be written to the consoles describing any failures that
    are encountered.  If no failures are found, then a success message will
    be displayed.  Additional reports can be found in the
    ``<VVP Directory>/ice_validator/output`` directory.  See
    :ref:`Reports <vvp-reports>` for more details.


.. _vvp-cmd-options:

Command Line Options
--------------------

Additional options can be specified at the command line to customize output
and execution.  VVP uses the `pytest <https://docs.pytest.org>`__
framework for test execution, and therefore all pytest options are available.

Please refer to the pytest documentation for information on the command-line
options that framework provides.  We will only document the specific options
that are used by VVP::

      --template-directory=TEMPLATE_DIR
                            Directory which holds the templates for validation

      --category=CATEGORY_NAME
                            Additional validations that can be selected.
                            The only supported value at this time is
                            environment_file which will enforce that specific
                            parameter values are excluded from the environment
                            file per ONAP Heat requirements.

      --self-test           Test the unit tests against their fixtured data

      --report-format=REPORT_FORMAT
                            Format of output report (html, csv, excel, json)

      --continue-on-failure
                            Continue validation even when structural errors exist
                            in input files

      --output-directory=OUTPUT_DIR
                            Alternate directory for report output.

.. _vvp-reports:

VVP Reports
-----------

After completion of the validations, several reports will be written to the
ouput directory (specified via ``--output-directory`` or
``<vvp-directory>/ice_validator/output`` by default).


+-----------------------+------------------------------------------------------+
| Report                | Description                                          |
+=======================+======================================================+
| ``report.{html,csv,`` | Full report of validation results showing the pass   |
|         ``xlsx,json}``| or failure of the validation and details on all      |
|                       | errors.  See :ref:`Validation Report <vvp-report>`   |
|                       | for information on the HTML, CSV, and Excel versions |
|                       | of the report, or                                    |
|                       | :ref:`JSON Report <vvp-json-report>` for details on  |
|                       | the machine-readable results.                        |
+-----------------------+------------------------------------------------------+
| ``traceability.csv``  | This shows a mapping from Heat requirement to        |
|                       | test case.                                           |
+-----------------------+------------------------------------------------------+
| ``mapping_errors.csv``| File should be empty, but if present shows tests     |
|                       | that are mapped to requirements that no longer exist |
+-----------------------+------------------------------------------------------+
| ``traceability.rst``  | Similar information to ``traceability.csv``, but     |
|                       | in reStructuredText for use in the VNF Requirements  |
|                       | documentation.                                       |
+-----------------------+------------------------------------------------------+
| ``failures``          | **Deprecated** JSON version of test failures.  Use   |
|                       | ``report.json`` instead.                             |
+-----------------------+------------------------------------------------------+

.. _vvp-report:

Validation Report
~~~~~~~~~~~~~~~~~

If the report format of ``html`` (*default*), ``excel``, or ``csv`` are
requested via the ``--report-format`` option, then the a report file will
be written to the output directory.  Regardless of format, the file will contain
a header section that summarizes the results and files scanned, and an error
section that has a row for each failure with four columns.

Header Information
^^^^^^^^^^^^^^^^^^

+-----------------------+------------------------------------------------------+
| Header Element        | Description                                          |
+=======================+======================================================+
| Categories Selected   | Any additional categories selected via ``--category``|
+-----------------------+------------------------------------------------------+
| Tool Version          | Version of the tool that produced the report         |
+-----------------------+------------------------------------------------------+
| Report Generated At   | The timestamp of when the report was generated       |
+-----------------------+------------------------------------------------------+
| Directory Validated   | Absolute path to directory validated                 |
+-----------------------+------------------------------------------------------+
| Checksum              | Unique MD5 has of the contents of template directory |
+-----------------------+------------------------------------------------------+
| Total Errors          | Number of errors/violations found                    |
+-----------------------+------------------------------------------------------+


Error Information
^^^^^^^^^^^^^^^^^

If any violations are found, then there will be one row for each violation
with the following columns:

+-----------------------+------------------------------------------------------+
| Column Name           | Description                                          |
+=======================+======================================================+
| Files                 | The file or files that were scanned as part of the   |
|                       | test.                                                |
+-----------------------+------------------------------------------------------+
| Tests                 | Name of the test case (not shown in HTML version)    |                                              |
+-----------------------+------------------------------------------------------+
| Error Message         | This shows the test and brief error message from the |
|                       | test that failed.  This will contain details about   |
|                       | the element that triggered the violation such as the |
|                       | parameter name, resource ID, etc.                    |
|                       |                                                      |
|                       | In the HTML version of the report this column will   |
|                       | also show the test case name, and provide a link to  |
|                       | ``Full Details`` the raw output of the test          |
+-----------------------+------------------------------------------------------+
| Requirements          | The requirement ID and text that was violated        |
+-----------------------+------------------------------------------------------+
| Resolution Steps      | For some violations, there are pre-defined resolution|
|                       | steps that indicate what action the user should take |
|                       | to resolve the violation.                            |
|                       |                                                      |
|                       | **Note**: Not all violations will have resolution    |
|                       | steps, rather the error message and requirement is   |
|                       | sufficient.                                          |
+-----------------------+------------------------------------------------------+
| Raw Test Output       | Full output from the pytest test case. This not a    |
|                       | dedicated column in the HTML version of the report.  |
+-----------------------+------------------------------------------------------+


.. _vvp-json-report:

JSON Report
~~~~~~~~~~~

This report is intended to provide a machine-readable version of the test
execution, and provides the most comprehensive summary of the test execution
and results.

File Header/Top Level
^^^^^^^^^^^^^^^^^^^^^

JSON Report <vvp-json-report>
The top level will include a summary of available execution metadata.

NOTE:  The ``tests`` and ``requirements`` entries are elided in the
example below.

**Example Header**:

.. code-block:: javascript

    {
      "version": "dublin",
      "template_directory": "/path/to/template",
      "timestamp": "2019-01-21T02:11:07.305000",
      "checksum": "6296aa211870634f9b4a23477c5eab28",
      "profile": "",
      "outcome": "FAIL",
      "tests": [],
      "requirements": [],
    }

**Header Definition**:

.. csv-table::
    :header-rows: 1
    :file: vvp_json_header.csv

.. _vvp-test-result:

Test Result
^^^^^^^^^^^

For each test result a JSON object will be provided that informs the consumer
what tests was run, its result, and the requirements it validated.

**Example Test Result**:

.. code-block:: javascript
    {
      "files": [
        "/Users/username/Desktop/stark_template2/STARKDB-nested-1.yaml",
        "/Users/username/Desktop/stark_template2/base_starkdb.yaml",
     ],
      "test_module": "test_resource_indices",
      "test_case": "test_indices_start_at_0_increment",
      "result": "FAIL",
      "error": " Index values associated with resource ID prefix STARKDB_server_ do not start at 0\n",
      "requirements": [
        {
          "id": "R-11690",
          "text": "When a VNF's Heat Orchestration Template's Resource ID contains an\n``{index}``, the ``{index}`` is a numeric value that **MUST** start at\nzero and **MUST** increment by one.\n\nAs stated in R-16447,\n*a VNF's <resource ID> MUST be unique across all Heat\nOrchestration Templates and all HEAT Orchestration Template\nNested YAML files that are used to create the VNF*.  While the ``{index}``\nwill start at zero in the VNF, the ``{index}`` may not start at zero\nin a given Heat Orchestration Template or HEAT Orchestration Template\nNested YAML file.",
          "keyword": "MUST"
        }
      ]
    }


**Test Result Definition**:

.. csv-table::
    :header-rows: 1
    :file: vvp_json_test_result.csv


.. _vvp-req-metadata:

Requirement Metadata
^^^^^^^^^^^^^^^^^^^^

For each test case, the following requirement metadata will be reported.

**Example Requirement Metadata**:

.. code-block:: javascript

    {
      "id": "R-11690",
      "text": "When a VNF's Heat Orchestration Template's Resource ID contains an\n``{index}``, the ``{index}`` is a numeric value that **MUST** start at\nzero and **MUST** increment by one.\n\nAs stated in R-16447,\n*a VNF's <resource ID> MUST be unique across all Heat\nOrchestration Templates and all HEAT Orchestration Template\nNested YAML files that are used to create the VNF*.  While the ``{index}``\nwill start at zero in the VNF, the ``{index}`` may not start at zero\nin a given Heat Orchestration Template or HEAT Orchestration Template\nNested YAML file.",
      "keyword": "MUST"
    }


**Requirement Metadata Definition**:

+-----------------+-------------+--------------+-----------+-------------------+
| Field Name      | Required    | Data Type    | Valid     | Description       |
|                 | Optional    |              | Values    |                   |
|                 | Conditional |              |           |                   |
+=================+=============+==============+===========+===================+
| ``id``          | Required    | ``string``   |           | Requirement ID    |
|                 |             |              |           | from the VNFRQTS  |
|                 |             |              |           | project           |
+-----------------+-------------+--------------+-----------+-------------------+
| ``text``        | Required    | ``string``   |           | Full text of      |
|                 |             |              |           | requirement       |
+-----------------+-------------+--------------+-----------+-------------------+
| ``keyword``     | Required    | ``string``   | MUST,     | RFC 2119 keyword  |
|                 |             |              | MUST NOT, | of the requirement|
|                 |             |              | MAY,      |                   |
|                 |             |              | SHOULD,   |                   |
|                 |             |              | SHOULD NOT|                   |
+-----------------+-------------+--------------+-----------+-------------------+


.. _vvp-req-result:

Requirement Result
^^^^^^^^^^^^^^^^^^

The file also includes an aggregated view of adherence to the VNF Requirements
validated by the validation scripts.  Since some requirements have multiple
test cases, these results roll-up the result to an aggregated result for each
requirement. This section does not include detailed test results.  If you
require detailed error information, then refer to the tests section of the
results.

**Example Requirement Result**:

.. code-block:: javascript

    {
      "id": "R-16447",
      "text": "A VNF's <resource ID> **MUST** be unique across all Heat\nOrchestration Templates and all HEAT Orchestration Template\nNested YAML files that are used to create the VNF.",
      "keyword": "MUST",
      "result": "FAIL"
      "errors": [
         "The error message"
      ]
    }


**Requirement Result Definition**:

.. csv-table::
    :header-rows: 1
    :file: vvp_json_req_result.csv


.. _vvp-docker-execution:

Docker Execution
----------------

A version of VVP is also provided as a Docker image.  If your environment
supports Docker, then this eliminates the need to setup and install
the application from source code.

To execute from Docker, issue the following command where
``<Local Template Directory>`` is where the Heat templates are located and
``<Local Report Directory>`` is where you would like the reports to be
written on your local machine::

    docker run --rm -i -v ~/<Local Template Directory>/:/template \
    -v ~/<Local Report Directory>:/reports \
    onap/vvp/validation-scripts --template-directory=/template \
    --output-directory=/reports

The same :ref:`command line options <vvp-cmd-options>` can be used with the
Docker image that are used with the version from source.

Self-Test Suite
---------------

The ``ice_validator`` includes an extensive self-test suite. It is a
**requirement** for any additions or changes to the test suite to
successfully and cleanly complete a tox run. Simply run ``tox`` from
the project root as:

``$ tox``

You can also run it under the folder ``ice_validator``:

``$ pytest --self-test``





