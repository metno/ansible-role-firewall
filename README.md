firewall
=========

A simple firewall for Linux hosts, based on the common iptables/netfilter commands. This role is heavily inspired by [geerlingguy/firewall](https://github.com/geerlingguy/ansible-role-firewall). Some code is reused directly. Other code is new, and adjusted for MET Norways usecase.

Requirements
------------

Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here. For instance, if the role uses the EC2 module, it may be a good idea to mention in this section that the boto package is required.

This role is limited to

* Ubuntu 18.04
* Ubuntu 16.04
* Ubuntu 12.04
* Centos 7
* Centos 6

Role Variables
--------------

* `firewall_disable_firewalld` --- disable firewalld on RedHat systems, default `true`.
* `firewall_disalbe_ufw` --- disable ufw on Debian based systems, default `true`.
* `firewall_enable_on_boot` --- enable firewall on boot, default `true`.



Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

MIT / BSD

Author Information
------------------

Created 2019 by [Arnulf Heimsbakk](mailto:arnulf.heimsbakk@met.no) for MET Norway.

###### set vim: spell spelllang=en:
