# README.md
# Ansible Role: icecast
Version 2.x is a comprehensive and flexible Ansible role to configure and install Icecast.  It can be used to configure Icecast as a standalone system, a master or a slave.  Multiple mounts and relay mounts can be configured if required.  Most aspects of Icecast can be configured for advanced use but there are enough sensible defaults to get a working system with very few role variables.  

Tested on openSUSE and on CentOS.  May work on others.  Requires Ansible version 2.0

# Role Variables
The current defaults can be found in [defaults/main.yml](defaults/main.yml) - These should be mostly self explanatory.

When run with just the default variables a working icecast server is installed and configured. The defaults set blank passwords which disables all authentication.

# Examples
These examples should cover most use cases and serve to demonstrate the role variables.

## Minimal Example
These variables provide enough to get a usable Icecast server (admin username defaults to "icecast-admin" but can be set with ```icecast_admin_user```)
```yaml
---
icecast_admin_password: hackme
icecast_source_password: hackme
```

## Specific mount or mounts
In this example, specific mounts are created. With the exception of ```http-headers``` , any valid mount configuration parameter can be used (see (http://www.icecast.org/docs/icecast-2.4.1/config-file.html#mountsettings)).

Notice that in this example, ```icecast_sources``` must be increased to at least 3 ([default is 2](http://www.icecast.org/docs/icecast-2.4.1/config-file.html#limits) (the fallback stream counts as an additional mount)).

```type``` defaults to "normal" if not specified.
```yaml
---
icecast_admin_password: hackme
icecast_sources: 3

icecast_mounts:
 - mount-name: /128.mp3
   username: billy
   password: hackme
   fallback-mount: /fallback.mp3
   fallback-override: 1

 - mount-name: /256.mp3
   username: ray
   password: hackme
```

## Master server relay
Icecast refers to its two types of relaying as either "Master server relay" (relay all master mounts) or "Specific mount relay" (relay specific master mounts).

The following demonstrates a minimal master server and relay server using this role.

### Master server relay - Master setup
```yaml
---
icecast_admin_password: hackme
icecast_source_password: hackme
icecast_relay_password: hackme2
```
### Master server relay - Relay setup
```yaml
---
icecast_admin_password: relayhackme

icecast_master_server_relay_enable: True
icecast_master_server: master.example.com
icecast_master_password: hackme2
```

Notice the use of ```icecast_master_server_relay_enable: True```
this enables the defaults for the [Master server relay settings.](http://www.icecast.org/docs/icecast-2.4.1/config-file.html#relay) The role defaults are shown below.
### Master server relay defaults
This are the defaults used when ```icecast_master_server_relay_enable``` is set to ```True```
```yaml
icecast_master_server_relay_enable: False
icecast_master_server:
icecast_master_server_port: 8000
icecast_master_update_interval: 120
icecast_master_username: relay
icecast_master_password:
icecast_relays_on_demand: 0
```
## Specific mountpoint relay
This example demonstrates how a relay can relay just a specific mount from a master.  Multiple relay mounts can be provided.  Any of the [relay mount settings](http://www.icecast.org/docs/icecast-2.4.1/config-file.html#relay) can be used.
```yaml
---
icecast_admin_password: hackmeplease

icecast_relay_mounts:
  - server: master.example.com
    port: 8000
    mount: /default.mp3
    on-demand: 1
```
Relay mounts can also be combined with mount settings. In this example the relayed stream "/default.mp3" will fallback to a local mp3 named "fallback.mp3" if the stream cannot be contacted.  Using the ```fallback-overide``` setting Icecast will attempt to reconnect listeners from the fallback stream to the original relayed stream if that stream becomes available again. 

```yaml
---
icecast_admin_password: hackmeplease

icecast_relay_mounts:
  - server: master.example.com
    port: 8000
    mount: /default.mp3
    on-demand: 1

icecast_mounts:
  - type: normal
    mount-name: /default.mp3
    fallback-mount: /fallback.mp3
    fallback-override: 1
```
# Ports and SSL
Icecast can run on multiple ports.  In this example Icecast runs on port 8000 and 8001 with SSL enabled for port 8001.  A certificate path is also specified.
```yaml
icecast_ssl_certificate: /etc/icecast.pem

icecast_listen_sockets:
  - port: 8000
  - port: 8001
    ssl: 1
```

# Logging
The following values can be used to overide the corresponding log settings.

```icecast_accesslog``` - Name of accesslog

```icecast_errorlog``` - Name of errorlog

```icecast_loglevel``` - Log level 1-4

# YP (Yellow pages)
```icecast_yp_xiph_enable``` - Adds the station to the http://dir.xiph.org YP Directory - this also needs to be enabled in the client (e.g Ices).

```icecast_hostname``` - Sets the icecast hostname and should be set when using a directory service.

# License
MIT
