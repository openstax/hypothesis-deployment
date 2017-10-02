# hypothesis-deployment
Deploy `hypothesis` to various server environments

## Playbooks

* hypothesis.yml - The main playbook

## Shared Roles
* [openstax.common](https://github.com/openstax/ansible-role-common)
* [openstax.postgres](https://github.com/openstax/ansible-role-postgres)
* [openstax.nodejs](https://github.com/openstax/ansible-role-nodejs)
* [openstax.hypothesis](https://github.com/openstax/ansible-role-hypothesis)

These scripts use `ansible-galaxy` to use and share roles.  Install roles by running:

```
ansible-galaxy install -r requirements.yml
```

By default, this will install the `roles` to `galaxy_roles` based on this project's `ansible.cfg`.
