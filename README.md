Role Name
=========

Install and configure [postfix](https://www.postfix.org/) MTA.

Requirements
------------

If you're using this to send external emails or as an SMTP relay port 25 and/or 587 must be open outgoing. This is usually blocked on domestic internet providers because of the issue with SPAM.

Role Variables
--------------

| Variable                         | Required | Default                                                               | Description                              |
| -------------------------------- | -------- | --------------------------------------------------------------------- | ---------------------------------------- |
| postfix_templates                | No       | See below                                                             | List of arbitrary templates to apply     |
| postfix_config_file              | No       | /etc/postfix/main.cf                                                  | Postfix configuration file path          |
| postfix_service_state            | No       | started                                                               | Postfix systemd service state            |
| postfix_service_enabled          | No       | true                                                                  | Postfix systemd enabled state            |
| postfix_inet_interfaces          | No       | localhost                                                             | Postfix option inet_interfaces           |
| postfix_inet_protocols           | No       | all                                                                   | Postfix option inet_protocols            |
| postfix_smtpd_banner             | No       | $myhostname ESMTP $mail_name                                          | Postfix option smtpd_banner              |
| postfix_mynetworks               | No       | 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128                          | Postfix option mynetworks                |
| postfix_smtpd_tls_cert_file      | No       | /etc/ssl/certs/ssl-cert-snakeoil.pem                                  | Postfix option smtpd_tls_cert_file       |
| postfix_smtpd_tls_key_file       | No       | /etc/ssl/private/ssl-cert-snakeoil.key                                | Postfix option smtpd_tls_key_file        |
| postfix_smtpd_tls_security_level | No       | may                                                                   | Postfix option smtpd_tls_security_level  |
| postfix_maillog_file             | No       | /var/log/postfix.log                                                  | Postfix option maillog_file              |
| postfix_myhostname               | No       | {{ inventory_hostname }}                                              | Postfix option myhostname                |
| postfix_smtpd_relay_restrictions | No       | permit_mynetworks permit_sasl_authenticated defer_unauth_destination  | Postfix option smtpd_relay_restrictions  |
| postfix_mydestination            | No       | $myhostname, localdomain, localhost, localhost.localdomain, localhost | Postfix option mydestination             |
| postfix_transport_template       | No       | transport.j2                                                          | Template for the postfix transports file |
| postfix_transport_file           | No       | /etc/postfix/transport                                                | Postfix option transport_file            |
| postfix_transports               | No       |                                                                       | List of tansports entries. See below     |

### postfix_templates

#### Default

```yaml
postfix_templates:
  - src: main.cf.j2
    dest: "{{ postfix_config_file }}"
```

#### List variables

| Variable | Required | Default | Description                   |
| -------- | -------- | ------- | ----------------------------- |
| src      | Yes      |         | Template source path          |
| dest     | Yes      |         | Compiled template destination |
| mode     | No       | 0o644   | Destination file permissions  |
| owner    | No       | root    | Destination file owner        |
| group    | No       | root    | Destination file group        |

### postfix_transports

| Variable | Required | Default | Description                       |
| -------- | -------- | ------- | --------------------------------- |
| pattern  | Yes      |         | Postfix transports option pattern |
| method   | Yes      |         | Postfix transports option method  |
| nexthop  | Yes      |         | Postfix transports option nexthop |

Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: all
      roles:
        - tuggan.postfix
      vars:
        postfix_templates:
          - src: main.cf.j2
            dest: /etc/postfix/main.cf
          - src: master.cf.j2
            dest: /etc/postfix/master.cf
            mode: 0664
            owner: root
            group: postfix
        postfix_transports:
          - pattern: *@subdomain.example.com
            method: smtp
            nexthop: 10.0.0.2
        postfix_dkim_socket: "unix:/opendkim/opendkim.sock"
      
License
-------

BSD
