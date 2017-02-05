Consul
======

[![Build Status](https://travis-ci.org/jebovic/ansible-consul.svg?branch=master)](https://travis-ci.org/jebovic/ansible-consul) [![Ansible Galaxy](https://img.shields.io/badge/galaxy-jebovic.consul-blue.svg?style=flat)](https://galaxy.ansible.com/jebovic/consul)

Install and configure Consul

This role is a part of my [OPS project](https://github.com/jebovic/ops), follow this link to see it in action. OPS provides a lot of stuff, like a vagrant file for development VMs, playbooks for roles orchestration, inventory files, examples for roles configuration, ansible configuration file, and many more.

Compatibility
-------------

Tested and approved on :

* Debian jessie (8+)
* Ubuntu Trusty (14.04 LTS)
* Ubuntu Xenial (16.04 LTS)

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
consul_tmp_full_path: /tmp/consul_0.7.2_linux_amd64.zip
consul_config_dir: /etc/consul/conf.d
consul_data_dir: /var/consul

# Consul configuration
consul_http_port: 8500
consul_config: []
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

Example : config
----------------

```yaml
# Consul global config
consul_config:
  datacenter: local-dc1
  acl_datacenter: local-dc1
  acl_master_token: "{{ secret_consul_master_token }}" # from _vars_enc.yml (ansible vaulted)
# Consul services definitions
consul_services:
  - name: api-platform
    check:
      interval: 10s
      script: "docker ps | grep -e 'srv_web' && docker ps | grep srv_database && (echo 'OK'; exit 0) || (echo 'KO';exit 1)"
    port: 8000
    tags:
      - web
# Consul checks definitions
consul_checks:
  - name: Health - ping
    interval: 30s
    script: "ping -c1 google.com"
  - name: Health - docker
    interval: 30s
    script: "docker ps"
    tags:
      - docker
  - name: Health - supervisor
    interval: 30s
    script: "supervisorctl status"
    tags:
      - supervisor
# Start consul in server mode with supervisor
supervisor_programs:
  consul:
    command: "{{ consul_bin_path }} agent -ui -bind={{ ansible_host }} -client=0.0.0.0 -node={{ ansible_fqdn }} -bootstrap -server -http-port {{ consul_http_port }} -data-dir={{ consul_data_dir }} -config-dir={{ consul_config_dir }} -domain={{ ansible_fqdn }}."
    autostart: "true"
    autorestart: "true"
    stderr_logfile: "{{ supervisor_log_path }}/consul-stderr.log"
    stderr_logfile_maxbytes: 1MB
    stderr_logfile_backups: 10
    stdout_logfile: "{{ supervisor_log_path }}/consul-stdout.log"
    stdout_logfile_maxbytes: 1MB
    stdout_logfile_backups: 10
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
