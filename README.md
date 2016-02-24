exabgp
======

Role to deploy and manage
[exabgp](https://github.com/Exa-Networks/exabgp).

Requirements
------------

The only requirement is *pip*.  This role will install *pip* if it is missing.

Role Variables
--------------

- **exabgp_version**: Version number of *exabgp* to deploy.  Defaults to 3.4.10.
- **exabgp_pid_file**: Path to PID file.  Defaults to */var/run/exabgp.pid*.
- **exabgp_user**: User for *exabgp* process to run as.  Defaults to *nobody*.
- **exabgp_group**: Group for *exabgp* process to run as.  Defaults to *nobody*.
- **exabgp_env**: This is a dictionary of dictionaries that configures
  how the *exabgp* process starts and runs.  Refer to *exabgp*
  documentation for what are valid configuration values.  A typical
  configuration knob is of the form *exabgp.log.enable*.  The first two
  parts, *exabgp.log* form the section name.  The last part is
  configuration key that accepts a parameter.  This defaults to:
```
  exabgp_env:
    exabgp.daemon:
      daemonize: true
      pid: "{{ exabgp_pid_file }}"
    exabgp.log:
      all: false
      enable: true
      destination: syslog
```
- **exabgp_conf**: This is a list of configuration sections for
  *exabgp.conf*.  Each element in the list is a dictionary with three
  possible keys: *section* (such as *group*, *neighbor*, etc), *name*
  (optional), and *config* that defines the actual configuration for
  this section.  This defaults to an empty list.
```
  exabgp_conf:
    - section: group
      name: EXAMPLE
      config: |-
        local-as 64512;
        local-address 127.0.0.1;
        hold-time 180;
        group-updates;
        family {
          ipv4 unicast;
        }
        neighbor 127.0.0.10 {
          description "EXAMPLE";
          peer-as 64513;
        }
    - section: neighbor
      name: 127.0.0.15
      config: |-
        local-as 64512;
        local-address 127.0.0.1;
        description "Example neighbor";
        peer-as 64514;
        hold-time 5;
        static {
          route 127.0.0.2 {
            next-hop 127.0.0.3;
          }
        }
        family {
          inet unicast;
        }
```

Dependencies
------------

No dependency on other roles.

Example Playbook
----------------

Example:

    - hosts: bgp_servers
      vars:
        exabgp_neighbors:
          127.0.0.5:
            local-address: 127.0.0.1
            description: Example neighbor
            local-as: 64512
            peer-as: 64513
            hold-time: 5
            family:
              inet: unicast
            route:
              127.0.0.2:
                next-hop: 127.0.0.3
      roles:
         - { role: sfromm.exabgp }

License
-------

GPLv2

Author Information
------------------

See https://github.com/sfromm
