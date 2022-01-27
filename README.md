NOTE: Development and maintenance of this software has stopped. For more information, see [DISCONTINUATION_NOTICE.md](DISCONTINUATION_NOTICE.md).

sap-hana-preconfigure
=====================

This asserts that a RHEL 7.x or RHEL 8 system is configured according to the SAP notes so that SAP HANA can be installed

Requirements
------------

Note
----
For finding out which SAP notes will be used by this role, please check the contents of variable `__sap_hana_preconfigure_assert_sapnotes` in files `vars/*.yml` (choose the file which matches your OS distribution and version).

The role currently does not check if the grub2 config file had been regenerated after a change to GRUB_CMDLINE_LINUX in /etc/default/grub.

The role currently does not check resource limits of the database group.

The role does not check package groups. This is already part of role sap-preconfigure-assert.

Role Variables
--------------

- set in `defaults/main.yml`:

### Ignore assert errors
If the following variable is set to `yes`, the role will not fail in case of any assert errors. Default is `no`, meaning that the role will fail on each assertion error.
```yaml
sap_hana_preconfigure_assert_ignore_errors: yes
```

### Execute only certain steps of SAP notes
If the following variable is set to no, only certain steps of SAP notes will be executed or checked as per setting of variable `sap_hana_preconfigure_assert_<sap_note_number>_<step>`. If this variable is undefined or set to no, all installation and configuration steps of applicable SAP notes will be executed.
```yaml
sap_hana_preconfigure_assert_config_all
```

### Perform installation or configuration steps, or both
If you have set `sap_hana_preconfigure_assert_config_all` (see above) to `no`, you can limit the scope of the role to only execute the installation or the configuration steps. For this purpose, set one of the following variables, or both, to `yes`. The default for both is `no`.
```yaml
sap_hana_preconfigure_assert_installation
sap_hana_preconfigure_assert_configuration
```

### Define configuration steps of SAP notes
For defining one or more configuration steps of SAP notes to be executed or checked only, set variable `sap_hana_preconfigure_assert_config_all` to `no`, `sap_hana_preconfigure_assert_configuration` to `yes`, and one or more of the following variables to `yes`:
```yaml
sap_hana_preconfigure_assert_2777782_[02...10], example: sap_hana_preconfigure_assert_2777782_05
sap_hana_preconfigure_assert_2772999_09
sap_hana_preconfigure_assert_2292690_[01...07,09,10], example: sap_hana_preconfigure_assert_2292690_02
sap_hana_preconfigure_assert_2009879_3_9
sap_hana_preconfigure_assert_2009879_3_13
sap_hana_preconfigure_assert_2009879_3_14_[1...4]
sap_hana_preconfigure_assert_2009879_3_15
sap_hana_preconfigure_assert_2382421
```

### Repo checking and enabling
If you want the role to check and if necessary enable SAP HANA repos, set the following variable to `yes`. Default is `no`.
```yaml
sap_hana_preconfigure_assert_enable_sap_hana_repos
```

### Override default repo list(s)
If you want to provide you own list(s) of repositories for checking and enabling, override one or more of the following variables. Otherwise, the defaults as set in vars/RedHat_*.yml will be used.
```yaml
sap_hana_preconfigure_assert_req_repos_RedHat_7_x86_64
sap_hana_preconfigure_assert_req_repos_RedHat_7_ppc64le
sap_hana_preconfigure_assert_req_repos_RedHat_8_x86_64
sap_hana_preconfigure_assert_req_repos_RedHat_8_ppc64le
```

###  HANA kernel parameters
[SAP Note 238241](https://launchpad.support.sap.com/#/notes/238241) defines kernel parameters that all Linux systems need to set.
The default parameter recomendations are dependent on the OS release. Hence the OS dependant default setting is defined in
./vars/{{ansible_os_release}}.yml. If you need to add or change parameters for your system, copy these parameters from the vars file
into the variable sap_hana_preconfigure_assert_kernel_parameters and add or change your settings, as in the following example:

```yaml
sap_hana_preconfigure_assert_kernel_parameters:
  - { name: net.core.somaxconn, value: 4096 }
  - { name: net.ipv4.tcp_max_syn_backlog, value: 8192}
  - { name: net.ipv4.tcp_timestamps, value: 1 }
  - { name: net.ipv4.tcp_slow_start_after_idle, value: 0 }
```

Example Playbook
----------------

Here is a simple playbook:

```yaml
---
    - hosts: all
      roles:
         - role: sap-preconfigure-assert
         - role: sap-hana-preconfigure-assert
```

Here is a useful command for only showing only SAP note numbers, FAIL:, WARN:, PASS: messages
```yaml
ansible-playbook -l remote_host sap-hana-assert.yml | awk '/SAP note/||/FAIL:/||/WARN:/||/PASS:/{sub ("    \"msg\": ", ""); print}'
```

Contribution
------------

Please read the [developer guidelines](./README.DEV.md) if you want to contribute

License
-------

GNU General Public License v3.0

Author Information
------------------

Bernd Finger

Please leave comments in the github repo issue list
