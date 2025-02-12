.. _tutorial-resource-plugins:

.. figure:: images/logo/occo_licence.png
    :align: right

Resource plugins
================

|

In this section, simple examples will be shown. The examples will focus on introducing the different resource types and will have two categories:

 - The helloworld examples will only show how a single node can be created and how very simple contextualisation information (e.g. message string) can be passed to a virtual machine (VM)
 - The ping examples will focus on to introduce how dependency can be created and how can connection between two nodes can be built up by passing the ip of a node to another.

Please, note that the following examples require a properly configured Occopus, therefore we suggest to continue this section if you already followed the instructions written in the :ref:`Installation <installation>` section.

.. _ec2-helloworld:

EC2-Helloworld
~~~~~~~~~~~~~~
This tutorial builds an infrastructure containing a single node. The node will receive information (i.e. a message string) through contextualisation. The node will store this information in ``/tmp`` directory.

**Features**

In this example, the following feature(s) will be demonstrated:

 - creating a node with basic contextualisation
 - using the ec2 resource handler

**Prerequisites**

 - accessing a cloud through EC2 interface (access key, secret key, endpoint, regionname)
 - target cloud contains a base OS image with cloud-init support (image id, instance type)

**Download**

You can download the example as `tutorial.examples.ec2-helloworld <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/ec2-helloworld.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. For ``ec2_helloworld_node`` set the followings in its ``resource`` section:

   - ``endpoint`` is an url of an EC2 interface of a cloud (e.g. `https://ec2.eu-west-1.amazonaws.com`).
   - ``regionname`` is the region name within an EC2 cloud (e.g. `eu-west-1`).
   - ``image_id`` is the image id (e.g. `ami-12345678`) on your EC2 cloud. Select an image containing a base os installation with cloud-init support!
   - ``instance_type`` is the instance type (e.g. `m1.small`) of your VM to be instantiated.
   - ``key_name``  optionally specifies the keypair (e.g. `my_ssh_keypair`) to be deployed on your VM.
   - ``security_group`` optionally specifies security settings (you can define multiple security groups in the form of a list, e.g. `sg-93d46bf7`) of your VM.
   - ``subnet_id`` optionally specifies subnet identifier (e.g. `subnet-644e1e13`) to be attached to the VM.

   .. important::

      You can get help on collecting identifiers for the resources section at :ref:`this page <collect_amazon>` ! Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:ec2_helloworld_node':
         -
             resource:
                 type: ec2
                 endpoint: replace_with_endpoint_of_ec2_interface_of_your_cloud
                 regionname: replace_with_regionname_of_your_ec2_interface
                 image_id: replace_with_id_of_your_image_on_your_target_cloud
                 instance_type: replace_with_instance_type_of_your_image_on_your_target_cloud
                 key_name: replace_with_key_name_on_your_target_cloud
                 security_group_ids:
                     - replace_with_security_group_id1_on_your_target_cloud
                     - replace_with_security_group_id2_on_your_target_cloud
                 subnet_id: replace_with_subnet_id_on_your_target_cloud

#. Make sure your authentication information is set correctly in your authentication file. You must set your access key and secret key in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``ec2_helloworld_node`` node into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-ec2-helloworld.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of nodes/ip addresses:
      helloworld:
          192.168.xxx.xxx (3116eaf5-89e7-405f-ab94-9550ba1d0a7c)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code:: bash

      ssh ...
      # cat /tmp/helloworld.txt
      Hello World! I have been created by Occopus

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

EC2-Ping
~~~~~~~~
This tutorial builds an infrastructure containing two nodes. The ping-sender node will ping the ping-receiver node. The sender node will store the outcome of ping in ``/tmp`` directory.

**Features**

 - creating two nodes with dependencies (i.e. ordering of deployment)
 - querying a node's ip address and passing the address to another
 - using the ec2 resource handler

**Prerequisites**

 - accessing a cloud through EC2 interface (access key, secret key, endpoint, regionname)
 - target cloud contains a base OS image with cloud-init support (image id, instance type)

**Download**

You can download the example as `tutorial.examples.ec2-ping <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/ec2-ping.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. Both, for ``ec2_ping_receiver_node`` and for ``ec2_ping_sender_node`` set the followings in their ``resource`` section:

   - ``endpoint`` is an url of an EC2 interface of a cloud (e.g. `https://ec2.eu-west-1.amazonaws.com`).
   - ``regionname`` is the region name within an EC2 cloud (e.g. `eu-west-1`).
   - ``image_id`` is the image id (e.g. `ami-12345678`) on your EC2 cloud. Select an image containing a base os installation with cloud-init support!
   - ``instance_type`` is the instance type (e.g. `m1.small`) of your VM to be instantiated.
   - ``key_name``  optionally specifies the keypair (e.g. `my_ssh_keypair`) to be deployed on your VM.
   - ``security_group`` optionally specifies security settings (you can define multiple security groups in the form of a list, e.g. `sg-93d46bf7`) of your VM.
   - ``subnet_id`` optionally specifies subnet identifier (e.g. `subnet-644e1e13`) to be attached to the VM.

   .. important::

            You can get help on collecting identifiers for the resources section at :ref:`this page <collect_amazon>` ! Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:ec2_ping_receiver_node':
         -
             resource:
                 type: ec2
                 endpoint: replace_with_endpoint_of_ec2_interface_of_your_cloud
                 regionname: replace_with_regionname_of_your_ec2_interface
                 image_id: replace_with_id_of_your_image_on_your_target_cloud
                 instance_type: replace_with_instance_type_of_your_image_on_your_target_cloud
                 key_name: replace_with_key_name_on_your_target_cloud
                 security_group_ids:
                     -
                         replace_with_security_group_id1_on_your_target_cloud
                     -
                         replace_with_security_group_id2_on_your_target_cloud
                 subnet_id: replace_with_subnet_id_on_your_target_cloud
             ...
     'node_def:ec2_ping_sender_node':
         -
             resource:
                 type: ec2
                 endpoint: replace_with_endpoint_of_ec2_interface_of_your_cloud
                 regionname: replace_with_regionname_of_your_ec2_interface
                 image_id: replace_with_id_of_your_image_on_your_target_cloud
                 instance_type: replace_with_instance_type_of_your_image_on_your_target_cloud
                 key_name: replace_with_key_name_on_your_target_cloud
                 security_group_ids:
                     -
                         replace_with_security_group_id1_on_your_target_cloud
                     -
                         replace_with_security_group_id2_on_your_target_cloud
                 subnet_id: replace_with_subnet_id_on_your_target_cloud
             ...

