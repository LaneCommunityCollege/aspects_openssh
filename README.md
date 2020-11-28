# aspects_openssh
Install openssh server and configure /etc/ssh/sshd_config.

# Requirements
Set ```hash_behaviour=merge``` in your ansible.cfg file.

# Role Variables
## aspects_packages_enabled
Turn [aspects_packages](https://github.com/LaneCommunityCollege/aspects_packages) on or off.

## aspects_openssh_enabled
Enable or disable tasks in this role. Set to ```True``` to run tasks. Set to ```False``` to block tasks. Default is set to ```False```.

## aspects_openssh_confd
Contains ```/etc/ssh/sshd_config``` variables in a dictionary. The dictionary is sorted by key, so each key must start with an appropriate prefix. The pattern is something like:

```
aspects_openssh_confd:
  <NNNNN><lowercasenospaceskeyword>:
    keyword: "<The ssh option from the manual>"
    argument: "<The value you want to set the ssh option to.>"
```

```defaults/main.yml``` contains default values for Protocol 2 related options. The values were taken from https://linux.die.net/man/5/sshd_config Except for Protocol. In this role, protocol version 1 is disabled by default.

If arguments need to be different between OS's, you can use the ansible_os_family as a key. See the example playbook below.

## aspects_openssh_confd_matches
Use for ```Match``` settings. See the example playbook below.

Note, override options are sorted the same way as in ```aspects_openssh_confd```.

## aspects_openssh_service_name
The name of the service for use in the handlers file.

It is set in OS family specific vars files, and included at the top of tasks/init.yml according to which OS family the host is running.

## aspects_openssh_subsystem_sftp
`sftp-server` is installed to different locations on RedHat and Debian based distributions.
The value in defaults should work for both via Jinja templating.
```
{% if ansible_os_family == 'RedHat' %}/usr/libexec/openssh/sftp-server{% else %}/usr/lib/openssh/sftp-server{% endif %}
```
If that doesn't work, you can override it.

# Dependencies

## aspects_packages
[aspects_packages](https://github.com/LaneCommunityCollege/aspects_packages) is used to manage packages.

# Example Playbook

```yaml
    - hosts: servers
      roles:
         - { role: aspects_openssh }
      vars:
        aspects_packages_enabled: True
        aspects_openssh_enabled: True
        aspects_openssh_confd:
          00000protocol:
            keyword: Protocol
            argument: "1,2"
          00010port:
            keyword: Port
            argument: "22"
          90000allowusers:
            keyword: AllowUsers
            argument: "user1 user2"
          90002subsystemsftp:
            keyword: Subsystem
            argument:
               Debian: "sftp /usr/lib/openssh/sftp-server"
               RedHat: "sftp /usr/libexec/openssh/sftp-server"
        aspects_openssh_confd_matches:
          anoncvs:
            condition: "Match User anoncvs"
            overrides:
              90000allowusers:
                keyword: AllowUsers
                argument: "anoncvs"
              90001allowgroups:
                keyword: AllowGroups
                argument: "anoncvs"
```

# License
MIT
