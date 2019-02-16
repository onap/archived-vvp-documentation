.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. http://creativecommons.org/licenses/by/4.0
.. Copyright 2019 AT&T Intellectual Property.  All rights reserved.

VVP Documentation
=================

The VNF Validation Platform (VVP) is an application to validate that `OpenStack
Heat Templates <https://wiki.openstack.org/wiki/Heat>`__ comply with the
ONAP requirements and guidelines documented in the :doc:`Heat section <../../../../../submodules/vnfrqts/requirements.git/docs/Chapter5/Heat/index>` of
the ONAP's :doc:`VNF Requirements and Guidelines documentation <../../../../../guides/onap-provider/index>`.

Adherence to these guidelines ensures that a VNF can be successfully
onboarded, modeled, instantiated, and orchestrated by ONAP to the fullest
extent possible.

VVP is a utility written in Python that can be executed via a
:doc:`command-line script <validation-scripts>`,
:ref:`Docker container <vvp-docker-execution>`, or a
native :doc:`Desktop GUI application <humaninterfaces>` to analyze and report
on the compliance of a given set of Heat templates to the ONAP requirements.

This guide will provide the user with instructions on how to acquire,
setup, and execute the validations.

.. toctree::
   :maxdepth: 1

   installation.rst
   validation-scripts.rst
   humaninterfaces.rst
   release-notes.rst