#. Make sure your authentication information is set correctly in your authentication file. You must set your access key and secret key in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``ec2_ping_receiver_node`` and ``ec2_ping_sender_node`` nodes into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-ec2-ping.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of ip addresses:
      ping-receiver:
          192.168.xxx.xxx (f639a4ad-e9cb-478d-8208-9700415b95a4)
      ping-sender:
          192.168.yyy.yyy (99bdeb76-2295-4be7-8f14-969ab9d222b8)

      30f566d1-9945-42be-b603-795d604b362f

#. Check the result on your virtual machine.

   .. code:: bash

      ssh ...
      # cat /tmp/message.txt
      Hello World! I am the sender node created by Occopus.
      # cat /tmp/ping-result.txt
      PING 192.168.xxx.xxx (192.168.xxx.xxx) 56(84) bytes of data.
      64 bytes from 192.168.xxx.xxx: icmp_seq=1 ttl=64 time=2.74 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=2 ttl=64 time=0.793 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=3 ttl=64 time=0.865 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=4 ttl=64 time=0.882 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=5 ttl=64 time=0.786 ms

      --- 192.168.xxx.xxx ping statistics ---
      5 packets transmitted, 5 received, 0% packet loss, time 4003ms
      rtt min/avg/max/mdev = 0.786/1.215/2.749/0.767 ms

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 30f566d1-9945-42be-b603-795d604b362f

Nova-Helloworld
~~~~~~~~~~~~~~~
This tutorial builds an infrastructure containing a single node. The node will receive information (i.e. a message string) through contextualisation. The node will store this information in ``/tmp`` directory.

**Features**

 - creating a node with basic contextualisation
 - using the nova resource handler

**Prerequisites**

 - accessing an OpenStack cloud through its Nova interface (username/pasword or X.509 VOMS proxy, endpoint, tenant_name or project_id and user_domain_name)
 - id of network to be associated to the virtual machine (network_id)
 - security groups to be associated to the virtual machine (security_groups)
 - name of keypair on the target cloud to be associated with the vm (key_name)
 - target cloud contains a base OS image with cloud-init support (image_id, flavor_name)
 - optionally, name of floating ip pool from which ip should be taken for the vm (floating_ip_pool)

**Download**

You can download the example as `tutorial.examples.nova-helloworld <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/nova-helloworld.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. For ``nova_helloworld_node`` set the followings in its ``resource`` section:

   - ``endpoint`` must point to the endpoint (url) of your target Nova cloud.
   - ``project_id`` is the id of project you would like to use on your target Nova cloud.
   - ``user_domain_name`` is the user domain name you would like to use on your target Nova cloud.
   - ``image_id`` is the image id on your Nova cloud. Select an image containing a base os installation with cloud-init support!
   - ``flavor_name`` is the name of flavor to be instantiated on your Nova cloud.
   - ``server_name`` optionally defines the hostname of VM (e.g.:"helloworld").
   - ``key_name`` optionally sets the name of the keypair to be associated to the instance. Keypair name must be defined on the target nova cloud before launching the VM.
   - ``security_groups`` optionally specifies security settings (you can define multiple security groups in the form of a list) for your VM.
   - ``floating_ip`` optionally allocates new floating IP address to the VM if set to any value.
   - ``floating_ip_pool`` optionally specifies the name of pool from which the floating ip must be selected.

   .. important::

      You can get help on collecting identifiers for the resources section at :ref:`this page <collect_openstack_horizon>` ! Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:nova_helloworld_node':
         -
             resource:
                 type: nova
                 endpoint: replace_with_endpoint_of_nova_interface_of_your_cloud
                 project_id: replace_with_projectid_to_use
                 user_domain_name: Default
                 image_id: replace_with_id_of_your_image_on_your_target_cloud
                 network_id: replace_with_id_of_network_on_your_target_cloud
                 flavor_name: replace_with_id_of_the_flavor_on_your_target_cloud
                 server_name: myhelloworld
                 key_name: replace_with_name_of_keypair_or_remove
                 security_groups:
                     -
                         replace_with_security_group_to_add_or_remove_section
                 floating_ip: add_yes_if_you_need_floating_ip_or_remove
                 floating_ip_pool: replace_with_name_of_floating_ip_pool_or_remove

#. Make sure your authentication information is set correctly in your authentication file. You must set your username/password or in case of x509 voms authentication the path of your VOMS proxy in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``nova_helloworld_node`` node into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-nova-helloworld.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of nodes/ip addresses:
      helloworld:
          aaa.bbb.ccc.ddd (3116eaf5-89e7-405f-ab94-9550ba1d0a7c)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code:: bash

      ssh ...
      # cat /tmp/helloworld.txt
      Hello World! I have been created by Occopus

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

Nova-Ping
~~~~~~~~~
This tutorial builds an infrastructure containing two nodes. The ping-sender node will
ping the ping-receiver node. The sender node will store the outcome of ping in ``/tmp`` directory.

**Features**

 - creating two nodes with dependencies (i.e. ordering of deployment)
 - querying a node's ip address and passing the address to another
 - using the nova resource handler

**Prerequisites**

 - accessing an OpenStack cloud through its Nova interface (username/pasword or X.509 VOMS proxy, endpoint, tenant_name or project_id and user_domain_name)
 - id of network to be associated to the virtual machine (network_id)
 - security groups to be associated to the virtual machine (security_groups)
 - name of keypair on the target cloud to be associated with the vm (key_name)
 - target cloud contains a base OS image with cloud-init support (image_id, flavor_name)
 - optionally, name of floating ip pool from which ip should be taken for the vm (floating_ip_pool)

**Download**

You can download the example as `tutorial.examples.nova-ping <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/nova-ping.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. Both, for ``nova_ping_receiver_node`` and for ``nova_ping_sender_node`` set the followings in their ``resource`` section:

   - ``endpoint`` must point to the endpoint (url) of your target Nova cloud.
   - ``project_id`` is the id of project you would like to use on your target Nova cloud.
   - ``user_domain_name`` is the user domain name you would like to use on your target Nova cloud.
   - ``image_id`` is the image id on your Nova cloud. Select an image containing a base os installation with cloud-init support!
   - ``flavor_name`` is the name of flavor to be instantiated on your Nova cloud.
   - ``server_name`` optionally defines the hostname of VM (e.g.:"helloworld").
   - ``key_name`` optionally sets the name of the keypair to be associated to the instance. Keypair name must be defined on the target nova cloud before launching the VM.
   - ``security_groups`` optionally specifies security settings (you can define multiple security groups in the form of a list) for your VM.
   - ``floating_ip`` optionally allocates new floating IP address to the VM if set to any value.
   - ``floating_ip_pool`` optionally specifies the name of pool from which the floating ip must be selected.

   .. important::

      You can get help on collecting identifiers for the resources section at :ref:`this page <collect_openstack_horizon>` ! Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:nova_ping_receiver_node':
	 -
	     resource:
                 type: nova
                 endpoint: replace_with_endpoint_of_nova_interface_of_your_cloud
                 project_id: replace_with_projectid_to_use
                 user_domain_name: Default
                 image_id: replace_with_id_of_your_image_on_your_target_cloud
                 network_id: replace_with_id_of_network_on_your_target_cloud
                 flavor_name: replace_with_id_of_the_flavor_on_your_target_cloud
                 server_name: mypingreceiver
                 key_name: replace_with_name_of_keypair_or_remove
                 security_groups:
                     -
                         replace_with_security_group_to_add_or_remove_section
                 floating_ip: add_yes_if_you_need_floating_ip_or_remove
                 floating_ip_pool: replace_with_name_of_floating_ip_pool_or_remove
             ...
     'node_def:nova_ping_sender_node':
	 -
	     resource:
                 type: nova
                 endpoint: replace_with_endpoint_of_nova_interface_of_your_cloud
                 project_id: replace_with_projectid_to_use
                 user_domain_name: Default
                 image_id: replace_with_id_of_your_image_on_your_target_cloud
                 network_id: replace_with_id_of_network_on_your_target_cloud
                 flavor_name: replace_with_id_of_the_flavor_on_your_target_cloud
                 server_name: mypingsender
                 key_name: replace_with_name_of_keypair_or_remove
                 security_groups:
                     -
                         replace_with_security_group_to_add_or_remove_section
                 floating_ip: add_yes_if_you_need_floating_ip_or_remove
                 floating_ip_pool: replace_with_name_of_floating_ip_pool_or_remove

