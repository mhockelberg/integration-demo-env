# Integration Demo Environment - Installer

A set of Ansible roles and playbooks that install an Integration Demo environment project on top of OpenShift 4.5 using operators.  This installer can be used to install the following components:
* 3scale API Management
* API Designer (Apicurito)
* Fuse Online
* AMQ Streams (Kafka, Connect, Connect S2I, Bridge)  
* MySQL (Employee sample DB)
* Noobaa S3 storage (s3 compatible option for 3scale RWX storage deployed with Noobaa community operator)
* Red Hat Single Sign-On

Prerequisites:
* Ansible 2.9.11+
* access to an OpenShift 4.5 cluster 
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
...

# components to install
threescale_install: true # install 3scale (boolean)
amqstreams_install: false # install AMQ streams (boolean)
apicurito_install: false # install API designer (boolean)
fuseonline_install: false # install fuse online (boolean)
microcks_install: false # install microcks (boolean) - not implemented yet
mysql_install: false # install MySQL (boolean)
noobaa_install: true # install noobaa (boolean)
sso_install: false # install SSO (boolean)
```

Set environment variables:
```
export api_aws_region=<< your aws region >> \
export api_aws_bucket=<< your aws s3 bucket name >> \
export api_aws_key=<< your aws key id >> \
export api_aws_secret=<< your aws key secret >> \
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

To provision selected components with 3scale using AWS S3 for RWX storage:
```
$ ansible-playbook -i inventories/inventory playbooks/install.yml \
    -e api_aws_region=$api_aws_region \
    -e api_aws_bucket=$api_aws_bucket \
    -e api_aws_key=$api_aws_key \
    -e api_aws_secret=$api_aws_secret \
    -e api_aws_auth=$api_aws_auth \
```

To provision selected components with 3scale using AWS S3 for RWX storage, 3scale SMTP configured (see 3scale system-smtp secret) and other 3scale specific system values configured (see 3scale system-seed secret):
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

To provision selected components with 3scale using Noobaa S3 for RWX storage, 3scale SMTP configured (see 3scale system-smtp secret) and other 3scale specific system values configured (see 3scale system-seed secret):
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

Installation takes approximately 10-15 minutes depending on selected components.


## Uninstalling (TODO)

To uninstall:
```
$ ansible-playbook -i inventories/inventory playbooks/install.yml -e ACTION=uninstall
```
