# Integration Demo Environment - Installer

A set of Ansible roles and playbooks that install an Integration Demo environment project on top of OpenShift 4.2+ using operators.  This installer can be used to install the following components:
* 3scale API Management
* API Designer (Apicurito)
* Fuse Online
* AMQ Streams (Kafka, Connect, Connect S2I, Bridge)  
* MySQL 5.7 (Employee sample DB)

Prerequisites:
* access to an OpenShift cluster 
* the appropriate version of `oc` client on the PATH
* logged in into the cluster with a user with cluster admin rights
* a Registry Credentials Secret with access to `registry.redhat.io` named `imagestreamsecret` should exist in the `openshift` Namespace. See https://access.redhat.com/RegistryAuthentication for more details.
* (optional) SMTP server to configure 3scale email notifications
* 3scale install requires AWS credentials and an existing s3 bucket for storage

## Installing (TODO)


Create a copy of the inventory template file:
```
$ cp inventories/inventory.template inventories/inventory
```

To provision all the components:
```
$ ansible-playbook -i inventories/inventory playbooks/install.yml
```

Installation takes approximately 10-15 minutes.


## Uninstalling (TODO)

To uninstall:
```
$ ansible-playbook -i inventories/inventory playbooks/install.yml -e ACTION=uninstall
```