#. Make sure your authentication information is set correctly in your authentication file. You must set your username/password or in case of x509 voms authentication the path of your VOMS proxy in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``nova_ping_receiver_node`` and ``nova_ping_sender_node`` nodes into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-nova-ping.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of ip addresses:
      ping-receiver:
          192.168.xxx.xxx (f639a4ad-e9cb-478d-8208-9700415b95a4)
      ping-sender:
          192.168.yyy.yyy (99bdeb76-2295-4be7-8f14-969ab9d222b8)

      30f566d1-9945-42be-b603-795d604b362f

#. Check the result on your virtual machine.

   .. code:: bash

      ssh ...
      # cat /tmp/message.txt
      Hello World! I am the sender node created by Occopus.
      # cat /tmp/ping-result.txt
      PING 192.168.xxx.xxx (192.168.xxx.xxx) 56(84) bytes of data.
      64 bytes from 192.168.xxx.xxx: icmp_seq=1 ttl=64 time=2.74 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=2 ttl=64 time=0.793 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=3 ttl=64 time=0.865 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=4 ttl=64 time=0.882 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=5 ttl=64 time=0.786 ms

      --- 192.168.xxx.xxx ping statistics ---
      5 packets transmitted, 5 received, 0% packet loss, time 4003ms
      rtt min/avg/max/mdev = 0.786/1.215/2.749/0.767 ms

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 30f566d1-9945-42be-b603-795d604b362f

Azure-Helloworld
~~~~~~~~~~~~~~~~

This tutorial builds an infrastructure containing a single node. The node will receive information (i.e. a message string) through contextualisation. The node will store this information in ``/tmp`` directory.

**Features**

- creating a node with basic contextualisation
- using the azure resource handler

**Prerequisites**

- accessing Microsoft Azure interface (Tenant ID, Client ID, Client Secret, Subscription ID)
- resource group name inside Azure
- location to use inside Azure
- virtual machine specifications (size, publisher, offer, sku and version)

**Download**

You can download the example as `tutorial.examples.azure-helloworld <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/azure-helloworld.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. For ``azure_helloworld_node`` set the followings in its ``resource`` section:

   - ``resource_group`` must contain the name of the resource group to allocate resources in.
   - ``location`` is the name of the location (region) to use.
   - ``vm_size`` is the size of the VM to allocate.
   - ``publisher`` is the name of the publisher of the image to use.
   - ``offer`` is the offer of the image to use.
   - ``sku`` is the sku of the image to use.
   - ``version`` is the version of the image to use.
   - ``username`` the name of the admin user to create.
   - ``password`` the password to set for thr admin user.
   - ``public_ip_needed`` optional, when set to True, a public IP is allocated for the resource.

   .. important::

      You can get help on collecting identifiers for the resources section at https://docs.microsoft.com/hu-hu/azure/developer/python/azure-sdk-authenticate. Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:azure_helloworld_node':
         -
           resource:
               type: azure_vm
               endpoint: https://management.azure.com
               resource_group: replace_with_resource_group_name
               location : replace_with_location
               vm_size: replace_with_vm_size
               publisher : replace_with_publisher_name
               offer : replace_with_offer
               sku : replace_with_sku
               version : replace_with_version
               username : replace_with_admin_username
               password : replace_with_admin_password
               # Optional - Existing VNet's name to use
               #vnet_name: replace_with_virtual_network_name
               # Optional - Existing NIC's name to use
               #nic_name: replace_with_nic_name
               # Optional - Subnet name
               #subnet_name: replace_with_subnet_name
               # Optional - Set to True if public IP is needed
               #public_ip_needed : True

#. Make sure your authentication information is set correctly in your authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``azure_helloworld_node`` node into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-azure-helloworld.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of nodes/ip addresses:
      helloworld:
          aaa.bbb.ccc.ddd (3116eaf5-89e7-405f-ab94-9550ba1d0a7c)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code:: bash

      ssh ...
      # cat /tmp/helloworld.txt
      Hello World! I have been created by Occopus

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

Azure-Ping
~~~~~~~~~~

This tutorial builds an infrastructure containing two nodes. The ping-sender node will
ping the ping-receiver node. The sender node will store the outcome of ping in ``/tmp`` directory.

**Features**

- creating two nodes with dependencies (i.e. ordering of deployment)
- querying a node's ip address and passing the address to another
- using the azure resource handler

**Prerequisites**

- accessing Microsoft Azure interface (Tenant ID, Client ID, Client Secret, Subscription ID)
- resource group name inside Azure
- location to use inside Azure
- virtual machine specifications (size, publisher, offer, sku and version)

**Download**

You can download the example as `tutorial.examples.azure-ping <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/azure-ping.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. Both, for ``azure_ping_receiver_node`` and for ``azure_ping_sender_node`` set the followings in their ``resource`` section:

   - ``resource_group`` must contain the name of the resource group to allocate resources in.
   - ``location`` is the name of the location (region) to use.
   - ``vm_size`` is the size of the VM to allocate.
   - ``publisher`` is the name of the publisher of the image to use.
   - ``offer`` is the offer of the image to use.
   - ``sku`` is the sku of the image to use.
   - ``version`` is the version of the image to use.
   - ``username`` the name of the admin user to create.
   - ``password`` the password to set for thr admin user.
   - ``public_ip_needed`` optional, when set to True, a public IP is allocated for the resource.

   .. important::

      You can get help on collecting identifiers for the resources section at https://docs.microsoft.com/hu-hu/azure/developer/python/azure-sdk-authenticate. Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:azure_ping_receiver_node':
	 -
	     resource:
             type: azure_vm
             endpoint: https://management.azure.com
             resource_group: replace_with_resource_group_name
             location : replace_with_location
             vm_size: replace_with_vm_size
             publisher : replace_with_publisher_name
             offer : replace_with_offer
             sku : replace_with_sku
             version : replace_with_version
             username : replace_with_admin_username
             password : replace_with_admin_password
             # Optional - Existing VNet's name to use
             #vnet_name: replace_with_virtual_network_name
             # Optional - Existing NIC's name to use
             #nic_name: replace_with_nic_name
             # Optional - Subnet name
             #subnet_name: replace_with_subnet_name
             # Optional - Set to True if public IP is needed
             #public_ip_needed : True
           ...
     'node_def:azure_ping_sender_node':
	 -
	     resource:
             type: azure_vm
             endpoint: https://management.azure.com
             resource_group: replace_with_resource_group_name
             location : replace_with_location
             vm_size: replace_with_vm_size
             publisher : replace_with_publisher_name
             offer : replace_with_offer
             sku : replace_with_sku
             version : replace_with_version
             username : replace_with_admin_username
             password : replace_with_admin_password
             # Optional - Existing VNet's name to use
             #vnet_name: replace_with_virtual_network_name
             # Optional - Existing NIC's name to use
             #nic_name: replace_with_nic_name
             # Optional - Subnet name
             #subnet_name: replace_with_subnet_name
             # Optional - Set to True if public IP is needed
             #public_ip_needed : True

#. Make sure your authentication information is set correctly in your authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``azure_ping_receiver_node`` and ``azure_ping_sender_node`` nodes into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-azure-ping.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of ip addresses:
      ping-receiver:
          192.168.xxx.xxx (f639a4ad-e9cb-478d-8208-9700415b95a4)
      ping-sender:
          192.168.yyy.yyy (99bdeb76-2295-4be7-8f14-969ab9d222b8)

      30f566d1-9945-42be-b603-795d604b362f

#. Check the result on your virtual machine.

   .. code:: bash

      ssh ...
      # cat /tmp/message.txt
      Hello World! I am the sender node created by Occopus.
      # cat /tmp/ping-result.txt
      PING 192.168.xxx.xxx (192.168.xxx.xxx) 56(84) bytes of data.
      64 bytes from 192.168.xxx.xxx: icmp_seq=1 ttl=64 time=2.74 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=2 ttl=64 time=0.793 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=3 ttl=64 time=0.865 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=4 ttl=64 time=0.882 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=5 ttl=64 time=0.786 ms

      --- 192.168.xxx.xxx ping statistics ---
      5 packets transmitted, 5 received, 0% packet loss, time 4003ms
      rtt min/avg/max/mdev = 0.786/1.215/2.749/0.767 ms

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 30f566d1-9945-42be-b603-795d604b362f

Azure-ACI-Helloworld
~~~~~~~~~~~~~~~~~~~~

This tutorial builds an infrastructure containing a single node. The node will receive information (i.e. a message string) through contextualisation. The node will store this information in ``/tmp`` directory.

**Features**

- creating a node with basic contextualisation
- using the azure_aci resource handler

**Prerequisites**

- accessing Microsoft Azure interface (Tenant ID, Client ID, Client Secret, Subscription ID)
- resource group name inside Azure
- location to use inside Azure

**Download**

