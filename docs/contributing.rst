.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. http://creativecommons.org/licenses/by/4.0
.. Copyright 2019 AT&T Intellectual Property.  All rights reserved.

How to Contribute
#################

Overview
~~~~~~~~

This section will provide details on how to contribute changes to the project
covering both the mechanics of how to contribute new code as well as
how to adhere to the code quality and coding practices of the project.


Prerequisites
~~~~~~~~~~~~~

As this project is part of the overall ONAP project, there are some common
guidelines and activities you will need to adhere to:

- Acquire a `Linux Foundation ID <https://identity.linuxfoundation.org/>`__
- Configure your `ONAP Gerrit Account <https://wiki.onap.org/display/DW/Configuring+Gerrit>`__
  as it is used for all code submissions and reviews
- Install VVP per the :ref:`installation instructions <vvp-installation>`
- Most features will require some knowledge of both `Openstack Heat <https://wiki.openstack.org/wiki/Heat>`__
  and the `ONAP Heat requirements <https://onap.readthedocs.io/en/latest/submodules/vnfrqts/requirements.git/docs/Chapter5/Heat/index.html>`__
- Knowledge of writing tests in `pytest <https://pytest.readthedocs.io/>`__

Other useful links:

- All work is documented and tracked via the `VVP Project <https://jira.onap.org/projects/VVP/issues/>`__
  in the `ONAP JIRA instance <https://jira.onap.org/>`__. Login is via your
  Linux Foundation ID
- Proposals for new features, general information about the projects,
  meeting minutes, and ONAP process information is located on the
  `ONAP Wiki <https://wiki.onap.org/>`__
- The VVP project hosts a weekly meeting to plan upcoming work, discuss open
  issues, and align on priorities.  Please consider attending if possible if you
  intend to contribute to the project.  Refer to the ONAP Calendar `<https://wiki.onap.org/pages/viewpage.action?pageId=6587439>`__
  for scheduling details

Objective
~~~~~~~~~

The primary focus of VVP is ensuring that a VNF that is described using Openstack
Heat complies with the ONAP Heat requirements specified in the `VNF Requirements (VNFRQTS) <https://onap.readthedocs.io/en/latest/submodules/vnfrqts/requirements.git/docs/index.html>`__
project.  If a VNF does not comply with these rules, then it may not successfully
be modeled in SDC, fail to instantiate, be improperly inventoried in A&AI, or
fail orchestration.

The project aims to validate every mandatory requirement in the VNF Requirements
related to Heat (i.e. all requirements with a **MUST** or **MUST NOT** keyword)

Heat templates are validated using tests written in ``pytest``.  Each test will
validate one or more requirements. Typically we strive to have 1 test per
requirement, but there are situations where it is easiest and clearest to
validate multiple, tightly related requirements with a single test.

Every test **MUST** have a corresponding requirement in the ONAP VNF Requirements
project.  If your contribution is a test and there is not an appropriate
requirement, then please consider making a contribution to that project first.


Writing Tests
#############

Coding Conventions
~~~~~~~~~~~~~~~~~~

* Follow `PEP-8 conventions <https://www.python.org/dev/peps/pep-0008/>`__
    * NOTE: The only variation is that the line-length can be 88
      characters vs. 80
* Familiarize yourself with the utilities that exist in the following utility
  modules and leverage them to avoid duplication.

    - ``ice_validator/tests/helpers.py``
    - ``ice_validator/tests/structures.py``
    - ``ice_validator/tests/utils/**``

* Ensure all source files include the standard Apache License 2.0 text and
  appropriate copyright statement (see other source files for example)
* All code must pass standard quality checks prior to submission which can be
  executed via ``tox`` or by running ``checks.py``
* When parsing YAML, always use the ``tests/cached_yaml`` module versus the
  default ``yaml`` module.  This will greatly improve performance due to the
  large number of yaml files that must be parsed.
* In an effort to keep the number of dependencies down, please favor using
  the Python standard library unless using an external library significantly
  improves or reduces the needed code to implement the functionality.
* For security purposes the following hardening activities are used:

    - Avoid usage of ``yaml.load`` and always use ``yaml.safe_load`` (Note: if you
      use ``cached_yaml`` as instructed above, then this is covered automatically)
    - Docker containers must not be run as root (see current Dockerfile for
      an example)
    - Inspect and resolve all findings by the bandit scans

File Name
~~~~~~~~~

Test files are written in Python, and should go into the
``/validation-scripts/ice_validator/tests/`` directory. They should be prefixed
with ``test_``. If not, ``pytest`` will not discover your test.  The file name
should reflect what is being tested.

Test Name
~~~~~~~~~

Tests are functions defined in the test file, and also must be prefixed with
``test_``. If not, ``pytest`` will not collect them during execution.
For example:

**test_my_new_requirement_file.py**

.. code-block:: python

  def test_my_new_requirement():

Requirement Decorator
~~~~~~~~~~~~~~~~~~~~~

Each test function must be decorated with a requirement ID from the
VNF Requirements project. The following is required to be imported at
the top of the test file:

``from tests.helpers import validates``

Then, your test function should be decorated like this:

.. code-block:: python

  @validates("R-123456",
             "R-123457") # these requirement IDs should come from the VNFRQTS project
  def test_my_new_requirement():

This decorator is used at the end of the test suite execution to generate a
report that includes the requirements that were violated. If a test is not
decorated it is unclear what the reason for a failure is, and the
implication is that the test is not needed.

The validation reports will show the requirement text that was violated and
it will be pulled from the ``heat_requirements.json`` file.  This file
is published by the VNFRQTS project, and VVP maintains a copy of the file.
Your requirement should be present in this file.  The ``update_reqs.py``
command can be used to re-synchronize the VVP copy with VNFRQTS master.

Test Parameters
~~~~~~~~~~~~~~~

There are several dynamic fixtures that can be injected into a test based on
what the test is attempting to validate. Each test should be parameterized based
on what artifact is being validated.

Available parameters are enumerated in
``/validation-scripts/ice_validator/tests/parameterizers.py``. Below is a description
of the most commonly used:

  - ``heat_template``: parameter is the full path name for a file with the
    extenstion ``.yaml`` or ``.yml``,
    if the file also has a corresponding file with the same name but
    extension ``.env``.
  - ``yaml_file``: parameter is the full path name for a file with the
    extenstion ``.yaml`` or ``.yml``
  - ``yaml_files``: parameter is a list of all files with the extenstion
    ``.yaml`` or ``.yml``.
  - ``volume_template``: parameter is the full path name for a file name
    that ends with ``_volume`` and the extension ``.yaml`` or ``.yml``.

There are many others that can also be used, check ``parameterizers.py`` for
the full list.

The parameter that you decide to use determines how many times a test is
executed, and what data is available to validate. For example, if the
test suite is executed against a directory with 10 ``.yaml`` files, and
a test is using the parameter ``yaml_file``, the test will be executed
once for each file, for a total of 10 executions. If the parameter
``yaml_files`` (note the plural) is used instead, the test will
only execute once.

Here's an example for how to parameterize a test:

.. code-block:: python

  @validates("R-123456",
             "R-123457")
  def test_my_new_requirement(yaml_file): # this test will execute for each .yaml or .yml

Collecting Failures
~~~~~~~~~~~~~~~~~~~

To raise a violation to ``pytest`` to be collected and included on the final
violation report, use the ``assert`` statement. Example:

.. code-block:: python

  @validates("R-123456",
             "R-123457")
  def test_my_new_requirement(yaml_file):
    my test logic
    ...
    ...
    ...

    assert not failure_condition, error_message

As one of the VVP priorities is User Comprehension, the ``error_message``
should be readable and include helpful information for triaging the failure,
such as the ``yaml_file``, the parameter the test was checking, etc...

If the assert statement fails, the failure is collected by ``pytest``, and the
decorated requirements and error_message are included in the final report.

Optional: Pytest Markers and Validation Categories
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The VVP test suite has the concept of a ``base`` test. These are used as
sanity tests and are executed before the other tests, and if they fail the
test suite execution is halted. A test should be annotated with ``base`` if the
failure is likely to generate many subsequent failures (ex: improperly formatted
YAML).  If you are writing a ``base`` test, mark your test like this:

.. code-block:: python

  import pytest

  @pytest.mark.base # this is the base test marker
  @validates("R-123456")
  def test_my_new_requirement():

The VVP test suite also has the concept of a ``category`` to
define what additional set of optional tests to execute when requested by the
end user. The way it works is by applying the ``categories`` decorator to the
test.

By default, all ``base`` tests and tests with no category are executed.
If you want an additional category to run, pass the command line argument:

``--category=<category>``

This will extend the default set of tests to also include tests marked with
the requested category like the following:

.. code-block:: python

  import pytest

  @categories("<category>") # substitue <category> with the category name
  @validates("R-123456")
  def test_my_new_requirement():

This should be used sparingly, and in practice consider reviewing a requirement
with the VNF Requirements team before adding a test to a category.

Testing your Test
~~~~~~~~~~~~~~~~~

Every Heat validation test must have a unit test that validates the test is
working as expected.  This is handled by creating a one or more "fixtures" that
will exercise the test and validate the expected result.

The fixtures are stored in the ``ice_validator/tests/fixtures`` directory under
a directory that matches the test file name **exactly**.

For example, if your test is named ``test_neutron_ports.py``, then the test
fixtures must be in the ``ice_validator/tests/fixtures/test_neutron_ports/``
directory.

At minimum, each test must have one example of heat templates/files that
pass (stored in the ``pass`` subdirectory), and one example that fails (
stored in the ``fail`` subdirectory).  These templates do not need to be complete,
valid Heat template - they only need to include the minimum content to
validate the test.

If you need to test multiple conditions or branches of your test, then you
can nest other directories under your test's fixture directory.  Each nested
directory, must in turn have a ``pass`` and ``fail`` subdirectory.

.. code-block::

    ice_validator/
    |--- tests/
         |--- fixtures/
              |--- test_neutron_ports/
                   |--- scenario_one/
                   |    |--- pass/
                   |    |--- fail/
                   |--- scenario_two/
                        |--- pass/
                        |--- fail/

To execute all tests for the entire suite, issue the following commmand
from the ``ice_validator`` directory:

``pytest --self-test``

If you wish to selectively execute your test against one of the fixtures,
then issue the following command from the ``ice_validator`` directory:

``pytest tests/<test_file>.py --template-directory=tests/fixtures/<test_file>/<scenario>``

If you have contributed code outside of a ``tests_*.py`` file, then you should
create suitable tests for that functionality in the ``app_tests`` directory.
The tests should be compatible with ``pytest``, but these tests
do not use the fixtures mechanism.

Submitting Your Changes For Review
##################################

Once you have completed your changes and tested they work as expected, then the
next step is to validate they are ready for submission.  The ``checks.py``
module in the root directory contains are variety of code quality checks
that the build server will execute.  These can be executed locally using ``tox``
or simply running ``checks.py``.

At the time of this writing, the following checks will be performed:

- Executing the full test suite (``app_tests`` and ``--self-test``)
- flake8 code style validation
- Ensuring the ``heat_requirements.json`` file is up-to-date with VNFRQTS
  (run ``update_reqs.py`` if this check fails)
- Ensures all mandatory tests from VNFRQTS have tests in VVP
- Security checks via bandit

Once all tests are passed, then refer to `Pushing Changes Using Git <https://wiki.onap.org/display/DW/Pushing+Changes+Using+Git>`__
for details on how to submit your change.

Once your change has been submitted, please add the following individuals as
reviewers at minimum:

- Steven Stark
- Trevor Lovett
- Steven Wright
