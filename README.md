# aspects_openssh
Install openssh server and configure /etc/ssh/sshd_config.

# Requirements
Set ```hash_behaviour=merge``` in your ansible.cfg file.

# Role Variables
## aspects_openssh_confd
See defaults/main.yml for default config.

If arguments need to be different between OS's, you can use the ansible_os_family as a key. Se the subsystemsftp setting in defaults/main.yml.

## aspects_openssh_confd_matches
Use for ```Match``` settings. defaults/main.yml has a commented out example cribbed from the openssh example sshd_config file.

## aspects_openssh_service_name
The name of the service for use in the handlers file.

It is set in OS family specific vars files, and included at the top of tasks/main.yml according to which OS family the host is running.

# Dependencies
* aspects_common_packages

# Example Playbook

    - hosts: servers
      roles:
         - { role: aspects_openssh }
      vars:
        aspects_openssh_confd:
          protocol:
            keyword: Protocol
            argument: "2"
          port:
            keyword: Port
            argument: "22"
          allowuers:
            keyword: AllowUsers
            argument: "user1 user2"

# License
MIT