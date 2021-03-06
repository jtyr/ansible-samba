samba
=====

Ansible role which helps to install and configure Samba.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```
---

- name: Example of how to install and configure Samba
  hosts: all
  vars:
    # Set domain
    samba_global_workgroup: example.cloud.local
    # Disable to load printers
    samba_global_load_printers: "no"
    # Add options into the Global section
    samba_global__custom:
      hosts allow: 127. 192.168.12. 192.168.13.
      dns proxy: "yes"
    # Remove default option from the global section
    samba_global_cups_options: null
    # Add new section
    samba_config__custom:
      public:
        comment: Public Stuff
        path: /home/samba
        public: "yes"
        writable: "yes"
        printable: "no"
        write list: +staff
    # Remove the Printers section
    samba_printers: {}
  roles:
    - samba
```


Role variables
--------------

```
# Package to be installed (explicit version can be specified here)
samba_pkg: samba

# Samba service name
samba_service: smb

# Enable the Samba service by default
samba_service_enable: yes

# Start the Samba service by default
samba_service_start: yes

# Path to the Samba config files
samba_config_file: /etc/samba/smb.conf
samba_hosts_config_file: /etc/samba/lmhosts
samba_users_config_file: /etc/samba/smbusers


# Default Hosts config
samba_hosts_config__default:
  127.0.0.1: localhost

# Custom Hosts config
samba_hosts_config__custom: {}

# Final Hosts config
samba_hosts_config: "{{
  samba_hosts_config__default.update(samba_hosts_config__custom) }}{{
  samba_hosts_config__default }}"


# Default Root user config
samba_users_root__default:
  - administrator
  - admin

# Custom Root user config
samba_users_root__custom: []

# Final Root user config
samba_users_root: "{{
  samba_users_root__default +
  samba_users_root__custom }}"

# Default Nobody user config
samba_users_nobody__default:
  - guest
  - pcguest
  - smbguest

# Custom Nobody user config
samba_users_nobody__custom: []

# Final Nobody user config
samba_users_nobody: "{{
  samba_users_nobody__default +
  samba_users_nobody__custom }}"

# Final Samba users config
samba_users_config:
  root: "{{ samba_users_root | join(' ') }}"
  nobody: "{{ samba_users_nobody | join(' ') }}"


# Values of the default Global section of the Samba config
samba_global_workgroup: mygroup
samba_global_server_string: Samba Server Version %v
samba_global_log_file: /var/log/samba/log.%m
samba_global_max_log_size: 50
samba_global_security: user
samba_global_passdb_backend: tdbsam
samba_global_load_printers: "yes"
samba_global_cups_options: raw

# Default Global section of the Samba config
samba_global__default:
  workgroup: "{{ samba_global_workgroup | upper }}"
  server string: "{{ samba_global_server_string }}"
  log file: "{{ samba_global_log_file }}"
  max log size: "{{ samba_global_max_log_size }}"
  security: "{{ samba_global_security }}"
  passdb backend: "{{ samba_global_passdb_backend }}"
  load printers: "{{ samba_global_load_printers }}"
  cups options: "{{ samba_global_cups_options }}"

# Custom Global section of the Samba config
samba_global__custom: {}

# Final Global section of the Samba config
samba_global:
  global: "{{
    samba_global__default.update(samba_global__custom) }}{{
    samba_global__default }}"


# Values of the default Homes section of the Samba config
samba_homes_comment: Home Directories
samba_homes_browseable: "no"
samba_homes_writable: "yes"

# Default Homes section of the Samba config
samba_homes__default:
  comment: "{{ samba_homes_comment }}"
  browseable: "{{ samba_homes_browseable }}"
  writable: "{{ samba_homes_writable }}"

# Custom Homes section of the Samba config
samba_homes__custom: {}

# Final Homes section of the Samba config
samba_homes:
  homes: "{{
    samba_homes__default.update(samba_homes__custom) }}{{
    samba_homes__default }}"


# Values of the default Printers section of the Samba config
samba_printers_comment: All Printers
samba_printers_path: /var/spool/samba
samba_printers_browseable: "no"
samba_printers_guest ok: "no"
samba_printers_writable: "no"
samba_printers_printable: "yes"

# Default Printers section of the Samba config
samba_printers__default:
  comment: All Printers
  path: /var/spool/samba
  browseable: "no"
  guest ok: "no"
  writable: "no"
  printable: "yes"

# Custom Printers section of the Samba config
samba_printers__custom: {}

# Final Printers section of the Samba config
samba_printers:
  printers: "{{
    samba_printers__default.update(samba_printers__custom) }}{{
    samba_printers__default }}"


# Custom Samba config
samba_config__custom: {}

# Temporal variable
samba_config__tmp: {}

# Final Samba config
samba_config: "{{
  samba_config__tmp.update(samba_global) }}{{
  samba_config__tmp.update(samba_homes) }}{{
  samba_config__tmp.update(samba_printers) }}{{
  samba_config__tmp.update(samba_config__custom) }}{{
  samba_config__tmp }}"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)


License
-------

MIT


Author
------

Jiri Tyr
