# README.md
# Ansible Role: icecast

An Ansible role to configure and install Icecast

Tested on openSUSE and on CentOS.  May work on others.  Requires ansible version 2.0

## Role Variables

These are the current defaults:

```yaml
icecast_config_file: /etc/icecast.xml  
icecast_location: Earth
icecast_admin_email: icemaster@localhost
icecast_source_password: ''
icecast_relay_password: ''
icecast_admin_user: icecast-admin
icecast_admin_password: ''
icecast_yp_xiph_enable: False
icecast_hostname: example.com
icecast_port: 8000
icecast_user: icecast
icecast_group: icecast
```
Most of these parameters should be self explanatory.  The defaults set blank passwords which disables authentication.

```icecast_yp_xiph_enable``` Adds the station to the http://dir.xiph.org YP (Yellow Pages) Directory - this also needs to be enabled in the client (e.g Ices).

## Usage

A minimal working example is shown in the Example Playbook below.

## Example Playbook

```yaml
- hosts: icecast
  roles:
    - role: ansible-icecast
      icecast_source_password: hackme
      icecast_admin_user: icecast-admin
      icecast_admin_password: hackme
      icecast_hostname: example.com
---
```
## License

MIT
