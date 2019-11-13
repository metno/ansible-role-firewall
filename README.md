firewall
=========

A simple firewall for Linux hosts, based on the common iptables/netfilter commands. This role is inspired by [UFW](https://en.wikipedia.org/wiki/Uncomplicated_Firewall "Uncomplicated Firewall") and [geerlingguy/firewall](https://github.com/geerlingguy/ansible-role-firewall).

Version
-------

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
* `firewall_enable_on_boot` --- enable firewall on boot, default `true`.
* `firewall_log_enabled` --- enable firewall logging, default `true`.
* `firewall_log_level` --- how much to log of dropped packages, default `-m limit --limit 3/min --limit-burst 10`.
* `firewall_policy_input`, `firewall_policy_forward`, `firewall_policy_output` --- set policies for firewall, default `SKIP`.
  * `ACCEPT` --- accept all packages.
  * `DROP` --- drop packages silently.
  * `SKIP` --- do not administrate this chain.
* `firewall4_default_raw` --- lines with raw iptables rules, default `'-A fw4-input -p tcp -m tcp --dport 22 -j ACCEPT'`.  
    Use the following chain names, see examples for more context.
    * `fw4-input` --- input chain ipv4, used when `firewall_policy_input` is set to `DROP`
    * `fw4-forward` --- forward chain ipv4, used when `firewall_policy_forward` is set to `DROP`.
    * `fw4-output` --- output chain ipv4, used when `firewall_policy_output` is set to `DROP`.
* `firewall6_default_raw` --- lines with raw iptables rules, default `'-A fw6-input -p tcp -m tcp --dport 22 -j ACCEPT'`.  
    Use the following chain names, see examples for more context.
    * `fw6-input` --- input chain ipv4, used when `firewall_policy_input` is set to `DROP`
    * `fw6-forward` --- forward chain ipv4, used when `firewall_policy_forward` is set to `DROP`.
    * `fw6-output` --- output chain ipv4, used when `firewall_policy_output` is set to `DROP`.
* `firewall4_raw` --- lines with raw iptables rules - use same chain names as `firewall4_default_raw`, default `''`.
* `firewall6_raw` --- lines with raw iptables rules - use same chain names as `firewall6_default_raw`, default `''`.


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
           firewall_log_enabled: true
           firewall4_default_raw: |
             -A fw4-input -p tcp -m tcp --dport 22 -j ACCEPT
           firewall6_default_raw: |
             -A fw6-input -p tcp -m tcp --dport 22 -j ACCEPT

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