You can download the example as `tutorial.examples.azure-aci-helloworld <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/azure-aci-helloworld.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. For ``azure_aci_helloworld_node`` set the followings in its ``resource`` section:

   - ``resource_group`` must contain the name of the resource group to allocate resources in.
   - ``location`` is the name of the location (region) to use.
   - ``memory`` must contain the amount of memory to allocate for the container in GB (e.g. 1).
   - ``cpu_cores`` must contain the amount of CPU cures to allocate for the container in GB (e.g. 1).

   .. important::

      You can get help on collecting identifiers for the resources section at https://docs.microsoft.com/hu-hu/azure/developer/python/azure-sdk-authenticate. Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:azure_aci_helloworld_node':
         -
            resource:
               type: azure_aci
               endpoint: https://management.azure.com
               resource_group: replace_with_resource_group_name
               location: replace_with_location
               memory: replace_with_memory
               cpu_cores: replace_with_cpu_cores
               os_type: linux
               image: alpine
               network_type: Private
               ports:
                  - 8080
            contextualisation:
               type: docker
               env: ["message={{variables.message}}"]
               command: ["sh", "-c", "echo \"$message\" > /tmp/message.txt; while true; do sleep 1000; done"]
            health_check:
               ping: False

#. Make sure your authentication information is set correctly in your authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``azure_aci_helloworld_node`` node into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-azure-aci-helloworld.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of nodes/ip addresses:
      helloworld:
          aaa.bbb.ccc.ddd (3116eaf5-89e7-405f-ab94-9550ba1d0a7c)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on the Azure portal. When you open the Azure portal, you can find your container instance inside all resources. From there, you can navigate to the connect panel of the container, and can use /bin/sh to gain root shell access inside the running container:

   .. code:: bash

      # cat /tmp/helloworld.txt
      Hello World! I have been created by Occopus

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

Azure-ACI-Nginx
~~~~~~~~~~~~~~~

This tutorial builds an infrastructure containing two nodes. The nginx-client node will
fetch the HTML content served by the nginx-server node, and store the outcome in the ``/tmp`` directory. The nginx-server node uses the
Alpine Linux-based Nginx image from the Docker hub, whereas the nginx-client node is run on top of a stock Alpine Linux image, also from
the Docker hub.

**Features**

- creating two nodes with dependencies (i.e. ordering of deployment)
- querying a node's ip address and passing the address to another
- using the azure_aci resource handler

**Prerequisites**

- accessing Microsoft Azure interface (Tenant ID, Client ID, Client Secret, Subscription ID)
- resource group name inside Azure
- location to use inside Azure

**Download**

You can download the example as `tutorial.examples.azure-aci-nginx <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/azure-aci-nginx.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. Both, for ``azure_aci_nginx_node`` and for ``azure_aci_client_node`` set the followings in their ``resource`` section:

   - ``resource_group`` must contain the name of the resource group to allocate resources in.
   - ``location`` is the name of the location (region) to use.
   - ``memory`` must contain the amount of memory to allocate for the container in GB (e.g. 1).
   - ``cpu_cores`` must contain the amount of CPU cures to allocate for the container in GB (e.g. 1).

   .. important::

      You can get help on collecting identifiers for the resources section at https://docs.microsoft.com/hu-hu/azure/developer/python/azure-sdk-authenticate. Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:azure_aci_nginx_node':
	 -
	     resource:
             type: azure_aci
             endpoint: https://management.azure.com
               resource_group: replace_with_resource_group_name
               location: replace_with_location
               memory: replace_with_memory
               cpu_cores: replace_with_cpu_cores
               os_type: linux
               image: nginx:alpine
               network_type: Public
               ports:
                  - 80
           ...
     'node_def:azure_aci_client_node':
	 -
	     resource:
             type: azure_aci
             endpoint: https://management.azure.com
               resource_group: replace_with_resource_group_name
               location: replace_with_location
               memory: replace_with_memory
               cpu_cores: replace_with_cpu_cores
               os_type: linux
               image: alpine
               network_type: Public
               ports:
                  - 8080

#. Make sure your authentication information is set correctly in your authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``azure_aci_nginx_node`` and ``azure_aci_client_node`` nodes into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-azure-aci-nginx.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of ip addresses:
      nginx-server:
          192.168.xxx.xxx (f639a4ad-e9cb-478d-8208-9700415b95a4)
      nginx-client:
          192.168.yyy.yyy (99bdeb76-2295-4be7-8f14-969ab9d222b8)

      30f566d1-9945-42be-b603-795d604b362f

#. Check the result on the Azure portal. When you open the Azure portal, you can find your container instances inside all resources.
From there, you can navigate to the connect panel of the nginx-client container, and can use /bin/sh to gain root shell access inside the running container:

   .. code:: bash

      / # ls -1 /tmp
      message.txt
      nginx_content.html
      / # cat /tmp/message.txt
      Hello World! I am the client node created by Occopus.

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 30f566d1-9945-42be-b603-795d604b362f

Docker-Helloworld
~~~~~~~~~~~~~~~~~
This tutorial builds an infrastructure containing a single node implemented by a Docker container. The node will receive information (i.e. a message string) through contextualisation. The node will store this information in ``/root/message.txt`` file.

**Features**

 - creating a node with basic contextualisation
 - using the docker resource handler

**Prerequisites**

 - accessing a Docker host or a Swarm cluster (endpoint)
 - having a docker image to be instantiated or using the one predefined in this example (origin, image)
 - command to be executed on the image and the required environment variables or using the one predefined in this example (command, environment variables)

 .. important::

    Encrypted connection is not supported yet!

**Download**

You can download the example as `tutorial.examples.docker-helloworld <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/docker-helloworld.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. For ``docker_helloworld_node`` set the followings in its ``resource`` section:

   - ``endpoint`` is the endpoint of your docker cluster (e.g. `tcp://1.2.3.4:2375` or `unix://var/run/docker.sock`).

   .. important::

      You can get help on collecting identifiers for the resources section at :ref:`this page <collect_amazon>` ! Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:docker_helloworld_node':
	 -
	     resource:
		 type: docker
		 endpoint: replace_with_your_docker_endpoint
		 origin: https://s3.lpds.sztaki.hu/docker/busybox_helloworld.tar
		 image: busybox_helloworld
		 tag: latest

#. Make sure your authentication information is set correctly in your authentication file. The docker plugin in Occopus does not apply authentication, however a dummy authentication block is needed. The instructions for setting the authentication properly is described at the :ref:`authentication page <authentication>`. There you can download a default authentication file containing the docker section already.

#. Load the node definition for ``docker_helloworld_node`` node into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-docker-helloworld.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of nodes/ip addresses:
      helloworld:
          192.168.xxx.xxx (3116eaf5-89e7-405f-ab94-9550ba1d0a7c)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code:: bash

        # docker ps
        CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS               NAMES
        13bb8c94b5f4        busybox_helloworld:latest   "sh -c /root/start.sh"   3 seconds ago       Up 2 seconds                            admiring_joliot

        # docker exec -it 13bb8c94b5f4 cat /root/message.txt
        Hello World! I have been created by Occopus.

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

