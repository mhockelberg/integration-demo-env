# Integration Demo Environment - Installer

A set of Ansible roles and playbooks that install an Integration Demo environment project on top of OpenShift 4.6 using operators.  This installer can be used to install the following components:
* Red Hat 3scale API Management 2.9 (Operator 0.6.1) 
* Red Hat API Designer 7.7.1 (Operator 7.7.1)
* Red Hat CodeReady Workspaces 2.5 (Operator 2.5.0)
* Red Hat Fuse Online (Operator 7.7.1)
* Red Hat AMQ Streams 7.7.1 (Operator 1.5.3)
* MySQL 5.7 (Employee sample DB)
* Noobaa S3 storage 5.5.0 (Community Operator 2.3.0) s3 compatible option for 3scale RWX storage 
* Red Hat Single Sign-On 7.4.3 (Operator 7.4.3)

Prerequisites:
* Ansible 2.9.11+
* access to an OpenShift 4.6 cluster
* the Operator ClusterServiceVersions (CSVs) listed above installed
* the appropriate version of the OpenShift command-line `oc` client on the PATH
* logged in into the cluster with a user with cluster admin rights
* a Registry Credentials Secret with access to `registry.redhat.io` named `pull-secret` should exist in the `openshift-config` namespace. See https://access.redhat.com/RegistryAuthentication for more details.
* (optional) SMTP server to configure 3scale email notifications
* 3scale install requires RWX storage.  If using s3 for RWX you have two options.  Option 1 - Provide AWS s3 credentials and an existing s3 bucket.  Option 2 - deploy Noobaa s3 role for s3 compatible storage.

## Installing

Create a copy of the inventory template file:
```
$ cp inventories/inventory.template inventories/inventory
```

Select the components to install in the playbooks/group_vars/all.yml file:
```
# set components to install to true
threescale_install: false # install 3scale (boolean)
amqstreams_install: false # install AMQ streams (boolean)
apicurito_install: false # install API designer (boolean)
codeready_install: false # install CodeReady Workspaces(boolean)
fuseonline_install: false # install fuse online (boolean)
microcks_install: false # install microcks (boolean) - not implemented yet
mysql_install: false # install MySQL (boolean)
noobaa_install: false # install noobaa (boolean)
sso_install: false # install SSO (boolean)
```


To provision selected components without 3scale run:
```
$ ansible-playbook -i inventories/inventory playbooks/install.yml
```

To provision 3scale, set environment variables:
```
export api_aws_region=<< your aws region >> \ # only if using AWS S3
export api_aws_bucket=<< your aws s3 bucket name >> \ # only if using AWS S3
export api_aws_key=<< your aws key id >> \ # only if using AWS S3
export api_aws_secret=<< your aws key secret >> \ # only if using AWS S3
export api_aws_auth=aws-auth \
export smtp_host=<< your smtp server host >> \ 
export smtp_port=<< your smtp port >> \
export smtp_authentication=plain \
export smtp_userid=<< your smtp user id >> \
export smtp_passwd=<< your smtp password >> \
export threescale_admin_access_token=yQmehFcTN19P2N6V \
export threescale_admin_email='' \
export threescale_admin_passwd=password \
export threescale_admin_userid=admin \
export threescale_master_access_token=i5npMTPY \
export threescale_master_domain=3scale-master \
export threescale_master_passwd=password \
export threescale_master_userid=master \
export threescale_tenant_name=3scale \
export noobaa_aws_key=<< noobaa s3 bucket aws key>> \ # only if Noobaa and 3scale installed separately
export noobaa_aws_secret=<< noobaa s3 bucket aws secret>> \ # only if Noobaa and 3scale installed separately
export sso_admin_userid=admin \ # setting not implemented yet
export sso_admin_passwd=password # setting not implemented yet
```

To provision selected components with 3scale using AWS S3 for RWX storage run:
```
$ ansible-playbook -i inventories/inventory playbooks/install.yml \
    -e api_aws_region=$api_aws_region \
    -e api_aws_bucket=$api_aws_bucket \
    -e api_aws_key=$api_aws_key \
    -e api_aws_secret=$api_aws_secret \
    -e api_aws_auth=$api_aws_auth \
```

To provision selected components with 3scale using AWS S3 for RWX storage, 3scale SMTP configured (see 3scale system-smtp secret) and other 3scale specific system values configured (see 3scale system-seed secret) run:
```
$ ansible-playbook -i inventories/inventory playbooks/install.yml \
    -e api_aws_region=$api_aws_region \
    -e api_aws_bucket=$api_aws_bucket \
    -e api_aws_key=$api_aws_key \
    -e api_aws_secret=$api_aws_secret \
    -e api_aws_auth=$api_aws_auth \
    -e smtp_host=$smtp_host \
    -e smtp_port=$smtp_port \
    -e smtp_authentication=$smtp_authentication \
    -e smtp_userid=$smtp_userid \
    -e smtp_passwd=$smtp_passwd \
    -e threescale_admin_access_token=$threescale_admin_access_token \
    -e threescale_admin_email=$threescale_admin_email \
    -e threescale_admin_passwd=$threescale_admin_passwd \
    -e threescale_admin_userid=$threescale_admin_userid \
    -e threescale_master_access_token=$threescale_master_access_token \
    -e threescale_master_domain=$threescale_master_domain \
    -e threescale_master_passwd=$threescale_master_passwd \
    -e threescale_master_userid=$threescale_master_userid \
    -e threescale_tenant_name=$threescale_tenant_name
```

To provision selected components with 3scale using Noobaa S3 for RWX storage, 3scale SMTP configured (see 3scale system-smtp secret) and other 3scale specific system values configured (see 3scale system-seed secret) run:
```
$ ansible-playbook -i inventories/inventory playbooks/install.yml \
    -e api_aws_auth=$api_aws_auth \
    -e smtp_host=$smtp_host \
    -e smtp_port=$smtp_port \
    -e smtp_authentication=$smtp_authentication \
    -e smtp_userid=$smtp_userid \
    -e smtp_passwd=$smtp_passwd \
    -e threescale_admin_access_token=$threescale_admin_access_token \
    -e threescale_admin_email=$threescale_admin_email \
    -e threescale_admin_passwd=$threescale_admin_passwd \
    -e threescale_admin_userid=$threescale_admin_userid \
    -e threescale_master_access_token=$threescale_master_access_token \
    -e threescale_master_domain=$threescale_master_domain \
    -e threescale_master_passwd=$threescale_master_passwd \
    -e threescale_master_userid=$threescale_master_userid \
    -e threescale_tenant_name=$threescale_tenant_name \
    -e noobaa_aws_key=$noobaa_aws_key \ # only if Noobaa and 3scale installed separately
    -e noobaa_aws_secret=$noobaa_aws_secret # only if Noobaa and 3scale installed separately
```

To provision selected components without 3scale:
```
$ ansible-playbook -i inventories/inventory playbooks/install.yml 
```


Installation takes approximately 10-15 minutes (or slightly longer) depending on selected installation components.


## Uninstalling (TODO)

To uninstall:
```
$ ansible-playbook -i inventories/inventory playbooks/install.yml -e ACTION=uninstall
```
