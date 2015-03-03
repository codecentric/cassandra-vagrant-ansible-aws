# Vagrant + Ansible + AWS Cassandra Setup - WORK IN PROGRESS

This project aims to `vagrant up` a multi-node/multi-datacenter Apache Cassandra ready-to-use production-grade cluster within Amazon's EC2 cloud. Configure and `vagrant up --provider=aws` to get going.

## Configuration

### AWS side

You need to have AWS setup so that

- there is a `default` security group
- there is a security group that allows inbound traffic to all ports used by Cassandra and DataStax OpsCenter Agent (configured defaults are for Cassandra: 7000,7001,9042,9160; for DataStax agent: 7199, 61621 as well as non-standard 7198, which is configured to be JMX RMI port)
- your instances receive a public DNS entry
- there is a key-pair for which you have the private key file available

Note that these settings are region specific in AWS, if you want to deploy to multiple regions you need to do this setup on each region. Also note that in case of multi-region deployments, you need to have *one* key-pair setup in all regions as the Ansible/Vagrant combination does not support multiple keys for multiple regions.

### Vagrant/Ansible side

- Install Vagrant (https://vagrantup.com)
- Install the `vagrant-aws` plugin: `vagrant plugin install vagrant-aws`
- Add the `dummy` aws box, the concrete config is done in the `Vagrantfile` and config: `vagrant box add dummy https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box`
- Install Ansible (http://www.ansible.com)

Configure your desired cluster topology as well as AWS credentials in the `aws-config.yml.template` and rename it to `aws-config.yml`. All configuration is done is this file. The deployed configuration files are in `ansible/roles/cassandra/templates`. You can overwrite the configuration there. All changes from the original file are annotated with a `changed` comment.

At the moment, we assume that you are using SSD storage and all data will be put to `/mnt/cassandra`.

This has at the moment only been tested under `Ubuntu 12.04.LTS`, so you should choose your AMI accordingly. Ansible relies on apt as a package manager.

## Deploy

`vagrant up --provider=aws` and you are good to go.

## Terminate the whole cluster

`vagrant destroy` (`-f` if you don't want to ack every node.)

## What do you get

- Multi-Region/Multi-Datacenter-Cassandra setup, preconfigured for...
  - AWS Multi-Region usage (EC2-Multi-Region-Snitch)
  - Settings optimized for SSD data storage (make sure to use an appropriate AMI)
  - monitoring using OpsCenter, DataStax agent is deployed and configured on each node
- DataStax OpsCenter node username "admin" and password "admin"

## Security concerns

The Ansible provisioning will setup a new superuser for Cassandra (setting a random password and revoking superuser rights for the default user cassandra) and configure JMX authentication with the username/password you configure. However, note that this information will be used on various occasions as command-line parameters as well as configuration files. For production usage, please audit the security of your cluster thoroughly.

## TODO

- DataStax agent can not connect to local node after authentication is setup...
- Figure out JMX authentication
- OpsCenter SSL Support
- Review SSL-communication for OpsCenter and Internode/DC
- Make Ansible roles provide meaningful defaults for configuration
- Make config template provide meaningful default for 2-dc-setup example setup so minimal adjustments are needed