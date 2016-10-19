Role Name
=========

A brief description of the role goes here.

```
# Set on provision in app role
setsebool httpd_enable_homedirs=1
setsebool httpd_read_user_content=1

# Change persist dir
sudo chcon -Rv --type=httpd_sys_content_rw_t /home/{{ appuser_name }}/{{ app_name }}/persist

# ???
# Symlink the bootstrap dir too :/ (or don't really need to clear-compiled?)
# this may be an issue if really needed
chcon -Rv --type=httpd_sys_content_rw_t /home/appuser/serialapp/current/bootstrap/cache
```

Requirements
------------

Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here. For instance, if the role uses the EC2 module, it may be a good idea to mention in this section that the boto package is required.

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
