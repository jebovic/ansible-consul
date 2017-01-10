Consul
======

[![Build Status](https://travis-ci.org/jebovic/ansible-consul.svg?branch=master)](https://travis-ci.org/jebovic/ansible-consul) [![Ansible Galaxy](https://img.shields.io/badge/galaxy-jebovic.consul-blue.svg?style=flat)](https://galaxy.ansible.com/jebovic/consul)

Install and configure Consul

This role is a part of my [OPS project](https://github.com/jebovic/ops), follow this link to see it in action. OPS provides a lot of stuff, like a vagrant file for development VMs, playbooks for roles orchestration, inventory files, examples for roles configuration, ansible configuration file, and many more.

Dependencies
------------

This role depends on [jebovic.supervisor](https://github.com/jebovic/ansible-supervisor) role to be fully functional

Role Variables
--------------

```yaml
# Consul installation configuration
consul_dep_packages:
  - unzip
consul_install_dir: /usr/local/bin
consul_bin_path: "{{ consul_install_dir }}/consul"
consul_tarball_url: https://releases.hashicorp.com/consul/0.7.2/consul_0.7.2_linux_amd64.zip
consul_config_dir: /etc/consul/conf.d
consul_data_dir: /var/consul

# Consul configuration
consul_http_port: 8500
consul_services: []
consul_checks: []
```

Example Playbook
----------------

```yaml
- hosts: servers
  roles:
     - { role: jebovic.consul }
     - { role: jebovic.supervisor }
```

Tags
----

* consul_config : only update config and restart service

License
-------

MIT

Author Information
------------------

Jérémy Baumgarth https://github.com/jebovic
