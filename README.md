# hypothesis-deployment
Deploy `hypothesis` to various server environments

## Playbooks

* hypothesis.yml - The main playbook

## Roles

* openstax.common
* openstax.postgres
* openstax.nodejs
* openstax.hypothesis

These scripts use `ansible-galaxy` to use and share roles.  Install roles by running:

```
ansible-galaxy install -r requirements.yml
```

By default, this will install the `roles` to `galaxy_roles` based on this project's `ansible.cfg`.
