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

## useful commands
```bash
# show docs for a module
ansible-doc $module_name

# list callbacks
ansible-doc -t callback -l
```

# performance

There are some things you can do to increase performance.

## turn of gather_facts

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
