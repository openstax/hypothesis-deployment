# hypothesis-deployment

Deploy `hypothesis` to various server environments

## Architecture decisions

The following mainly describes the differences (and similiarities) you may encounter from other deployment projects.

### Directory Structure

The directory structure follows the [Alternative Directory Structure][ads] described in the Ansible docs. The main difference is the intentories directory is named environments and the hosts file is called inventory.

#### Example Structure

```shell
├── README.md
├── ansible.cfg
├── environments
│   ├── qa
│   │   ├── group_vars
│   │   │   ├── all.yml
│   │   └── inventory
│   ├── dev
│   │   ├── group_vars
│   │   │   ├── all.yml
│   │   └── inventory
├── galaxy_roles
├── hypothesis.yml
├── requirements.yml
├── tasks
    └── app_user.yml
```

## Shareable roles

This deployment repository utilizes the `ansible-galaxy` command and a `requirements.yml` file to specifiy roles that can be downloaded from GitHub. The roles are downloaded to the `galaxy_roles` directory. The `galaxy_roles` directory is where the the playbook will look to find the roles required to run the deployments.

The sharing of roles between many repos or playbooks is a "revolutionary" idea. It is an attempt to avoid monolithic repos, achieve some semblance of the Single Responsibility Principle (SRP), and to stop recreating roles for every app deployment. Currently, there may be 5 different postgresql roles! What?! Ideally we want to share our roles across playbooks b/c the more we are able to share generic roles across repos, the more robust they will become, and the less we need to recreate the wheel.

Roles have the responsiblity of doing all the configuration of the area they are responsible for. If that is to install and configure RabbitMQ then that role is responsible for all of that. If a change needs to happen to RabbitMQ it happens in only one place, the GitHub repo/role where that change is needed. This naturally allows you to make changes only where they need to happen. That is SRP.

When roles are in their own repo it also allows you to test them on their own. Unfortunately, not all the roles have tests but some of them do! Having some tests is better than no tests.

## Playbook variables

This repository acts as store for all the variables utilized in every environment such as dev, qa, staging, and prod. The variables are stored in `environments/dev/group_vars/all.yml` for the variables that override the defaults in the role. In this playbook all variables are defined even if they are already set as default in the role in order to be more explicit and act as a the single source of reference.

## Install Roles

These scripts use `ansible-galaxy` to use and share roles.  Install roles by running:

    ansible-galaxy install -r requirements.yml

By default, this will install the `roles` to `galaxy_roles` based on this project's `ansible.cfg`.

If you have already downloaded the roles previously, you may want to make sure they are up-to-date. To make sure all roles are up to date you'll need to delete the `galaxy_roles` directory and reinstall the requirements.

    rm -rf galaxy_roles && ansible-galaxy install -r requirements.yml

[ads]: https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html#alternative-directory-layout

## Run a deployment

In order to run a deployment you need to run the `ansible-playbook` command, specify an environment, and the location of the vault file:

    ansible-playbook -i environments/dev/inventory hypothesis.yml --vault-id=/vault/location

## Role Dependencies

### [Hypothesis Server](https://github.com/openstax/ansible-hypothesis.git)

The primary reason this repo exists. This installs and configures the OpenStax Hypothesis fork

### [Common](https://github.com/openstax/ansible-role-common.git)

Installs a common set packages used across most servers.

### [Pip](https://github.com/openstax/ansible-pip.git)

[![Build Status](https://travis-ci.org/openstax/ansible-pip.svg?branch=master)](https://travis-ci.org/openstax/ansible-pip)

Installs various versions of PIP

### [PostgreSQL](https://github.com/openstax/ansible-postgresql.git)

[![Build Status](https://travis-ci.org/openstax/ansible-postgresql.svg?branch=master)](https://travis-ci.org/openstax/ansible-postgresql)

Installs various versions of PostgreSQL. Defaults to `9.6`

### [NodeJS](https://github.com/openstax/ansible-role-nodejs.git)

[![Build Status](https://travis-ci.org/geerlingguy/ansible-role-nodejs.svg?branch=master)](https://travis-ci.org/geerlingguy/ansible-role-nodejs)

Installs various versions of NodeJS. This is a fork of `geerlinguy/nodejs` galaxy role.

### [WSGI](https://github.com/openstax/ansible-role-wsgi.git)

This was an attempt to install various forms of WSGI applications. Currently, this will only work with a Pyramid application. It may be better to actually separate out those webservers into different roles.

### [RabbitMQ](https://github.com/openstax/ansible-role-rabbitmq.git)

Installs and configures RabbitMQ.

### [ElasticSearch](https://github.com/openstax/ansible-role-elasticsearch.git)

Installs and configures ElasticSearch with the ICU plugin that is necessary for Hypothesis. A future version of this role would more than likely take plugins via an ansible variable.

### [Erlang](https://github.com/openstax/ansible-role-erlang.git)

Installs various versions of Erlang.

### [Nginx](https://github.com/openstax/ansible-role-nginx.git)

Installs various versions of Nginx, deletes default.conf, and can create multiple vhosts.

### [SSL](https://github.com/openstax/ansible-role-ssl.git)

This is used to pull down our certs from S3 or copy them a local directory.

### [Java](https://github.com/openstax/ansible-role-java.git)

Installs Oracle JDK 8.

### [Papertrail](https://github.com/openstax/ansible-role-papertrail.git)

This installs and configures Papertrail. There is another [role](https://github.com/openstax/osdeploy.papertrail) that was designed by Amanda to be a shared role and ironically enough I didn't know about it and ended up recreating it here! That was exactly the thing I was trying to avoid :facepalm:
