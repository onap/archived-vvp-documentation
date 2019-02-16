.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. http://creativecommons.org/licenses/by/4.0
.. Copyright 2019 AT&T Intellectual Property.  All rights reserved.

Graphical User Interface
========================

If desired, a graphical user interface is also provided when the application
has been :doc:`installed from source <installation>`.  This can provide a
convenient wrapper that enables users to perform validations without using
extensive command-line usage.

At this time the application can only be run from source, but in the future
a packaged version may be provided.


How to Start the Application
----------------------------

1.  Ensure you have installed the application and its dependencies from
    source as described in the :doc:`Installation chapter <installation>`

2.  Navigate to the ``ice_validator`` directory::

    > cd <vvp-directory>/ice_validator

3.  Launch the gui using the ``vvp.py`` command::

    > python vvp.py


How to Use the Tool
-------------------

.. note::
    The look-and-feel of the application will vary slightly depending
    on the Operating System of the host machine.  The screenshot below is how
    the application looks on a Windows machine.

**Sample Screenshot of VVP GUI Application**

.. image:: vvp_app.png


All configuration options available to the command-line version of the
application are exposed as options on the left-hand side of the GUI.

Additional Validation Categories
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This allows the base set of tests to be extended by selecting additional
categories of tests.  At this time, only one additional category is supported.

This maps to the ``--category`` command-line option.

+----------------------+-------------------------------------------------------+
| Category             | Description                                           |
+======================+=======================================================+
| Environment          | When selected, VVP will flag parameters in environment|
| File Compliance      | files that should be excluded per the ONAP Heat       |
|                      | requirements.  De-selecting this can be useful when   |
|                      | when testing instantiation directly in OpenStack      |
|                      | without ONAP orchestration.  This is equivalent to    |
|                      | specifying ``--category=environment_file`` from the   |
|                      | command-line.                                         |
+----------------------+-------------------------------------------------------+

Settings
~~~~~~~~

**Verbosity** - Controls the level of comparison output displayed in the
underlying pytest validation output.  Default maps to pytest ``-v`` level of
verbosity.

**Report Format** - Controls the format of the output report generated after
validation.  The options are: HTML (the default), Excel, and CSV.  This is
equivalent the ``--report-format`` command-line option with the exception,
that the JSON format is not supported via the GUI.

**Input Format** - Controls the expected format of the template files.  This
can either be a ZIP file containing the Heat templates or a directory
containing the Heat templates.  There is no ZIP file option for the command-line
script at this time.

**Halt on Basic Failures** - VVP deems certain tests as "base tests" which if
failed have the potential to generate a large number of other errors. This
would include tests such as validating that the Heat templates are valid
YAML.  If checked, the tool will immediately stop all other tests and show
a report of this single failure. This can be useful in reducing the number
of errors to sift through in these situations.  De-selecting this option
is the equivalent of specifying ``--continue-on-failure`` as a command-line option.

Running Validations
~~~~~~~~~~~~~~~~~~~

1. Select the desired input format in the settings (ZIP or Directory)
2. Select the [...] button next to the Template Location input box
3. Select the directory or ZIP file containing the Heat templates, and then
   click Open
4. Once the input is selected, select the "Validate Templates" button to
   start the validation process.  The white box to the right will
   display output as the validations are executed.
5. Once validation is complete a summary of pass and fail will be written
   to the output window, and a "View Report" option will appear on the
   left-hand control panel.
6. Select the "View Report" option, and the report will be opened in the
   the appropriate application based on report format.
7. If you have questions about report output, please refer to the
   :ref:`Validation Report reference material <vvp-report>` for more
   information.
