Lighthouse-role
=========

This role can install Lighthouse-role on EL

Role Variables
--------------

|vars|description|
|---------|----------------|
| lighthouse_url | Which repository will Lighthouse be downloaded from  |
| lighthouse_dir | The home directory where Lighthouse will be downloaded |

Dependencies
------------

nginx

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: lighthouse-role }

License
-------

MIT

Author Information
------------------

Roman Skvorchenkov