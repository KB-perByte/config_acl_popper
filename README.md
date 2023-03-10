# config_acl_popper

## Example Playbook

file: play.yml
```
- name: Ace delete role
  hosts:
    - ios
  gather_facts: false
  vars:
    - acls_data:
      -   acls:
          -   aces:
              -   grant: permit
                  sequence: 10
                  source:
                      address: 192.168.12.0
                      wildcard_bits: 0.0.0.255
              acl_type: standard
              name: '1'
          -   aces:
              -   destination:
                      any: true
                      port_protocol:
                          eq: '22'
                  grant: permit
                  protocol: tcp
                  sequence: 10
                  source:
                      any: true
              -   destination:
                      host: 192.168.20.5
                      port_protocol:
                          eq: '22'
                  grant: permit
                  protocol: tcp
                  sequence: 21
                  source:
                      host: 192.168.11.8
              acl_type: extended
              name: acl_123
          -   aces:
              -   destination:
                      address: 192.0.3.0
                      port_protocol:
                          eq: www
                      wildcard_bits: 0.0.0.255
                  grant: deny
                  option:
                      traceroute: true
                  protocol: tcp
                  protocol_options:
                      tcp:
                          fin: true
                  sequence: 10
                  source:
                      address: 192.0.2.0
                      wildcard_bits: 0.0.0.255
                  ttl:
                      eq: 10
              acl_type: extended
              name: test_acl
          afi: ipv4

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

Generates commands to negate a set of aces -
```
ip access-list standard 1
no 10 permit 192.168.12.0 0.0.0.255
ip access-list extended acl_123
no 10 permit tcp any any eq 22
no 21 permit tcp host 192.168.11.8 host 192.168.20.5 eq 22
ip access-list extended test_acl
no 10 deny tcp 192.0.2.0 0.0.0.255 192.0.3.0 0.0.0.255 eq www fin option traceroute ttl eq 10
```
