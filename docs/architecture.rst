.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. http://creativecommons.org/licenses/by/4.0
.. Copyright 2017 AT&T Intellectual Property.  All rights reserved.


Architecture
===============

The architecture of VVP based on micro-services. Each element has
dedicated container.
VVP uses rocket and kubernetes to manage the VVP system.
There is a devkit component which allows you to render and deploy VVP.
It's render and deploy the rockets with kuberenetes management (via quay.io).

We store files on Ceph distributed storage system that includes 'RADOS Gateway'
which allows us to store files in AWS S3 based API (buckets etc..).

Architecture Alignment
----------------------------

How does this project fit into the rest of the ONAP Architecture?
* The VNF Validation Program will utilize the architecture to validate VNFs against it.
What other ONAP projects does this project depend on?
* This project depends on SDC, VNF Validation Program, and VNF Requirements
How does this align with external standards/specifications?
* Are there dependencies with other open source projects?
  * OpenStack

Interactions
-------------------

This is the list of containers part of VVP application:
* ci-uwsgi - end-to-end flow tests based on Seleniunm
* cms-nginx - webserver of CMS
* cms-uwsgi - backend uwsgi server which hosts django application
* em-nginx - webserver of engagement manager
* em-uwsgi - backend uwsgi server which hosts django application
* ext-haproxy - load balancer for external transport
* int-haproxy - load balancer for internal (container to container) transport
* gitlab - holds all customers files in repos
* imagescanner - scan for validity and viruses on users files
* jenkins - run validation tasks
* portal - run the UI/UX application AKA VVP-Portal
* postgresql - store all data of engagement manager
* redis - in memory key-value store for all project
* celery - task queue manager which manage all validation tasks

Repo name:
* org.onap.vvp/devkit
* org.onap.vvp/ansible-ice-bootstrap
* org.onap.vvp/portal
* org.onap.vvp/engagementmgr
* org.onap.vvp/cms
* org.onap.vvp/jenkins
* org.onap.vvp/haproxy
* org.onap.vvp/postgresql
* org.onap.vvp/gitlab
* org.onap.vvp/jeeves
* org.onap.vvp/test-engine
* org.onap.vvp/validation-scripts
* org.onap.vvp/documentation
* org.onap.vvp/image-scanner
