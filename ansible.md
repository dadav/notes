# knowledge

## playbooks

### execution order

1. pre_tasks
2. hadlers of pre_task
3. roles
4. tasks
5. handlers of roles and tasks
6. post_tasks
7. handlers of post_tasks

### variables

* ansible_all_ipv4_addresses

### useful tasks

#### Rebooting

```yaml
- name: Reboot a slow machine that might have lots of updates to apply
  reboot:
    reboot_timeout: 3600
```

#### Flush handlers

```yaml
- name: Force all notified handlers to run at this point, not waiting for normal sync points
  meta: flush_handlers
```

#### Group hosts by facts

```yaml
- hosts: all
  tasks
  - name: Create a group of all hosts by operating system
    group_by: key={{ansible_distribution}}

- hosts: Ubuntu
  tasks
  - debug: msg=foo
    
```

## useful commands
```bash
# show docs for a module
ansible-doc $module_name

# list callbacks
ansible-doc -t callback -l
```

# performance

There are some things you can do to increase performance.

## use mitogen

Mitogen updates Ansible’s slow and wasteful shell-centric implementation with pure-Python equivalents...

1. Download [mitogen](https://networkgenomics.com/try/mitogen-0.2.9.tar.gz)
2. Change the configuration

```ini
[defaults]
strategy_plugins = /path/to/mitogen-0.2.9/ansible_mitogen/plugins/strategy
strategy = mitogen_linear
```

## turn off gather_facts

```yaml
- hosts: all
  gather_facts: false
```

## parallelism

**Avoid the usage of `loop` if you can**

Modules like `yum` can install packages in parallel (with a single command).

## profiling

Use these options to profile your roles
```ini
[defaults]
callback_whitelist=timer, profile_tasks, cgroup_perf_recap
```

## ssh connection

Turn on control master and pipelining

```ini
[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s
pipelining = true
```