Docker-Ping
~~~~~~~~~~~
This tutorial builds an infrastructure containing a two nodes implemented by Docker containers. The ping-sender node will ping the ping-receiver node to demonstrate the connection between the two nodes. The sender node will store the outcome of ping in ``/root/ping-result.txt`` file.

**Features**

 - creating two nodes with dependencies (i.e ordering or deployment)
 - querying a node's ip address and passing the address to another
 - using the docker resource handler

**Prerequisites**

 - accessing a Docker host or a Swarm cluster (endpoint)
 - having a docker image to be instantiated or using the one predefined in this example (origin, image)
 - command to be executed on the image and the required environment variables or using the one predefined in this example (command, env)

 .. important::

    Encrypted connection is not supported yet!

**Download**

You can download the example as `tutorial.examples.docker-ping <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/docker-ping.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. Both, for ``docker_ping_receiver_node`` and for ``docker_ping_sender_node`` set the followings in their ``resource`` section:

   - ``endpoint`` is the endpoint of your docker cluster (e.g. `tcp://1.2.3.4:2375` or `unix://var/run/docker.sock`).

   .. important::

      You can get help on collecting identifiers for the resources section at :ref:`this page <collect_amazon>` ! Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:docker_ping_receiver_node':
       -
	     resource:
		 type: docker
		 endpoint: replace_with_your_docker_endpoint
		 origin: https://s3.lpds.sztaki.hu/docker/busybox_helloworld.tar
		 image: busybox_helloworld
		 tag: latest
             ...
     'node_def:docker_ping_sender_node':
	 -
	     resource:
		 type: docker
		 endpoint: replace_with_your_docker_endpoint
		 origin: https://s3.lpds.sztaki.hu/docker/busybox_ping.tar
		 image: busybox_ping
		 tag: latest

#. Make sure your authentication information is set correctly in your authentication file. The docker plugin in Occopus does not apply authentication, however a dummy authentication block is needed. Instructions for setting the authentication properly is described at the :ref:`authentication page <authentication>`. There you can download a default authentication file containing the docker section already.

#. Load the node definition for ``docker_ping_receiver_node`` and ``docker_ping_sender_node`` nodes into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-docker-ping.yaml

#. After successful finish, the nodes with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of nodes/ip addresses:
      ping-receiver:
        10.0.0.2 (552fe5b2-23a6-4c12-a4e2-077521027832)
      ping-sender:
        10.0.0.3 (eabc8d2f-401b-40cf-9386-4739ecd99fbd)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code:: bash

        # ssh ...
        # docker ps
        CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS               NAMES
        4e83c45e8378        busybox_ping:latest         "sh -c /root/start.sh"   16 seconds ago      Up 15 seconds                           romantic_brown
        10b27bc4d978        busybox_helloworld:latest   "sh -c /root/start.sh"   17 seconds ago      Up 16 seconds                           jovial_mayer

        # docker exec -it 4e83c45e8378 cat /root/ping-result.txt
        PING 172.17.0.2 (172.17.0.2): 56 data bytes
        64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.195 ms
        64 bytes from 172.17.0.2: seq=1 ttl=64 time=0.105 ms
        64 bytes from 172.17.0.2: seq=2 ttl=64 time=0.124 ms
        64 bytes from 172.17.0.2: seq=3 ttl=64 time=0.095 ms
        64 bytes from 172.17.0.2: seq=4 ttl=64 time=0.085 ms

        --- 172.17.0.2 ping statistics ---
        5 packets transmitted, 5 packets received, 0% packet loss
        round-trip min/avg/max = 0.085/0.120/0.195 ms

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

CloudSigma-Helloworld
~~~~~~~~~~~~~~~~~~~~~
This tutorial builds an infrastructure containing a single node. The node will receive information (i.e. a message string) through contextualisation. The node will store this information in ``/tmp`` directory.

**Features**

 - creating a node with basic contextualisation
 - using the cloudsigma resource handler

**Prerequisites**

 - accessing a cloud through CloudSigma interface (email, password, endpoint)
 - target cloud contains a base OS image with cloud-init support (library drive identifier)

**Download**

You can download the example as `tutorial.examples.cloudsigma-helloworld <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/cloudsigma-helloworld.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. For ``cloudsigma_helloworld_node`` set the followings in its ``resource`` section:

   - ``endpoint`` is an url of a CloudSigma interface of a cloud (e.g. `https://zrh.cloudsigma.com/api/2.0`).
   - ``libdrive_id`` is the image id (e.g. `40aa6ce2-5198-4e6b-b569-1e5e9fbaf488`) on your CloudSigma cloud. Select an image containing a base os installation with cloud-init support!
   - ``cpu`` is the speed of CPU (e.g. `2000`) in terms of MHz of your VM to be instantiated.
   - ``mem`` is the amount of RAM (e.g. `1073741824`) in terms of bytes to be allocated for your VM.
   - ``vnc_password`` set the password for your VNC session.
   - ``pubkeys``  optionally specifies the keypairs (e.g. `f80c3ffb-3ab5-461e-ad13-4b253da122bd`) to be assigned to your VM.
   - ``firewall_policy`` optionally specifies network policies (you can define multiple security groups in the form of a list, e.g. `8cd00652-c5c8-4af0-bdd6-0e5204c66dc5`) of your VM.

   .. important::

      You can get help on collecting identifiers for the resources section at :ref:`this page <collect_amazon>` ! Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:cloudsigma_helloworld_node':
         -
            resource:
                type: cloudsigma
                endpoint: replace_with_endpoint_of_cloudsigma_interface_of_your_cloud
                libdrive_id: replace_with_id_of_your_library_drive_on_your_target_cloud
                description:
                    cpu: 2000
                    mem: 1073741824
                    vnc_password: secret
                    pubkeys:
                        -
                            replace_with_id_of_your_pubkey_on_your_target_cloud
                    nics:
                        -
                            firewall_policy: replace_with_id_of_your_network_policy_on_your_target_cloud
                            ip_v4_conf:
                                conf: dhcp

