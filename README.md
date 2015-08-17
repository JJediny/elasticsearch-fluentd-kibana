# InnoHub Ansible : elasticsearch-kibana-fluentd
=========

* Creates an elasticseach, kibana and fluentd host
* Sets up fluentd client

Requirements
------------

* none yet

Role Variables
--------------

fluentd_plugins : optional : should contain fluentd plugins to be installed

- fluent-plugin-elasticsearch
- fluent-plugin-record-reformer
- fluent-plugin-record-modifier

fluentd_sources : optional : should contain fluentd sources to be configured
- params:
    type: "forward"
- params:
    type: "tail"
    path: "/var/log/nginx/access.log"
    pos_file: "/var/log/td-agent/httpd-access.log.pos"
    tag: "nginx.access"
    format: "nginx"
- params:
    type: "tail"
    path: "/var/log/nginx/error.log"
    pos_file: "/var/log/td-agent/httpd-error.log.pos"
    tag: "nginx.error"
    format: "nginx"

fluentd_matches : optional : should contain fluentd matches to be configured
- match: "nginx.**"
  params:
    type: "record_modifier"
    hostname: "${hostname}"
    tag: "log_with_hostname"
# to store to elasticsearch
- match: "log_with_hostname"
  params:
    type: "elasticsearch"
    logstash_format: "true"
    flush_interval: "60s"
# catch all to store to elasticsearch
- match: "**"
  params:
    type: "elasticsearch"
# to forward to log host server
- match: "log_with_hostname"
  params:
    type: "forward"
    flush_interval: "60s"
  server_params:
    host: "{{ hostvars[groups['log_host_servers'][0]]['ansible_eth0']['ipv4']['address']}}"

License
-------

MIT
