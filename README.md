# Selective Authentication/ File Based IOS Configurfation Playbook

Intended as a free form all around ios_cofig based plabook can be translated to other vendor dependent modules like junos_config

Written for use with Ansible 2.5 network_cli.

## Selective Authentication

The playbook loads a centralized user authentication file (ansible-vault encrypted)
``` yml
---
creds:
 username: 'ansible'
 password: '@ns1bl3'

local_creds:
 username: 'ansible_local'
 password: '@ns1bl3#'

alt_creds:
 username: 'ansible_alt'
 password: '@ns1bl3$'
```

Then directed by the built-in vars:
``` yml
  vars:
    #authentication_provider: normal (tacacs) | local | alternate
    authentication_provider: normal
```

## Configuration File Choice
The configuration file is selected via vars:
``` yml
    # reeform_type: general | named
    freeform_type: general
```

``` yml
  - name: Load Configuration for {{ inventory_hostname }} {{ ansible_date_time.iso8601 }}
    ios_config:
      backup: yes
      src: cfg/{{ inventory_hostname }}.cfg
      match: none
      save_when : changed
    when: (run_type == "push") or
          (run_type == "both") and 
          (freeform_type == "named") 

  - name: Load General Configuration {{ ansible_date_time.iso8601 }}
    ios_config:
      backup: yes
      src: cfg/ios_freeform.cfg
      match: none
      save_when : changed
    when: (run_type == "push") or
          (run_type == "both") and 
          (freeform_type == "general") 
```

