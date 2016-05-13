# InnoHub Ansible : elasticsearch-kibana-fluentd
=========

* Creates an elasticseach, kibana and fluentd host
* Sets up fluentd client

Requirements
------------

* none yet

Role Variables
--------------

elasticsearch_heap_size : optional : should contain elasticsearch heap size

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


nginx_user_pwd: required : should contain your htpasswd file content
  - admin:$apr1$Qct6l9a7$rb.3WbVa7X/78/yVHyIwv/

nginx_ssl_crt: required : should contain your ssl certificate
  - -----BEGIN CERTIFICATE-----
    MIIB+zCCAWQCCQCCWEJqWVLYXDANBgkqhkiG9w0BAQUFADBCMQswCQYDVQQGEwJY
    WDEVMBMGA1UEBwwMRGVmYXVsdCBDaXR5MRwwGgYDVQQKDBNEZWZhdWx0IENvbXBh
    bnkgTHRkMB4XDTE1MDUxODA5MzMyMVoXDTE2MDUxNzA5MzMyMVowQjELMAkGA1UE
    BhMCWFgxFTATBgNVBAcMDERlZmF1bHQgQ2l0eTEcMBoGA1UECgwTRGVmYXVsdCBD
    b21wYW55IEx0ZDCBnzANBgkqhkiG9w0BAQEFAAOBjQAwgYkCgYEA3zavC57RxfVP
    bmIKBPslpsRMf+dF9oWLV/8DekvEnKl2W6MTjShsnQKF4JyoLduB/8obmSy8OQMN
    wD5CnTaV2oCtMV++T9WtCtbrvlK1NVQXvACREBtI09f+6myYAEr42eglCQIVxua7
    77UAiaO923uEyewpTlr4u35GeNCUUxUCAwEAATANBgkqhkiG9w0BAQUFAAOBgQBD
    FnlQTQnR/5uV3O4L6wtdiTTAbj0kDtY+kFLCjSMyDf6WbNczv9ZILJKcfB78xgxE
    5ppszdr6i34EPkng3dlJINwtwm+cE8wW4vMIakBeuXaewfHywPgLBnrsldsFAJeI
    4iaHuCnl49v3Geg4r9T2Jhv/QU1lxJsS/txEj244uA==
    -----END CERTIFICATE-----

nginx_ssl_key: required : should contain your ssl key
  - -----BEGIN RSA PRIVATE KEY-----
    MIICXgIBAAKBgQDfNq8LntHF9U9uYgoE+yWmxEx/50X2hYtX/wN6S8ScqXZboxON
    KGydAoXgnKgt24H/yhuZLLw5Aw3APkKdNpXagK0xX75P1a0K1uu+UrU1VBe8AJEQ
    G0jT1/7qbJgASvjZ6CUJAhXG5rvvtQCJo73be4TJ7ClOWvi7fkZ40JRTFQIDAQAB
    AoGBAJ511qycMfWjoaEOLmi81kzenCRMzeklgveUTSa7tTc2QpTpX07dfMW17c63
    x9jONSqekxsZnAGpPlDUojuzbYteWDB6Rxef/3c2+uGlJWw0DQQbimXzRuyqwwuy
    ynaHnCy6HhY+jBIp77RC96BQ5vHqN73Dt2bfEDLqqQwh5BWhAkEA791qJMJnBXjH
    PjzhrkF+/k6MbEZuxE+asfUPa9dDGHHeqIPe6ySwxaCfP8vYcPpJUi7dtrpRwsP7
    VogDb+4FfQJBAO46hrCiMtNpXHhr/jk3CmjRSkB+DwKd3IRhCIOryLl7MBpphwhw
    7mQMwmG3hYigtAAdCIWK1jI1FLNfoB7jl3kCQQDgH47yUgNZBYWGDfUBkYlLrbFf
    6l/NZmQxK74BEd5LlPprkuxT4rCZuH+UaLAQwIyKEehwuJ900RKjw+sSRuExAkEA
    t8PZxkM4JbCurg3QC16T3wsi1eDnv0L6qfsX0EohCkMmdhlVGoPSwMa+4xXoId6l
    OzkqK6lPVWdw0nU/VNSHMQJAKCdsUCy4Y8+neOXW9AcL95tlzwQ3C2dlbSadea2Y
    gAGrLMnFDWkogejrk5xVswlMUzjV6TefJAeQDHA1HIIR5g==
    -----END RSA PRIVATE KEY-----

License
-------

MIT
