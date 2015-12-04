# Ansible PagerDuty Maintenance Windows

[![Ansible Role](https://img.shields.io/ansible/role/ansible-6373.svg)](https://galaxy.ansible.com/detail#/role/6373)
[![Build Status](https://travis-ci.org/shrikeh/ansible-pagerduty-maintenance-windows.svg)](https://travis-ci.org/shrikeh/ansible-pagerduty-maintenance-windows)
[![GitHub Stars](https://img.shields.io/github/stars/shrikeh/ansible-server-density-monitoring.svg)](https://github.com/shrikeh/ansible-pagerduty-maintenance-windows)

[Ansible][ansible] role to create [PagerDuty][pagerduty] scheduled maintenance windows for services using the API.

### Why an Ansible role?

Firstly, because it allows automation if you wish to coincide a maintenance window with your Ansible provisioning.

Secondly, because a lot of the variables you can stick in a `group_vars/all/pagerduty-maintenance.yml` or [Vault][ansible_vault], you can largely forget most of the settings and just concentrate on the dates. For extra ease, you can have hosts associated with certain services:

```YAML
---
# group_vars/databases/pagerduty-maintenance.yml

pagerduty_maintenance_window_host_service_ids:
  - ABCDEF
  - GHIJK
...
```

The advantage? Because humans suck at remembering things, whereas YAML files don't. Take a look at the example playbook below for more info.

![PagerDuty logo](https://www.pagerduty.com/wp-content/themes/pd2015_git_sass/assets/img/pagerduty-logo-500px.png)


## Requirements

[`httplib2`][httplib2] is required.

## Role Variables

##### `pagerduty_maintenance_windows`
Default: `none`

**Required**. List that defines the windows. Each window (item) in the list has the following properties:

- `token`: Optional. The authorisation token to use with this call. If not specified, `pagerduty_maintenance_window_token` is used. One or the other must be specified, i.e. if you do not set `pagerduty_maintenance_window_token` then you must specify a token for every window.
- `url`: Optional. The endpoint for the API call. If this is omitted, then `pagerduty_maintenance_window_api_url` is used instead. One or the other must be specified, i.e. if you do not set `pagerduty_maintenance_window_api_url` then you must specify a url for every window.
- `description`: Optional. Description (reason) for the service window.
- `service_ids`: **Required**. List of service IDs to set up the window for.
- `dates`: **Required**. List of dates for the window. You can have multiple dates; each list item is a new `start`/`end` pair, together with an optional `description`, i.e.:

```YAML
dates:
  - start:  		'2016-12-02T14:00:00Z'
    end:    		'2016-12-02T15:00:00Z'
  - start:  		'2016-12-03T14:00:00Z'
    end:    		'2016-12-03T15:00:00Z'
    description: 	'Special maintenance window'

```
This allows you to specify the same window for multiple dates (useful if you have a recurring scheduled backup at 3am, I have found...).

- `timeout`: Optional. You can give a specific URI call a different timeout if you so wish. Otherwise, it uses the value of `pagerduty_maintenance_window_api_timeout`.

A fully-specified window to append to `pagerduty_maintenance_windows` is thus:
```YAML
- token: 'foobarbaztoken'
  url: 'https://shrikeh.pagerduty.com/api/v1/maintenance_window'
  description: 'Daily backup'
  timeout: 35
  service_ids:
    - 'ABCDE'
    - 'UWXYZ'
  dates: # Add two separate windows, one hour in duration, 24 hours apart.
    - start:  		  '2016-12-02T14:00:00Z'
      end:    		  '2016-12-02T15:00:00Z'
    - start:  		  '2016-12-03T14:00:00Z'
      end:    	    '2016-12-03T15:00:00Z'
      description: 	'Second backup'
```

##### `pagerduty_maintenance_window_subdomain`
Default: `none`

Required if `pagerduty_maintenance_window_api_domain` is not overridden, or a window does not have a `url`. The subdomain of the PagerDuty endpoint, e.g. `shrikeh` for `shrikeh.pagerduty.com`.

##### `pagerduty_maintenance_window_api_domain`
Default: `"{{ pagerduty_maintenance_window_subdomain | mandatory }}.pagerduty.com"`

Required to be overridden if not every list item specifies a `url` key and `pagerduty_maintenance_window_subdomain` is not set.

##### `pagerduty_maintenance_window_api_endpoint`
Default: `/api/v1/maintenance_windows`

Endpoint for the call, as specified on the [PagerDuty API documentation][pagerduty_api_docs].

##### `pagerduty_maintenance_window_api_url`
Default: `"https://{{ pagerduty_maintenance_window_api_domain }}{{ pagerduty_maintenance_window_api_endpoint }}"`

Fully qualified URI for the API call, i.e. `shrikeh.pagerduty.com/api/v1/maintenance_windows`.

##### `pagerduty_maintenance_window_token`
Default: `none`

If a `token` is not specified as a key for an item in `pagerduty_maintenance_windows` then this is used.

##### `pagerduty_maintenance_window_api_timeout`
Default: `30`

Time in seconds before the API call times out. Can be overridden on a per-window setting using the `timeout` key.

## Dependencies
------------

None.

## Example Playbook
----------------

```YAML
---
    - hosts: databases
      vars:
        # Better to put these in group_vars/databases/pagerduty-maintenance-windows.yml
        pagerduty_maintenance_window_host_service_ids:
          - ABCDEF
          - GHIJK
      - hosts: webservers
        vars:
          # Better to put these in group_vars/webservers/pagerduty-maintenance-windows.yml
          pagerduty_maintenance_window_host_service_ids:
            - ABCDEF
            - DEFGH
    - hosts: all
      vars:
        pagerduty_maintenance_windows:
          - token: "{{ lookup('env', 'PAGERDUTY_MAINTENANCE_WINDOW_TOKEN') }}"
            service_ids: "{{ pagerduty_maintenance_window_host_service_ids }}"
            dates:
              - start:        '2016-12-02T14:00:00Z'
                end:          '2016-12-02T15:00:00Z'
                description:  'Nightly MySQL backup'
      roles:
         - { role: shrikeh.pagerduty-maintenance-windows }
...
```
## @todo
-------

Document outstanding default variables.

## License
-------

[MIT][licence]

## Author Information
------------------
Contact me on Twitter @[barney_hanlon][twitter]

[ansible]:http://www.ansible.com/ "Ansible home page"
[pagerduty]: https://www.pagerduty.com/ "PagerDuty home page"
[ansible_vault]: http://docs.ansible.com/ansible/playbooks_vault.html "Ansible Vault documentation"
[licence]: https://raw.githubusercontent.com/shrikeh/ansible-pagerduty-maintenance-windows/master/LICENSE "Link to the license in the repository"
[twitter]: https://twitter.com/barney_hanlon "Link to my Twitter page"
[httplib2]: https://pypi.python.org/pypi/httplib2 "Python httplib2 library"
[pagerduty_api_docs]: https://developer.pagerduty.com/documentation/rest/maintenance_windows/create "PagerDuty API documentation for maintenance windows"
