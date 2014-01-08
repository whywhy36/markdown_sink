#oss-manifests

## why oss-manifests instead of oss-release
We used [oss-release](https://github.com/vchs/oss-release) to deploy the legacy OSS tools, which includes [Gerrit](https://code.google.com/p/gerrit/) and [Jenkins](http://jenkins-ci.org/). That's one heritage from Cloud Foundry. As we switch to vCHS and we want to upgrade the Gerrit and Jenkins, we decided to re-implement the OSS infrastructure using [Puppet](http://puppetlabs.com/) instead of BOSH. Puppet is the tool based on which zombie is developed.

## Prequisites
Before trying to deploy your oss-manifests instance. Make sure you get familiar with
- Puppet and its master & slave mode
- Puppet [Hiera](http://docs.puppetlabs.com/hiera/1/)
- Gerrit, Jenkins, Nginx configuraiton

## Overview
Oss-manifest will setup one OSS infrastructure which consists of:
- Nginx
- Gerrit master
- Gerrit replication (optional)
- MySQL master
- MySQL slave
- Jenkins master
- Jenkins slaves (the suggested instances' num is 4, you can increase it according to your need)

## Deployment
#### setup Puppet master & slave cluster
To deploy oss-manifest, you should have one cluster consists of one Puppet master and multiple slaves, each slave must be able to communicate with master node. The hostname schema of each node should like:

``[service-type]-[master|slave]-[index]``

For example, there are 4 Jenkins slave nodes, the second one should have hostname ``'jenkins-slave-1'``.

You can refer to the offical document of Puppet to setup master and slave nodes. You can also leverage [oss-boxbuilder](https://github.com/vchs/oss-boxbuilder) to ease the deployment.

#### setup Puppet master environment
After setting up Puppet master-slave cluster, ssh to the master node and pull this repository into any directory. 

``cd oss-manifests``

Configure your environment by modifying common.yaml under hieradata sub-directory.

``.\setup_puppet_master.sh``

This will initialize the puppet master environment by downloading all required Puppet modules and lay them into correct paths. 

#### deploy OSS environment
All puppet slaves will pull the catalogs from the master node. You don't have to do any other stuffs on each slave node. If you are a busy man and want the target environment to be setup as soon as possible, you can login into each slave node to trigger the pull operation manually. 

After the synchronization finished, you should also do some extra configuration:
- setup MySQL master-slave replication
- install Gerrit replication plugin 
These two tasks can be done by run
``./config_mysql_replica.sh MASTER_IP SLAVE_IP SLAVE_USER `` and ``./config_gerrit_master.sh GERRIT_MASTER_IP``

#### configure Gerrit
There's some differences between different Gerrit version. Please refer to the corresponding Gerrit documents. The first user of Gerrit will be the Administrator. If you want to add some pre-defined users, like non-interactive users, try to write some scripts to insert that users' information into database directly, that's what we used to do.

#### configure Jenkins
Jenkins master-slave cluste is automate setup. The Jenkins slave nodes will reigister themselves to master node as anonymous user. If you want to enable access control based on group, please be sure to allow anonymous users to manage slave creation, update and delete.

#### migration
If you want to migrate your own OSS environment to the puppet-powered one, try to leverage the already exists tools.
- Gerrit migration : copy Gerrit directoy and use mysqldump to export/import data.
- Gerrit upgrade: use gerrit.war
- Jenkins migration : just copy the job directories

## Usage
After setting up oss infrastructure, you can start to use it. There's one basic flow to use this infrasture:
1. install gerrit command line tool
2. gerrit clone repo-name
3. do some modification and gerrit push back
4. one jenkins job will be triggered and then score the corresponding commit according to the test result.
5. if the commit is verified and some reviewers approve your commit, you can merge this patchset.
6. if the commit breaks the test, you should redo step 3.

## Contribution
If you want to contribute your codes to improve this project, please refer to some other repositories, try to leverage or improve them will help this project become better.
- [oss-boxbulder](https://github.com/vchs/oss-boxbuilder)
- [vchs-service-puppet-modules](https://github.com/vchs/vchs-services-puppet-modules)
- [oss-epiphyllum](https://github.com/vchs/oss-epiphyllum)


