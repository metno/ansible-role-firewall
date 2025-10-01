firewall
=========

A simple firewall for Linux hosts, based on the common iptables/netfilter commands. This role is inspired by [UFW](https://en.wikipedia.org/wiki/Uncomplicated_Firewall "Uncomplicated Firewall") and [geerlingguy/firewall](https://github.com/geerlingguy/ansible-role-firewall).

Version
-------

* `5.1.0` --- Added support for RHEL10. The role now supports os_family = redhat.
* `5.0.1` --- Moved testing to Ansible Molecule
* `5.0.0` --- Updated for ansible-core 2.16. Removed support for Ubuntu Xenial and Ubuntu Bionic.
* `4.1.1` --- update meta/main.yml
* `4.1.0` --- added support for ubuntu 24.04
* `4.0.1` --- bug fix, ansible-lint
* `4.0.0` --- update to ansible 2.12.9
* `3.8.0` --- add RHEL9 and CentOS Stream 8 support
* `3.7.0` --- add Jammy, remove CentOS 8
* `3.6.0` --- add RHEL8, remove CentOS 6 and trusty
* `3.5.0` --- remove ubuntu precise from testing
* `3.4.0` --- added ubuntu focal, 20.04
* `3.3.2` --- tested with Ansible 2.9.11
* `3.3.1` --- bump, prepare for github
* `3.3.0` --- log invalid packages as IPT BLOCK INVALID, renamed IPTABLES in log to IPT
* `3.2.0` --- added option to limit ping with `firewall_echo_request_from_ipv4` and `firewall_echo_request_from_ipv6`
* `3.1.2` --- install dependencies for check mode in check mode for centos
* `3.1.1` --- updated for CentOS 8
* `3.1.0` --- firewall for  openstack neutron nodes, set `firewall_is_neutron` to `true`
* `3.0.2` --- satisfy lint, use pipefail when reading in new firewalls
* `3.0.1` --- check if docker is running if `check_mode == true`
* `3.0.0` --- handle docker if docker is running, user `DOCKER-USER` chain instead of forward
* `2.0.0` --- flush firewall after adding rules only when `firewall_flush_on_change`, else append rules
* `1.0.1` --- fix, `ansible-playbook --check` works again
* `1.0.0` --- initial release
* `master` --- latest development version

Requirements
------------

This role supports

* RedHat Based OS, version 8,9,10
* Ubuntu 20.04, 22.04, and 24.04
* CentOS 7
* CentOS Stream 8


Role Variables
--------------

* `firewall_disable_firewalld` --- disable firewalld on RedHat systems, default `true`
* `firewall_disable_ufw` --- disable ufw on Debian based systems, default `true`
* `firewall_is_neutron` --- is this an openstack neutron node, default `false`
* `firewall_flush_on_change` --- flush firewall when rules have changed else only apply new rules, default `false`
* `firewall_enable_on_boot` --- enable firewall on boot, default `true`
* `firewall_log_enabled` --- enable firewall logging, default `true`
* `firewall_log_level` --- how much to log of dropped packages, default `-m limit --limit 3/min --limit-burst 10`
* `firewall_enable_ipv4_forward` --- enable ipv4 forwarding, default `false`
* `firewall_enable_ipv6_forward` --- enable ipv6 forwarding, default `false`
* `firewall_policy_input`, `firewall_policy_forward`, `firewall_policy_output` --- set policies for firewall, default `ACCEPT`
  * `ACCEPT` --- accept all packages
  * `DROP` --- drop packages silently
* `firewall_default_raw_ipv4` --- lines with raw iptables rules, default `'-A fw4-input -p tcp -m tcp --dport 22 -j ACCEPT'`  
    Use the following chain names, see examples for more context
    * `fw4-input` --- input chain ipv4, used when `firewall_policy_input` is set to `DROP`
    * `fw4-forward` --- forward chain ipv4, used when `firewall_policy_forward` is set to `DROP`
    * `fw4-output` --- output chain ipv4, used when `firewall_policy_output` is set to `DROP`
* `firewall_default_raw_ipv6` --- lines with raw iptables rules, default `'-A fw6-input -p tcp -m tcp --dport 22 -j ACCEPT'`  
    Use the following chain names, see examples for more context
    * `fw6-input` --- input chain ipv4, used when `firewall_policy_input` is set to `DROP`
    * `fw6-forward` --- forward chain ipv4, used when `firewall_policy_forward` is set to `DROP`
    * `fw6-output` --- output chain ipv4, used when `firewall_policy_output` is set to `DROP`
* `firewall_raw_ipv4` --- additional lines with raw iptables rules - use same chain names as `firewall_default_raw_ipv4`, default `''`
* `firewall_raw_ipv6` --- additional lines with raw iptables rules - use same chain names as `firewall_default_raw_ipv6`, default `''`
* `firewall_echo_request_from_ipv4` --- comma separated string with addresses/nets to allow ICMP echo request from, default not defined
* `firewall_echo_request_from_ipv6` --- comma separated string with addresses/nets to allow ICMP6 echo request from, default not defined

Example Playbook
----------------

    - hosts: servers
      roles:
         - role: firewall
          firewall_policy_input: DROP
          firewall_policy_forward: DROP
          firewall_policy_output: ACCEPT
          firewall_enable_ipv4_forward: true
          firewall_enable_ipv6_forward: false
          firewall_log_enabled: true
          # disable external ping
          firewall_echo_request_from_ipv4: 127.0.0.1
          firewall_echo_request_from_ipv6: ::1/128
          firewall_log_level: -m limit --limit 3/hour --limit-burst 5
          firewall_default_raw_ipv4: |
            -A fw4-input -p tcp -m tcp --dport 22 -j ACCEPT
          firewall_raw_ipv4: |
            -A fw4-input -s 10.0.0.0/24 -j ACCEPT
          firewall_default_raw_ipv6: |
            -A fw6-input -p tcp -m tcp --dport 22 -j ACCEPT
          firewall_raw_ipv6: |
            -A fw6-input  -s fe80::/10 -d fe80::/10 -p udp -m udp --sport 547 --dport 546 -j ACCEPT

Testing
-------

Testing is done using Ansible Molecule. It uses Vagrant with libvirt as backend.

To run full test run:

```bash
molecule test
```

To run test step by step run:

```bash
molecule create
molecule converge
molecule verify
molecule destroy
```

To run toward specific scenario use `-s` option.
```
molecule test -s ubuntu
```

License
-------

GPLv2

Author Information
------------------

Created 2019 by IT Infrastructure at MET Norway

Contactpoint: [IT Infrastructure Basis Team](mailto:it-is-basis@met.no)

###### set vim: spell spelllang=en:
