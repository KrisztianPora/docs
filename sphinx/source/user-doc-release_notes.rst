.. _user-doc-release_notes:

Release Notes
*************

v1.10 (30 Nov 2021)
===================

- Deprecate novaclient, relying on openstacksdk only in nova plugin
- Drop voms support, boot volume support in nova plugin
- Remove voms auth type checking for nova plugin
- Add support for booting from volumes in case of diskless flavors
- Add support for endpoints with/wo version number in nova plugin
- Add support to pass FQDN as environment variable in azure container plugin
- Add support for server naming and ssh key in azure vm plugin
- Add cost query to cloudbroker plugin and to API endpoint

v1.9 (25 May 2021)
==================

- Fixing protocol id in azure vm plugin
- Fixing authentication check in the azure container and vm plugins
- Refactor floating ip handling in nova plugin
- Update nova client library to latest version in nova plugin

v1.8 (17 Aug 2020)
==================

- Add Azure ACI (container) plugin
- Remove OCCI plugin

v1.7 (30 Apr 2020)
==================

- Add Azure plugin
- Upgrade to Python 3
- Add reporting multiple node addresses
- Add -f parameter for downscale to select node
- Add ApplicationCredential auth type handling to nova
- Add region selection to nova
- Add downscale by any ip of the node

v1.6 (05 Apr 2019)
==================

- New REST methods: notify, scaleto
- Export ip addresses by occopus-maintain
- Select youngest instance at downscaling when unspecified
- Added FCM event logging and notification
- Add cmd() and getprivip() methods in cloud-init resolution
- Add tagging for ec2 clouds
- Fixes in cloudsigma plugin (start server timeout, error code, ip retrieval)
- Fixes in docker plugin (version, dependencies, local image source support)
- Fixes in ec2 plugin (boto v2.48.0. with dependencies)


v1.5 (23 May 2017)
==================

- Reimplemented cloudbroker plugin: handle instances, not jobs
- Remove cloud-broker node resolver (replaced by cloud-init)
- Add multiuser support in handling redis server
- Improve error handling and logging in cloudsigma, ec2 and occi plugins
- Improve nova plugin to handle interruption
- Add infra and node name syntax checking
- Add new Occopus installer script
- Improve parallel node creation


v1.4 (27 March 2017)
====================

- Improve node handling in cloudsigma plugin
- Improve floating ip handling in nova plugin
- Precise syntax error reporting for descriptors
- Unique VM name for nodes as default
- Introduce user defined VM name templates
- Improve error/exception handling and reporting
- Fix logging and evaluation in schema checker
- Fix calculating default scaling min,max
- Restructure health-check reporting
- Deprecate 'network_mode' attribute in docker plugin
- Introduce attach and detach functions in rest
- Compatible REST and cmd-line functions


v1.3 (09 January 2017)
======================

- New Puppet config-manager plugin: server-free, called "puppet_solo"
- Remove external redis config for occopus-import command
- Remove attribute dependency from plugins
- Reimplement floating_ip handling in nova plugin
- Fix bug in filtering
- New tutorial for puppet_solo plugin
- New tutorial to introduce autoscaling with prometheus


v1.2 (11 August 2016)
=====================

- Support for keystone v3 password-based authentication in Nova plugin
- Infrastructure dynamic reconfiguration
- More logs in occi plugin
- Small fixes


v1.1 (5 June 2016)
==================

- New CloudSigma resource-handler plugin
- New tutorials for CloudSigma
- New getipall() method for cloud-init
- Fix yaml_import in node definition
- Bugfixes in plugins: occi, docker, nova


v1.0 (6 April 2016)
===================

- Restructure node definition format
- Introduce schema checking
- Mixed config-manager support
- Refactor plugin names
- Reorganise config and authentication
- New authenticator selection mechanism
- New filtering mechanism in node description
- Simplification of health_check
- Introduce getip() in context templates
- Update occopus commands
- Introduce occopus-maintain command for maintenance
- Introduce occopus-scale command for scaling
- Support for multi infrastructure handling
- Refactor occopus-import command parameters


v0.3.0 (15 Jan 2016)
====================

- introduce periodical service health checking
- new service health check mechanism: database check
- new service health check mechanism: port check
- add timeout for service unavailability
- improved nova plugin: voms based authorization
- new plugin: handling docker cluster
- new plugin: occi cloud interface for EGI FedClouds
- tutorials to demonstrate chef, docker and occi plugins
- node definition 'synch_strategy' keyword renamed to 'service_health_check'


v0.2.1 (10 Nov 2015)
====================

Improved EC2 handling:

- support for security group, subnet and keypairs in EC2 plugin
- two ec2 tutorials updated


v0.2.0 (4 Nov 2015)
===================

- multi-cloud support
- basic command line utils and REST interface
- support for cloud interfaces: EC2, NOVA, CloudBroker
- support for configuration manager: Chef
- initial version of error detection and recovery
- manual scaling through REST API
- tutorials for EC2, NOVA and CloudBroker
