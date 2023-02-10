# config_acl_popper

## Example Playbook

file: play.yml

```
  tasks:
  - name: Build source of truth based and keep backup
    ansible.builtin.include_role: &ref_role
      name: config_acl_popper
    vars:
      action: build source of truth

  - name: Apply negate commands
    ansible.builtin.include_role: *ref_role
    vars:
      action: apply back configuration
```
