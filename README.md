firewall
=========

A simple firewall for Linux hosts, based on the common iptables/netfilter commands. This role is inspired by [UFW](https://en.wikipedia.org/wiki/Uncomplicated_Firewall "Uncomplicated Firewall") and [geerlingguy/firewall](https://github.com/geerlingguy/ansible-role-firewall).

Version
-------

* `3.0.2` --- satisfy lint, use pipefail when reading in new firewalls
* `3.0.1` --- check if docker is running if `check_mode == true`
* `3.0.0` --- handle docker if docker is running, user `DOCKER-USER` chain instead of forward
* `2.0.0` --- flush firewall after adding rules only when `firewall_flush_on_change`, else append rules
* `1.0.1` --- fix, `ansible-playbook --check` works again
* `1.0.0` --- initial release
* `master` --- latest development version

Requirements
------------

This role is limited to

* Ubuntu 18.04 - Bionic
* Ubuntu 16.04 - Xenial
* Ubuntu 14.04 - Trusty
* Ubuntu 12.04 - Precise
* CentOS 7
* CentOS 6

Role Variables
--------------

* `firewall_disable_firewalld` --- disable firewalld on RedHat systems, default `true`.
* `firewall_disable_ufw` --- disable ufw on Debian based systems, default `true`.
* `firewall_flush_on_change` --- flush firewall when rules have changed else only apply new rules, default `false`.
* `firewall_enable_on_boot` --- enable firewall on boot, default `true`.
* `firewall_log_enabled` --- enable firewall logging, default `true`.
* `firewall_log_level` --- how much to log of dropped packages, default `-m limit --limit 3/min --limit-burst 10`.
* `firewall_enable_ipv4_forward` --- enable ipv4 forwarding, default `false`
* `firewall_enable_ipv6_forward` --- enable ipv6 forwarding, default `false`
* `firewall_policy_input`, `firewall_policy_forward`, `firewall_policy_output` --- set policies for firewall, default `ACCEPT`.
  * `ACCEPT` --- accept all packages.
  * `DROP` --- drop packages silently.
* `firewall_default_raw_ipv4` --- lines with raw iptables rules, default `'-A fw4-input -p tcp -m tcp --dport 22 -j ACCEPT'`.  
    Use the following chain names, see examples for more context.
    * `fw4-input` --- input chain ipv4, used when `firewall_policy_input` is set to `DROP`
    * `fw4-forward` --- forward chain ipv4, used when `firewall_policy_forward` is set to `DROP`.
    * `fw4-output` --- output chain ipv4, used when `firewall_policy_output` is set to `DROP`.
* `firewall_default_raw_ipv6` --- lines with raw iptables rules, default `'-A fw6-input -p tcp -m tcp --dport 22 -j ACCEPT'`.  
    Use the following chain names, see examples for more context.
    * `fw6-input` --- input chain ipv4, used when `firewall_policy_input` is set to `DROP`
    * `fw6-forward` --- forward chain ipv4, used when `firewall_policy_forward` is set to `DROP`.
    * `fw6-output` --- output chain ipv4, used when `firewall_policy_output` is set to `DROP`.
* `firewall_raw_ipv4` --- additional lines with raw iptables rules - use same chain names as `firewall_default_raw_ipv4`, default `''`.
* `firewall_raw_ipv6` --- additional lines with raw iptables rules - use same chain names as `firewall_default_raw_ipv6`, default `''`.

Dependencies
------------

None.

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

### Test environment for all OSes

```bash
cd tests
vagrant up
```

### Rerun role

Run role on all OSes again.

```bash
vagrant provision
```

### Debug interactively

This uses cluster ssh to work with all vagrant boxes at the same time.

```bash
vagrant ssh-config > ~/.ssh/config
cat ~/.ssh/config | grep ^Host | cut -d\  -f2 | xargs cssh
```

License
-------

MIT / BSD

Author Information
------------------

Created 2019 by [Arnulf Heimsbakk](mailto:arnulf.heimsbakk@met.no) for MET Norway.

###### set vim: spell spelllang=en:
