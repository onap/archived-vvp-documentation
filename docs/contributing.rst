.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. http://creativecommons.org/licenses/by/4.0
.. Copyright 2019 AT&T Intellectual Property.  All rights reserved.

Contributing
############

Objective
~~~~~~~~~

**The objective for the VVP test suite is for each
test to directly correlate with at least one requirement in the**
`VNF Requirements <https://onap.readthedocs.io/en/latest/submodules/vnfrqts/requirements.git/docs/index.html>`__
**project in ONAP. If the test you intend to write doesn't
have a corresponding requirement in the VNF Requirements project, consider
making a contribution to that project first.**

Convenience vs Convention
~~~~~~~~~~~~~~~~~~~~~~~~~

There are a lot of ways to write tests. Priorities for the VVP test suite are

 - Accuracy
 - User Comprehension

The test suite is often used by people who don't write code, or people
who aren't devoted to writing python validation tests.

The output of failed validation tests can be difficult to read, so
keep that in mind when you are deciding whether to create another
level of abstraction vs having some code duplication or verbose tests.

Writing Tests
#############

File Name
~~~~~~~~~

Test files are written in python, and should go into the
``/validation-scripts/ice_validator/tests/`` directory. They should be prefixed
with ``test_``. If not, ``pytest`` will not discover your test.

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

Each test function should be decorated with a requirement ID from the
VNF Requirements project. The following is required to be imported at
the top of the test file:

``from .helpers import validates``

Then, your test function should be decorated like this:

.. code-block:: python

  @validates("R-123456",
             "R-123457") # these requirement IDs should come from the VNFRQTS project
  def test_my_new_requirement():

This decorator is used at the end of the test suite execution to generate a
report that includes the requirements that were violated. If a test is not
decorated it is unclear what the reason for a failure is, and the
implication is that the test is not needed.

Test Parameters
~~~~~~~~~~~~~~~

Each test should be parameterized based on what artifact is being validated.
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
test suite execution is halted. If you are writing a ``base`` test, mark your
test like this:

.. code-block:: python

  import pytest

  @pytest.mark.base # this is the base test marker
  @validates("R-123456")
  def test_my_new_requirement():

The VVP test suite also has the concept of a ``category`` to
define what additional set of optional tests to execute. The way it works
is by using ``categories`` decorator.

By default, all ``base`` tests and tests with no category are executed.
If you want an additional category to run, pass the command line argument:

``--category=<category>``

This will execute all ``base`` tests, non-marked tests,
and tests marked like the following:

.. code-block:: python

  import pytest

  @categories("<category>") # substitue <category> with the category name
  @validates("R-123456")
  def test_my_new_requirement():

This should be used sparingly, and in practice consider reviewing a requirement
with the VNF Requirements team before adding a test to a category.
