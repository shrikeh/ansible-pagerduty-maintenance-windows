# Ansible PagerDuty Maintenance Windows
=========
[![Ansible Role](https://img.shields.io/ansible/role/ansible-6320.svg)](https://galaxy.ansible.com/detail#/role/6320)
[![Build Status](https://travis-ci.org/shrikeh/ansible-pagerduty-maintenance-windows.svg)](https://travis-ci.org/shrikeh/ansible-pagerduty-maintenance-windows)
[![GitHub Stars](https://img.shields.io/github/stars/shrikeh/ansible-server-density-monitoring.svg)](https://github.com/shrikeh/ansible-pagerduty-maintenance-windows)

Ansible role to create PagerDuty scheduled maintenance windows for services using the API.

## Requirements
------------

None.

## Role Variables
--------------

None.

## Dependencies
------------

None.

## Example Playbook
----------------

```YAML
---
    - hosts: servers
      roles:
         - { role: shrikeh.pagerduty-maintenance-windows }
...
```

## License
-------

[MIT][licence]

## Author Information
------------------
Contact me on Twitter @[barney_hanlon][twitter]


[licence]: https://raw.githubusercontent.com/shrikeh/ansible-server-density-monitoring/master/LICENSE "Link to the license in the repository"
[twitter]: https://twitter.com/barney_hanlon "Link to my Twitter page"
