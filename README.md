## Datanyze test task
[![Build status](https://travis-ci.com/silazare/ansible-uwsgi-deploy.svg?)](https://travis-ci.org/silazare)


### Requirements
- Ansible 2.8 (Python3)
- Vagrant 2.2.5
- VirtualBox 6.0
- Ubuntu 18.04 for VM nodes

### Task description

Using Ansible best practices and multiple Vagrant instances, deploy a basic uwsgi application served by nginx, and a separate loadbalancer of your choice.
The provisioner should do all of the work, running 'vagrant up' once should result in a running and reachable stack.

- Utilize ansible roles
- Utilize ansible groups
- Utilize auto-generated inventory
- Create all roles yourself
- Two web servers, one load balancer
- Idempotent, should be able to run roles against instances repeatedly
- Utilize the latest version of Nginx from Nginx repos, not Ubuntu repos
- Ubuntu Server LTS (14.04 or 16.04)
- Access logs should go in /var/log/datanyze-logs/test-access.log
- The logs should be rotated automatically
- Security updates should be installed automatically
- All services should come back on after an instance restarts
- Use app.py code to be hosted

### Solution description

- By Default there are 2 application servers + 2 loadbalancers (This could be changed in Vagrantfile)
- For additional reliablity check - keepalived is installed to check loadbalancer VIP (default - 192.168.200.10)
- Ansible provisioner will run only after all VMs are up and running


### Usage

- Clone this repository to your folder:

- Startup Vagrant:
```sh
$ vagrant up
```

- After services started up, check HTTP at LBA VIP:
```
http://192.168.200.10
```