#. Make sure your authentication information is set correctly in your authentication file. You must set your email and password in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``cloudsigma_helloworld_node`` node into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-cloudsigma-helloworld.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of nodes/ip addresses:
      helloworld:
          192.168.xxx.xxx (3116eaf5-89e7-405f-ab94-9550ba1d0a7c)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code:: bash

      ssh ...
      # cat /tmp/helloworld.txt
      Hello World! I have been created by Occopus

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

CloudSigma-Ping
~~~~~~~~~~~~~~~
This tutorial builds an infrastructure containing two nodes. The ping-sender node will ping the ping-receiver node. The sender node will store the outcome of ping in ``/tmp`` directory.

**Features**

 - creating two nodes with dependencies (i.e. ordering of deployment)
 - querying a node's ip address and passing the address to another
 - using the cloudsigma resource handler

**Prerequisites**

 - accessing a cloud through CloudSigma interface (email, password, endpoint)
 - target cloud contains a base OS image with cloud-init support (library drive identifier)

**Download**

You can download the example as `tutorial.examples.cloudsigma-ping <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/cloudsigma-ping.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. Both, for ``cloudsigma_ping_receiver_node`` and for ``cloudsigma_ping_sender_node`` set the followings in their ``resource`` section:

   - ``endpoint`` is an url of a CloudSigma interface of a cloud (e.g. `https://zrh.cloudsigma.com/api/2.0`).
   - ``libdrive_id`` is the image id (e.g. `40aa6ce2-5198-4e6b-b569-1e5e9fbaf488`) on your CloudSigma cloud. Select an image containing a base os installation with cloud-init support!
   - ``cpu`` is the speed of CPU (e.g. `2000` for 2GHz) in terms of MHz of your VM to be instantiated.
   - ``mem`` is the amount of RAM (e.g. `1073741824`) in terms of bytes to be allocated for your VM.
   - ``vnc_password`` set the password for your VNC session.
   - ``pubkeys``  optionally specifies the keypairs (e.g. `f80c3ffb-3ab5-461e-ad13-4b253da122bd`) to be assigned to your VM.
   - ``firewall_policy`` optionally specifies network policies (you can define multiple security groups in the form of a list, e.g. `8cd00652-c5c8-4af0-bdd6-0e5204c66dc5`) of your VM.

   .. important::

      You can get help on collecting identifiers for the resources section at :ref:`this page <collect_amazon>` ! Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

     'node_def:cloudsigma_ping_receiver_node':
         -
            resource:
                name: my_cloudsigma_cloud
                type: cloudsigma
                endpoint: replace_with_endpoint_of_cloudsigma_interface_of_your_cloud
                libdrive_id: replace_with_id_of_your_library_drive_on_your_target_cloud
                description:
                    cpu: 2000
                    mem: 1073741824
                    vnc_password: secret
                    pubkeys:
                        -
                            replace_with_id_of_your_pubkey_on_your_target_cloud
                    nics:
                        -
                            firewall_policy: replace_with_id_of_your_network_policy_on_your_target_cloud
                            ip_v4_conf:
                                conf: dhcp
                                ip: null
                            runtime:
                                interface_type: public
             ...
     'node_def:cloudsigma_ping_sender_node':
         -
            resource:
                name: my_cloudsigma_cloud
                type: cloudsigma
                endpoint: replace_with_endpoint_of_cloudsigma_interface_of_your_cloud
                libdrive_id: replace_with_id_of_your_library_drive_on_your_target_cloud
                description:
                    cpu: 2000
                    mem: 1073741824
                    vnc_password: secret
                    pubkeys:
                        -
                            replace_with_id_of_your_pubkey_on_your_target_cloud
                    nics:
                        -
                            firewall_policy: replace_with_id_of_your_network_policy_on_your_target_cloud
                            ip_v4_conf:
                                conf: dhcp
                                ip: null
                            runtime:
                                interface_type: public
             ...

#. Make sure your authentication information is set correctly in your authentication file. You must set your email and password in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``cloudsigma_ping_receiver_node`` and ``cloudsigma_ping_sender_node`` nodes into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-cloudsigma-ping.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of ip addresses:
      ping-receiver:
          192.168.xxx.xxx (f639a4ad-e9cb-478d-8208-9700415b95a4)
      ping-sender:
          192.168.yyy.yyy (99bdeb76-2295-4be7-8f14-969ab9d222b8)

      30f566d1-9945-42be-b603-795d604b362f

#. Check the result on your virtual machine.

   .. code:: bash

      ssh ...
      # cat /tmp/message.txt
      Hello World! I am the sender node created by Occopus.
      # cat /tmp/ping-result.txt
      PING 192.168.xxx.xxx (192.168.xxx.xxx) 56(84) bytes of data.
      64 bytes from 192.168.xxx.xxx: icmp_seq=1 ttl=64 time=2.74 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=2 ttl=64 time=0.793 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=3 ttl=64 time=0.865 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=4 ttl=64 time=0.882 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=5 ttl=64 time=0.786 ms

      --- 192.168.xxx.xxx ping statistics ---
      5 packets transmitted, 5 received, 0% packet loss, time 4003ms
      rtt min/avg/max/mdev = 0.786/1.215/2.749/0.767 ms

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 30f566d1-9945-42be-b603-795d604b362f

CloudBroker-Helloworld
~~~~~~~~~~~~~~~~~~~~~~
This tutorial builds an infrastructure containing a single node. The node will receive information (i.e. a message string) through contextualisation. The node will store this information in ``/tmp`` directory.

**Features**

 - creating a node with basic contextualisation
 - using the cloudbroker resource handler

**Prerequisites**

 - accessing a CloudBroker Platform instance (URL, email and password)
 - Deployment, Instance type properly registered on the CloudBroker platform

**Download**

