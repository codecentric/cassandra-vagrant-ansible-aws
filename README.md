# Vagrant + Ansible + AWS Cassandra Setup

This project aims to `vagrant up` a multi-node/multi-datacenter Apache Cassandra ready-to-use production-grade cluster within Amazon's EC2 cloud.

## Configuration

### AWS side

- Make sure your default security group allows inbound SSH from any location
- Create a new security group which allows inbound traffic to ports used by Cassandra: 7000,7001,9042,9160.

### Vagrant side

- Install Vagrant (https://vagrantup.com)
- Install the `vagrant-aws` plugin: `vagrant plugin install vagrant-aws`
- Add the `dummy` aws box, the concrete config is done in the `Vagrantfile` and config: `vagrant box add dummy https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box`

Configure your desired cluster topology as well as AWS credentials in the aws-config.yml.template and rename it to aws-config.yml.