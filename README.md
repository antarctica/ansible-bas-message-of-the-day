# Message Of The Day (`motd`)

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

* None

## Requirements

* Operating system images **MUST** contain suitable meta-data

The *name* and *version* of the operating system image must be accessible for use in this role. Ansible local facts are
the preferred mechanism to expose this information. Variables in this role will use default values which assume local
facts have been set.

If using operating system images from the [BAS Packer VM Templates](https://github.com/antarctica/packer-vm-templates) 
project, which is strongly recommended for all BAS projects, the following versions, or higher, provide this required
meta-data, which will be automatically picked up by this role.

| Template             | Minimum Version Required |
| -------------------- | ------------------------ |
| `antarctica/trusty`  | 3.1.0                    |
| `antarctica/centos7` | 0.5.0                    |
 
If you are using another operating system image you will need to ensure you either provide the same local facts, or 
override the variable defaults in this role. To use the same local facts, create a file 
`etc/ansible/facts.d/os_template.fact`. This file uses the INI format with using the structure shown below:

```
[general]
name=[OS image name]
name_alt=[OS image alternative name]
version=[OS image version]
```

Note: The `[OS image alternative name]` should be a version of the `[OS image name]` without any non-alphanumeric 
characters, except hyphens (`-`).

E.g.

```INI
[general]
name=antarctica/trusty
name_alt=antarctica-trusty
version=3.0.1
```

* Variables **MUST** override variables in this role for the infrastructure provider and manager

For information shown in the message of the day to be useful the variables for the infrastructure provider and manager 
**MUST** be set to relevant values. It is recommended to use *group_var* files to do this.

For example, a *group_vars* file for machines managed by Vagrant would set these variables to as shown below:

```yaml
---

# BAS message of the day
motd_information_infrastructure_provider: "{{ ansible_virtualization_type | default(omit) }}"
motd_information_infrastructure_manager: Vagrant
```

If you are using the 
[BAS Web & Applications Team Project Template (Pristine)](https://github.com/antarctica/Web-Applications-Project-Template)
suitable *group_vars* should already exist, you will will only need to ensure machines are placed into the correct 
groups using the inventory file (`provisioning/inventory`).

* All machines require unique identifiers from the BAS Systems Inventory

In addition to displaying system information, the message of the day displays the identifier for the current machine 
(or class of machines in the case of local development) within the BAS Systems Inventory.

To do this each machine needs to override variables in this role with the relevant identifier, and a base URL to view 
this identifier in the BAS Systems Inventory.

If you are using the 
[BAS Web & Applications Team Project Template (Pristine)](https://github.com/antarctica/Web-Applications-Project-Template)
suitable *group_vars* should already exist, you will will only need to ensure machines specify their identifiers.

To set machine specific identifiers it is recommended to use *host_vars* or *inventory_vars* files. For the the Base 
URL it is recommended to use a global variables file or suitably scoped *group_vars* file.

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
    - BAS.motd
```

### Tags

Tags are not used in this role.

### Variables

#### *motd_information_infrastructure_provider*

* **MUST** be specified
* Specifies which infrastructure provider powers the machine
* Example: `AWS EC2`
* Default: `""` - empty string

#### *motd_information_infrastructure_manager*

* **MUST** be specified
* Specifies which infrastructure manager is used on the machine
* Example: `Terraform`
* Default: `""` - empty string

#### *motd_information_distribution_name*

* **MAY** be specified unless Ansible fact discovery is disabled, otherwise **MUST** be specified
* Where not specified, the value of the *ansible_distrubtion* variable will be used, this is set by Ansible 
automatically providing fact discovery, where enabled.
* Default: `"{{ ansible_distribution }}"`

#### *motd_information_distribution_version*

* **MAY** be specified unless Ansible fact discovery is disabled, otherwise **MUST** be specified
* Where not specified, the value of the *ansible_distrubtion* variable will be used, this is set by Ansible 
automatically providing fact discovery, where enabled.
* Default: `"{{ ansible_distribution_version }}"`

#### *motd_information_template_name*

* **MAY** be specified if using a suitable operating system image, otherwise this **MUST** be specified
* Specifies the name of the operating system image used by the machine
* Values **SHOULD** consist of the template name formatted for human consumption (if multiple names are available)
* Where not specified, the value of the *ansible_local.os_template.general.name* variable will be used, providing this
is set by the operating system image. See the *requirements* section for more information.
* Default: `"{{ ansible_local.os_template.general.name }}"`

#### *motd_information_template_version*

* **MAY** be specified if using a suitable operating system image, otherwise this **MUST** be specified
* Specifies the version of the operating system image used by the machine
* Values **SHOULD** consist of the version number only, e.g. do not include "version" or "v" etc.
* Where not specified, the value of the *ansible_local.os_template.general.name* variable will be used, providing this
is set by the operating system image. See the *requirements* section for more information.
* Default: `"{{ ansible_local.os_template.general.version }}"`

#### *motd_information_systems_inventory_identifer*

* **MUST** be specified
* Specifies the identifier for the machine within the BAS Systems Inventory
* Values **MUST** be valid resource identifiers within the BAS Systems Inventory, as determined by Jira
* Values **MUST NOT** include a leading slash (`/`)
* Typically, given the nature of this variable, its value is set on a per-machine basis, as either a *host_var* or 
inventory variable
* Example: `BASSYS-6`
* Default: `""` - empty string

#### *motd_information_systems_inventory_resource*

* **MUST** be specified
* Specifies the base URL used when constructing links to viewing a resource within the BAS Systems Inventory
* Values **SHOULD** be the canonical URL for viewing resources within the BAS Systems Inventory
* Values **MUST NOT** include a trailing slash (`/`)
* Typically this value will be same for all resources and so can be set as a group_var or standalone variable
* Example: `https://jira.ceh.ac.uk/browse`
* Default: `""` - empty string

## Developing

### Issue tracking

Issues, bugs, improvements, questions, suggestions and other tasks related to this package are managed through the 
[BAS Web Services Forum](https://jira.ceh.ac.uk/projects/WSF) project on Jira.

This service is currently only available to BAS or NERC staff, although external collaborators can be added on request.
See our contributing policy for more information.

### Source code

All changes should be committed, via pull request, to the canonical repository, which for this project is:

`ssh://git@stash.ceh.ac.uk:7999/WSF/ansible-role-message-of-the-day.git`

A mirror of this repository is maintained on GitHub. Changes are automatically pushed from the canonical repository to
this mirror, in a one-way process.

`git@github.com:antarctica/ansible-bas-message-of-the-day.git`

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
