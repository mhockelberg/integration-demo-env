# Integration Demo Environment Installer

A set of Ansible roles and playbooks that install an Integration Demo environment project on top of an OpenShift 4.8 cluster using operators.  This installer can be used to install the following components:
* Red Hat 3scale API Management 2.11.0 (Operator 0.8.0-0.1634606167.p) 
* Red Hat AMQ Streams 1.8.2 (Operator 1.8.2)
* Red Hat API Designer 7.9.1 (Operator 7.9.1)
* Red Hat CodeReady Workspaces 2.12.1 (Operator 2.12.1)
* Red Hat Fuse Online 7.9.1 (Operator 7.9.1)
* Red Hat Single Sign-On 7.4.9 (Operator 7.4.9)
* Community Microcks 1.4.1 (Community Operator 1.4.1)
* Community MySQL 5.7 (Employee sample DB)
* Community Noobaa S3 storage 5.8.0 (Community Operator 5.8.0) s3 compatible option for 3scale RWX storage 

Prerequisites:
* Ansible, last tested with ansible [core 2.11.2] 
    * python version = 3.9.6 (default, Jun 29 2021, 05:25:02) [Clang 12.0.5 (clang-1205.0.22.9)]
    * jinja version = 3.0.1
    * libyaml = True 


* access to an OpenShift 4.8 cluster
* the Operator ClusterServiceVersions (CSVs) listed above installed
* the appropriate version of the OpenShift command-line `oc` client on the PATH
* logged in into the cluster with a user with cluster admin rights
* a Registry Credentials Secret with access to `registry.redhat.io` named `pull-secret` should exist in the `openshift-config` namespace. See https://access.redhat.com/RegistryAuthentication for more details.
* (optional) SMTP server to configure 3scale email notifications
* 3scale install requires RWX storage.  If using s3 for RWX you have two options.  Option 1 - Provide AWS s3 credentials and an existing s3 bucket.  Option 2 - deploy Noobaa s3 role for s3 compatible storage.  
#
# Installing

Create a copy of the inventory template file:
```bash
$ cp inventories/inventory.template inventories/inventory
```

Select the components to install in the playbooks/group_vars/all.yml file:
```yaml
# set components to install to true
threescale_install: false # install 3scale (boolean) # defaults to user/pass = admin/password
amqstreams_install: false # install AMQ streams (boolean)
apicurito_install: false # install API designer (boolean)
codeready_install: false # install CodeReady Workspaces(boolean)
fuseonline_install: false # install fuse online (boolean)
microcks_install: false # install microcks (boolean) # defaults to  user/pass = admin/password
mysql_install: false # install MySQL (boolean)
noobaa_install: false # install noobaa (boolean)
sso_install: false # install SSO (boolean)
```


To provision selected components without 3scale run:
```bash
$ ansible-playbook -i inventories/inventory playbooks/install.yml
```

To provision 3scale, set environment variables:
```bash
export api_aws_region='< your aws region >' \ # only if using AWS S3
export api_aws_bucket='< your aws s3 bucket name >' \ # only if using AWS S3
export api_aws_key='< your aws key id >' \ # only if using AWS S3
export api_aws_secret='< your aws key secret >' \ # only if using AWS S3
export api_aws_auth=aws-auth \
export smtp_host='< your smtp server host >' \ 
export smtp_port='< your smtp port >' \
export smtp_authentication=plain \
export smtp_userid='< your smtp user id >' \
export smtp_passwd='< your smtp password >' \
export threescale_admin_access_token=yQmehFcTN19P2N6V \
export threescale_admin_email='' \
export threescale_admin_passwd=password \
export threescale_admin_userid=admin \
export threescale_master_access_token=i5npMTPY \
export threescale_master_domain=3scale-master \
export threescale_master_passwd=password \
export threescale_master_userid=master \
export threescale_tenant_name=3scale \
export noobaa_aws_key='< noobaa s3 bucket aws key >' \ # only if Noobaa and 3scale installed separately
export noobaa_aws_secret='< noobaa s3 bucket aws secret >' \ # only if Noobaa and 3scale installed separately
export sso_admin_userid=admin \ # setting not implemented
export sso_admin_passwd=password # setting not implemented
```

To provision selected components with 3scale using AWS S3 for RWX storage run:
```bash
$ ansible-playbook -i inventories/inventory playbooks/install.yml \
    -e api_aws_region=$api_aws_region \
    -e api_aws_bucket=$api_aws_bucket \
    -e api_aws_key=$api_aws_key \
    -e api_aws_secret=$api_aws_secret \
    -e api_aws_auth=$api_aws_auth \
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

To provision selected components with 3scale using AWS S3 for RWX storage, 3scale SMTP configured (see 3scale system-smtp secret) and other 3scale specific system values configured (see 3scale system-seed secret) run:
```bash
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
```bash
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


Installation takes anywhere from 5-15 minutes (or slightly longer) depending on selected installation components.
  
#
# Uninstalling (TODO)

```Temporary Workaround```  
For each project that you want to uninstall:
1. Uninstall the Custom Resources
2. Uninstall the Operators
3. Delete the Project after all of the pods are terminated and removed by the first two steps

```Below Not implemented```  
To uninstall:
```bash
$ ansible-playbook -i inventories/inventory playbooks/install.yml -e ACTION=uninstall
```
#
# Troubleshooting and Tips


## CodeReady Workspaces

ISSUE:  I'm getting a CPU limit error when deploying a workspace in CodeReady Workspaces.

```Error: Failed to run the workspace: "Unrecoverable event occurred: 'FailedCreate', 'Error creating: pods "workspace<xxxxxxxxxxx>" is forbidden: maximum cpu usage per Pod is <x>, but limit is <x>m.', 'workspace<xxxxxxxxxxx>'"```

SOLUTION:  Verify that the max CPU limits configured on your cluster support your workspace configuration.
```
