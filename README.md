exabgp
======

Role to deploy and manage
[exabgp](https://github.com/Exa-Networks/exabgp).

Requirements
------------

No external dependencies.

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
- **exabgp_neighbors**: This is a dictionary of dictionaries that
  configures the neighbors for *exabgp*.  The key is the neighbor IP
  address.  The configuration for the neighbor is then set as the key,
  value pairs.  For those parameters that do not take a value,
  eg. *passive*, treat them like a boolean.  Each subsection will
  similarly be a dictionary of key/value pairs.


Dependencies
------------

The only requirement is *pip*.  This role will install *pip* if it is missing.

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
