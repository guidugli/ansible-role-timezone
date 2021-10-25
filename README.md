Ansible Role: timezone
=========

This role sets timezone on RHEL/CentOS, Fedora and Debian/Ubuntu systems.

Requirements
------------

Operating system running on bare metal or on a hypervisor virtualization. This role should not be executed on containers.

Role Variables
--------------

**Available variables are listed below, along with default values (see defaults/main.yml):**

    timezone_name: America/Sao_Paulo

Specify desired timezone. Refer to the following URL to consult available timezones: [List of time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

    #timezone_hwclock: 

Whether the hardware clock is in UTC or in local timezone. If not specified (default), keep current setting.
Note that this option is recommended not to change and may fail to configure, especially on virtual environments such as AWS.


Dependencies
------------

No dependencies.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      vars:
        timezone_name: America/Sao_Paulo
      roles:
         - { role: guidugli.timezone }

License
-------

MIT / BSD

Author Information
------------------

This role was created in 2020 by Carlos Guidugli.
