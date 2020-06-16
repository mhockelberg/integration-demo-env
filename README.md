# 3scale - Installer

A set of Ansible roles and playbooks that install the 3scale Demo environment project on top of OpenShift 4.2+ using the 3scale Operator.

Prerequisites:
* access to an OpenShift cluster 
* the appropriate version of `oc` client on the PATH
* logged in into the cluster with a user with cluster admin rights
* a Registry Credentials Secret with access to `registry.redhat.io` named `imagestreamsecret` should exist in the `openshift` Namespace. See https://access.redhat.com/RegistryAuthentication for more details.
* (optional) SMTP server to configure 3scale email notifications

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
