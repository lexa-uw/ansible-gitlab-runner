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
Array of key => value options which role use for create command to register runner.
For more information use `gitlab-runner register --help` in your console 
or see the [options for runner](https://docs.gitlab.com/runner/configuration/advanced-configuration.html#the-runners-section)

Add role to project:
----------------
Add role into your requirements(_requirements.yml_ for example):
```yaml
- src: https://github.com/lexa-uw/ansible-gitlab-runner
  version: v1.0.0
  name: gitlab-runner
```

Install role: `ansible-galaxy install -r ./requirements.yml --roles-path ./roles/`

Playbook example:
----------------
```yaml
- hosts: all
  vars_files:
    - vars/main.yml
  roles:
    - { role: gitlab-runner }
```

Inside `vars/main.yml`
```yaml
gitlab_runner_package_version: 10.5.0
  - name: First shell runner
    options:
      leave-runner:
      url: 'https://gitlab.com/'
      registration-token: '123abc'
      executor: 'shell'
      limit: 2
      cache-dir: "/tmp/cache-runner-1"
  - name: Second shell runner
    options:
      leave-runner:
      url: 'https://example.com/'
      registration-token: '789xyz'
      executor: 'shell'
      limit: 2
      cache-dir: "/tmp/cache-runner-2"
```
