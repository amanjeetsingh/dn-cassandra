# dn-cassandra
Playbooks/Roles used to deploy Apache Cassandra

# Installation
To install Cassandra using the [provision-cassandra.yml](provision-cassandra.yml) playbook in this repository, first clone the contents of this repository to a local directory using a command like the following:

```bash
$ git clone --recursive https://github.com/Datanexus/dn-cassandra
```

That command will pull down the repository and it's dependencies. Currently this playbook's only dependencies are on the [common-roles](https://github.com/Datanexus/common-roles) and [common-utils](https://github.com/Datanexus/common-utils) submodules in this repository. The first provides a set of common roles that are reused across the DataNexus playbooks, while the second provides a similar set of common utilities, including a pair of dynamic inventory scripts that can be used to control deployments made using this playbook in AWS and OpenStack environments.

The only other requirements for using the playbook in this repository are a relatively recent (v2.x) release of Ansible. The easiest way to obtain a recent relese if Ansible is via a `pip install`, which requires that Python and pip are both installed locally. We have performed all of our testing using a recent (2.7.x) version of Python (Python 2); your mileage may vary if you attempt to run the playbook or the attached dynamic inventory scripts under a newer (v3.x) release of Python (Python 3).

# Using this role to deploy Cassandra
The [provision-cassandra.yml](provision-cassandra.yml) file at the top-level of this repository supports both single-node Cassandra deployments and the deployment of multi-node Cassandra clusters. The process of deploying Cassandra to these nodes will vary, depending on whether you are managing your inventory dynamically or statically (more on this topic [here](docs/Dynamic-vs-Static-Inventory.md)), whether you are performing a single-node deployment or are deploying a Cassandra cluster, and where you are downloading the packages and dependencies from that are needed to run Cassandra on those nodes.

We discuss the various deployment scenarios supported by this playbook in [this document](docs/Deployment-Scenarios.md) and discuss how the [Vagrantfile](Vagrantfile) in this repository can be used to deploy Cassandra (both single-node deployments and multi-node clusters are supported) to a set of VMs hosted locally in VirtualBox [here](docs/Deployment-via-Vagrant.md).

## Controlling the configuration
This repository includes a default set of parameters defined in the [vars/cassandra.yml](vars/cassandra.yml) and [defaults/main.yml](defaults/main.yml) files that make it possible to perform deployments of Cassandra out of the box with few, if any, changes necessary. If you are not happy with the default configuration defined in these two files, there are a number of ways that you can customize the configuration used for your deployment, and which method you use is entirely up to you:

* You can edit the [vars/cassandra.yml](vars/cassandra.yml) or [defaults/main.yml](defaults/main.yml) files to modify the default values that are defined in those files or define additional configuration parameters
* You can override the values defined in these files or define additional configuration parameters by passing the values for those parameters into your `ansible-playbook` run on the command-line as extra variables
* You can setup a *local variables file* on the local filesystem of the Ansible host that contains the values for the parameters you wish to set or customize, then pass the location of that file into your `ansible-playbook` command as an extra variable

We have provided a summary of the configuration parameters that can be set (using any of these three methods) during an `ansible-playbook` run [here](docs/Supported-Config-Params.md). Overall, we have found the last option to be the easiest and most flexible of those three options. This is because:

* It avoids modifying files that are being tracked under version control in the main, `dn-cassandra` repository (the first option); making such changes will, more than likely, lead to conflicts at a later date when these files are modified in the main `dn-cassandra` repository in a way that is inconsistent with the values that you have set in your clone, locally.
* It lets you maintain your preferred configuration for any given Cassandra deployment in the form of a configuration file, which you can easily maintain (along with the configuration files used for other deployments you have made) under version control in a separate repository
* It provides a record of the configuration of any given deployment, which is in direct contrast to the second option (where the configuration parameters for any given deployment are passed in on the command-line as extra variables)

That being said, the second option may be useful for some deployment scenarios (a one-off deployment of a local test environment, for example), so it remains a viable option for some users. Overall, we would recommend against trying to maintain your preferred cluster configuration using the values defined in the [vars/cassandra.yml](vars/cassandra.yml) and [defaults/main.yml](defaults/main.yml) files.

# Assumptions
It is assumed that this playbook will be run on a recent (systemd-based) version of RHEL or CentOS (RHEL-7.x or CentOS-7.x, for example); no support is provided for other distributions or earlier versions of these distributions (the `site.xml` playbook will not run successfully). Furthermore, it is assumed that you are interested in deploying a relatively recent version of Cassandra using this playbook (the current default is v3.10). In recent tests against the v3.0.11 release, the `cassandra` service errored out due to extra configuration parameters in the `cassandra.yaml` file and, as such, the cluster failed to start. There is an open issue, but that issue has not been resolved.

It should also be noted that in order to execute the vagrant commands shown in [this document](docs/Deployment-via-Vagrant.md) locally, recent versions of [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org) will have to be installed locally. While Vagrant does support management of Virtual Machines deployed via VMware Workstation and/or Fusion with the right (commercial) drivers in place, we have only tested the [Vagrantfile](Vagrantfile) in this repository under VirtualBox using recent (v1.9.x) releases of Vagrant.