You can download the example as `tutorial.examples.cloudbroker-helloworld <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/cloudbroker-helloworld.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. For ``cloudbroker_helloworld_node`` set the followings in its ``resource`` section:

   - ``endpoint`` is the url of the CloudBroker REST API interface (e.g. `https://cola-prototype.cloudbroker.com`).
   - ``deployment_id`` is the id of a preregistered deployment in CloudBroker referring to a cloud, image, region, etc. Make sure the image contains a base os (preferably Ubuntu) installation with cloud-init support! The id is the UUID of the deployment which can be seen in the address bar of your browser when inspecting the details of the deployment.
   - ``instance_type_id`` is the id of a preregistered instance type in CloudBroker referring to the capacity of the virtual machine to be deployed. The id is the UUID of the instance type which can be seen in the address bar of your browser when inspecting the details of the instance type.
   - ``key_pair_id`` is the id of a preregistered ssh public key in CloudBroker which will be deployed on the virtual machine. The id is the UUID of the key pair which can be seen in the address bar of your browser when inspecting the details of the key pair.
   - ``opened_port`` is one or more ports to be opened to the world. This is a string containing numbers separated by comma.

   .. important::

      You can get help on collecting identifiers for the resources section at :ref:`this page <collect_amazon>` ! Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

      ...
      resource:
	type: cloudbroker
        endpoint: replace_with_endpoint_of_cloudbroker_interface
        description:
          deployment_id: replace_with_deployment_id
          instance_type_id: replace_with_instance_type_id
          key_pair_id: replace_with_keypair_id
          opened_port: replace_with_list_of_ports_separated_with_comma
      contextualisation:
      ...

#. Make sure your authentication information is set correctly in your authentication file. You must set your ``email`` and ``password`` in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``cloudbroker_helloworld_node`` node into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-cloudbroker-helloworld.yaml

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of nodes/ip addresses:
      helloworld:
        192.168.xxx.xxx (3116eaf5-89e7-405f-ab94-9550ba1d0a7c)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code:: bash

      ssh ...
      # cat /tmp/helloworld.txt
      Hello World! I have been created by Occopus

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

CloudBroker-Ping
~~~~~~~~~~~~~~~~
This tutorial sets up an infrastructure containing two nodes on the CloudBroker Platform. The ping-sender node will
ping the ping-receiver node. The node will store the outcome of ping in ``/tmp`` directory.

**Features**

 - creating two nodes with dependencies (i.e. ordering of deployment)
 - querying a node's ip address and passing the address to another
 - using the cloudbroker resource handler

**Prerequisites**

 - accessing a CloudBroker Platform instance (URL, username and password)
 - Software, Executabe, Resource, Region and Instance type properly registered on the CloudBroker platform

**Download**

You can download the example as `tutorial.examples.cloudbroker-ping <https://raw.githubusercontent.com/occopus/docs/devel/tutorials/cloudbroker-ping.tar.gz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. Both, for ``cloudbroker_ping_receiver_node`` and for ``cloudbroker_ping_sender_node`` set the followings in their ``resource`` section:

   - ``endpoint`` is the url of the CloudBroker REST API interface (e.g. `https://cola-prototype.cloudbroker.com`).
   - ``deployment_id`` is the id of a preregistered deployment in CloudBroker referring to a cloud, image, region, etc. Make sure the image contains a base os (preferably Ubuntu) installation with cloud-init support! The id is the UUID of the deployment which can be seen in the address bar of your browser when inspecting the details of the deployment.
   - ``instance_type_id`` is the id of a preregistered instance type in CloudBroker referring to the capacity of the virtual machine to be deployed. The id is the UUID of the instance type which can be seen in the address bar of your browser when inspecting the details of the instance type.
   - ``key_pair_id`` is the id of a preregistered ssh public key in CloudBroker which will be deployed on the virtual machine. The id is the UUID of the key pair which can be seen in the address bar of your browser when inspecting the details of the key pair.
   - ``opened_port`` is one or more ports to be opened to the world. This is a string containing numbers separated by comma.

   .. important::

      You can get help on collecting identifiers for the resources section at :ref:`this page <collect_amazon>` ! Alternatively, detailed explanation can be found at the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.

   .. code:: yaml

      'node_def:cloudbroker_ping_receiver_node':
        -
          resource:
            type: cloudbroker
            endpoint: replace_with_endpoint_of_cloudbroker_interface
            description:
              deployment_id: replace_with_deployment_id
              instance_type_id: replace_with_instance_type_id
              key_pair_id: replace_with_keypair_id
              opened_port: replace_with_list_of_ports_separated_with_comma
          contextualisation:
            type: cloudinit
            context_template: !yaml_import
              url: file://cloud_init_ping_receiver.yaml
      'node_def:cloudbroker_ping_sender_node':
        -
          resource:
            type: cloudbroker
            endpoint: replace_with_endpoint_of_cloudbroker_interface
            description:
              deployment_id: replace_with_deployment_id
              instance_type_id: replace_with_instance_type_id
              key_pair_id: replace_with_keypair_id
              opened_port: replace_with_list_of_ports_separated_with_comma
          contextualisation:
            type: cloudinit
            context_template: !yaml_import
              url: file://cloud_init_ping_sender.yaml

#. Make sure your authentication information is set correctly in your authentication file. You must set your ``email`` and ``password`` in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``cloudbroker_ping_receiver_node`` and ``cloudbroker_ping_sender_node`` node into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!

   .. code:: bash

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code:: bash

      occopus-build infra-cloudbroker-ping.yaml

#. After successful finish, the nodes with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code:: bash

      List of nodes/ip addresses:
      ping-receiver:
        192.168.xxx.xxx (f639a4ad-e9cb-478d-8208-9700415b95a4)
      ping-sender:
        192.168.yyy.yyy (99bdeb76-2295-4be7-8f14-969ab9d222b8)
      30f566d1-9945-42be-b603-795d604b362f

#. Check the result on your virtual machine.

   .. code:: bash

      ssh ...
      # cat /tmp/message.txt
      Hello World! I am the sender node created by Occopus.
      # cat /tmp/ping-result.txt
      PING 192.168.xxx.xxx (192.168.xxx.xxx) 56(84) bytes of data.
      64 bytes from 192.168.xxx.xxx: icmp_seq=1 ttl=64 time=2.74 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=2 ttl=64 time=0.793 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=3 ttl=64 time=0.865 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=4 ttl=64 time=0.882 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=5 ttl=64 time=0.786 ms

      --- 192.168.xxx.xxx ping statistics ---
      5 packets transmitted, 5 received, 0% packet loss, time 4003ms
      rtt min/avg/max/mdev = 0.786/1.215/2.749/0.767 ms

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code:: bash

      occopus-destroy -i 30f566d1-9945-42be-b603-795d604b362f
