# BAS Message Of The Day (`bas-motd`)

Configures the message of the day shown when logging into a machine

## Overview

* Replaces the default message of the day with a custom value containing information on the infrastructure, operating 
system, operating system image and identifier in the BAS Systems Inventory for the machine
* Retains information on the last login
* On Ubuntu machines, retains system update notices

### Intended Audience

This role is designed to meet the needs of BAS projects only, it is not intended to be a generic role.
Consequently this role may not suit the needs of other users or teams. This is not considered a limitation.

BAS also produces a number of roles, which are intended to be generic, within the BAS Ansible Roles Collection (BARC).
See [here](https://galaxy.ansible.com/detail#/user/18670) for available roles, 
and [here](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt) for a general overview and policies.

## Quality Assurance

This role uses manual testing to ensure the features offered by this role work as advertised. 
See tests/README.md for more information.

## Dependencies

* [**antarctica.bas-systems-inventory**](https://galaxy.ansible.com/antarctica/bas-systems-inventory/)
  * Minimum version: *0.1.0*

## Requirements

* None

## Limitations

* None

## Usage

### Typical playbook

```yaml
---

- name: apply login messages to identify each machine
  hosts: all
  become: yes
  vars: []
  roles:
    - antarctica.bas-motd
```

### Tags

Tags are not used in this role.

### Variables

#### *motd_information_infrastructure_manager*

* **MAY** be specified
* Specifies which infrastructure manager is used on the machine
* By default this variable will use the value of the *ansible_local.bas_systems_inventory.compute_resource.manager* 
local fact set by the **antarctica.bas-systems-inventory** role, this is a conventional default and **SHOULD NOT** be 
changed without good reason
* Example: `terraform`
* Default: `"{{ ansible_local.bas_systems_inventory.compute_resource.manager }}"

#### *motd_information_infrastructure_provider*

* **MAY** be specified
* Specifies which infrastructure provider powers the machine
* By default this variable will use the value of the *ansible_local.bas_systems_inventory.compute_resource.provider* 
local fact set by the **antarctica.bas-systems-inventory** role, this is a conventional default and **SHOULD NOT** be 
changed without good reason
* Example: `aws-ec2`
* Default: `"{{ ansible_local.bas_systems_inventory.compute_resource.provider }}"` - empty string

#### *motd_information_distribution_name*

* **MAY** be specified unless Ansible fact discovery is disabled, then **MUST** be specified
* Where not specified, the value of the *ansible_distrubtion* variable will be used, this is set by Ansible 
automatically providing fact discovery is enabled.
* Default: `"{{ ansible_distribution }}"`

#### *motd_information_distribution_version*

* **MAY** be specified unless Ansible fact discovery is disabled, then **MUST** be specified
* Where not specified, the value of the *ansible_distrubtion* variable will be used, this is set by Ansible 
automatically providing fact discovery is enabled.
* Default: `"{{ ansible_distribution_version }}"`

#### *motd_information_template_name*

* **MAY** be specified
* Specifies the name of the operating system image used by the machine
* Values **SHOULD** consist of the template name formatted for human consumption (if multiple names are available)
* By default this variable will use the value of the 
*ansible_local.bas_systems_inventory.compute_resource.system_image_name* local fact set by the 
**antarctica.bas-systems-inventory** role, this is a conventional default and **SHOULD NOT** be changed without good 
reason
* Default: `"{{ ansible_local.bas_systems_inventory.compute_resource.system_image_name }}"`

#### *motd_information_template_version*

* **MAY** be specified if using a suitable operating system image, otherwise this **MUST** be specified
* Specifies the version of the operating system image used by the machine
* Values **SHOULD** consist of the version number only, e.g. do not include "version" or "v" etc.
* By default this variable will use the value of the 
*ansible_local.bas_systems_inventory.compute_resource.system_image_version* local fact set by the 
**antarctica.bas-systems-inventory** role, this is a conventional default and **SHOULD NOT** be changed without good 
reason
* Default: `"{{ ansible_local.bas_systems_inventory.compute_resource.system_image_version }}"`

#### *motd_information_systems_inventory_identifer*

* **MAY** be specified
* Specifies the identifier for the Compute Resource associated with the host in the BAS Systems Inventory
* Values **MUST** be valid identifiers within the BAS Systems Inventory, as determined by the Systems Inventory
* Values **MUST NOT** include a leading slash (`/`)
* By default this variable will use the value of the *ansible_local.bas_systems_inventory.compute_resource.id* local 
fact set by the **antarctica.bas-systems-inventory** role, this is a conventional default and **SHOULD NOT** be changed 
without good reason
* Example: `BASSYS-6`
* Default: `"{{ ansible_local.bas_systems_inventory.compute_resource.id }}"`

#### *motd_information_systems_inventory_resource*

* **MAY** be specified
* Specifies the base URL used when constructing links to viewing resources within the BAS Systems Inventory
* Values **SHOULD** be the canonical URL for viewing resources within the BAS Systems Inventory
* Values **MUST NOT** include a trailing slash (`/`)
* Default: `"https://jira.ceh.ac.uk/browse"`

#### *motd_information_environment*

* **MAY** be specified
* Specifies the environment the Instance of the System from the BAS Systems Inventory this host is associated with
* Values **MUST** be the valid System Instance environments, as determined by the BAS Systems Inventory
* By default this variable will use the value of the *ansible_local.bas_systems_inventory.system_instance.environment* 
local fact set by the **antarctica.bas-systems-inventory** role, this is a conventional default and **SHOULD NOT** be 
changed without good reason
* Example: `"development-local"`
* Default: `"{{ ansible_local.bas_systems_inventory.system_instance.environment }}"

## Developing

### Issue tracking

Issues, bugs, improvements, questions, suggestions and other tasks related to this package are managed through the 
[BAS Web Services Forum](https://jira.ceh.ac.uk/projects/WSF) project on Jira.

This service is currently only available to BAS or NERC staff, although external collaborators can be added on request.
See our contributing policy for more information.

### Source code

All changes should be committed, via pull request, to the canonical repository, which for this project is:

`ssh://git@stash.ceh.ac.uk:7999/WSF/ansible-bas-motd.git`

A mirror of this repository is maintained on GitHub. Changes are automatically pushed from the canonical repository to
this mirror, in a one-way process.

`git@github.com:antarctica/ansible-bas-motd.git`

Note: The canonical repository is only accessible within the NERC firewall. External collaborators, please make pull 
requests against the mirrored GitHub repository and these will be merged as appropriate.

### Contributing policy

This project welcomes contributions, see `CONTRIBUTING.md` for our general policy.

The [Git flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow/) 
workflow is used to manage the development of this project:

* Discrete changes should be made within feature branches, created from and merged back into develop 
(where small changes may be made directly)
* When ready to release a set of features/changes, create a release branch from develop, update documentation as 
required and merge into master with a tagged, semantic version (e.g. v1.2.3)
* After each release, the master branch should be merged with develop to restart the process
* High impact bugs can be addressed in hotfix branches, created from and merged into master (then develop) directly

## License

Copyright 2016 NERC BAS.

Unless stated otherwise, all documentation is licensed under the Open Government License - version 3. All code is
licensed under the MIT license.

Copies of these licenses are included within this role.
