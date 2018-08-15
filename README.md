GitLab Runner 
=============

This role will install the [official GitLab Runner](https://gitlab.com/gitlab-org/gitlab-runner) with updates.

Requirements
------------

This role requires Ansible 2.5 or higher.

Role Variables
--------------

### Package options:

`gitlab_runner_package_name`
**Since Gitlab 10.x** The package name of `gitlab-ci-multi-runner` has been renamed to `gitlab-runner`. In order to install 
a version <= 10.x you will need to define this variable `gitlab_runner_package_name: gitlab-ci-multi-runner`. 
(_default: gitlab-runner_)

`gitlab_runner_package_version`
GitLab's runner version to install.(_default: 10.6.0_)

`gitlab_runner_source_list_checksum`
Hash of installer [file](https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh)([for 
version <10](https://packages.gitlab.com/install/repositories/runner/gitlab-ci-multi-runner/script.deb.sh)).

### GitLab's common settings:

`gitlab_runner_config_concurrent`
The maximum number of jobs to run concurrently. Defaults to the number of processor cores.

`gitlab_runner_config_log_level`
Log level (options: debug, info, warn, error, fatal, panic). Note that this setting has lower priority than log-level set by 
command line argument --debug, -l or --log-level

`gitlab_runner_config_check_interval`
Defines the interval length, in seconds, between new jobs check. The default value is 3; if set to 0 or lower, the default value will be used.

`gitlab_runner_config_sentry_dsn`
Enable tracking of all system level errors to sentry

`gitlab_runner_config_metrics_server`
Address (<host>:<port>) on which the Prometheus metrics HTTP server should be listening

More information about this options you can find 
[here](https://docs.gitlab.com/runner/configuration/advanced-configuration.html#the-global-section).

### Runner options:
`gitlab_runner_list`
Array of of runner name and key => value options which role use for create command to register runner.
For more information use `gitlab-runner register --help` in your console 
or see the [options for runner](https://docs.gitlab.com/runner/configuration/advanced-configuration.html#the-runners-section).
From version 1.3.0 this role supports for "array" options(```docker-volumes``` for example, see example).

Add role to project:
----------------
Add role into your requirements(_requirements.yml_ for example):
```yaml
- src: https://github.com/lexa-uw/ansible-gitlab-runner
  version: v1.3.0
  name: gitlab-runner
```

Install role: `ansible-galaxy install -r ./requirements.yml --roles-path ./roles/`

Playbook example:
----------------
### Install version >=10
```yaml
- hosts: all
  vars_files:
    - vars/main.yml
  roles:
    - { role: gitlab-runner }
```

Inside `vars/main.yml`
```yaml
gitlab_runner_package_version: 11.1.0
gitlab_runner_list:
  - name: First shell runner
    options:
      cache-dir: "/tmp/cache-runner-1"
      executor: 'shell'
      leave-runner:
      limit: 2
      registration-token: '123abc'
      url: 'https://gitlab.com/'
  - name: Second docker executor runner
    options:
      docker-image: '"alpine:3.7"'
      docker-volumes:
        - "/var/run/docker.sock:/var/run/docker.sock"
      executor: 'docker'
      leave-runner:
      limit: 2
      registration-token: '789xyz'
      tag-list: '"docker-in-docker"'
      url: 'https://example.com/'
```

### Install version <10
```yaml
- hosts: all
  vars_files:
    - vars/main.yml
  roles:
    - { role: gitlab-runner }
```

Inside `vars/main.yml`
```yaml
gitlab_runner_package_version: 9.5.1
gitlab_runner_package_name: 'gitlab-ci-multi-runner'
gitlab_runner_source_list_checksum: md5:092e775bc01064171166a113bc52aa09
gitlab_runner_list:
  - name: First shell runner
    options:
      leave-runner:
      url: 'https://gitlab.com/'
      registration-token: '123abc'
      executor: 'shell'
      limit: 2
      cache-dir: "/tmp/cache-runner-1"
```
