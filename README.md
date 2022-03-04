# Ansible role MySQL

[![CI Molecule](https://github.com/darexsu/ansible-role-mysql/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-mysql/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57634?color=blue&label=downloads)

|  Testing         |  Debian            |  Ubuntu         |  Rocky Linux  | Oracle Linux |
| :--------------: | :----------------: | :-------------: | :-----------: | :----------: |
| Distro version   |  10, 11            | 18.04, 20.04    |  8            | 8            |
| Third-party repo |  mysql.com         |   mysql.com     |   mysql.com   |  mysql.com   | 

### 1) Install role from Galaxy
```
ansible-galaxy install darexsu.mysql --force
```

### 2) Example playbooks: 

- [full playbook](#full-playbook)
  - install
    - [install MariaDB from official repo](#install-mysql-from-official-repo)
    - [install MariaDB {version} from third-party repo](#install-mysql-from-third-party-repo)
  - config
    - [configure server.cnf](#configure-servercnf)

Role behaviour: Replace or Merge (with "hash_behaviour=replace" in ansible.cfg):
```
# Replace             # Merge
[host_vars]           [host_vars]
---                   ---
  vars:                 vars:
    dict:                 merge:
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"

# Role recursive merge:
[host_vars]     [current role]    [include_role]
  
  dict:          dict:              dict:
    a: "1" -->     a: "1"    -->      a: "1"
                   b: "2"    -->      b: "2"
                                      c: "3"
    
```

##### full playbook
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             mysql
      mysql:
        enabled: true
        src: "distribution"
        version: ""
        service:
          state: "started"
          enabled: true
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             remove mysql
      mysql_remove:
        enabled: false
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             install mysql
      mysql_install:
        enabled: true
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             config server.cnf
      mysql_config:
        enabled: false
        file: "{{ mysql_const[ansible_os_family]['config_file']}}"
        src: "mysql__server.conf.j2"
        backup: false
        skip-grant-tables: false
        vars:         
          pid-file: "{{ mysql_const[ansible_os_family]['pid-file'] }}"
          socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
          datadir: "{{ mysql_const[ansible_os_family]['datadir'] }}"
          log-error: "{{ mysql_const[ansible_os_family]['log-error'] }}"
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```

##### install mysql from official repo
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             mysql 
      mysql:
        enabled: true  
        src: "distribution"   # <-- enable official repo
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             install mysql
      mysql_install:
        enabled: true
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```
##### install mysql from third-party repo
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             mysql
      mysql:
        enabled: true  
        src: "third_party"    # <-- enable third_party repo
        version: "8.0"       # <-- set version
        service:
          state: "started"
          enabled: true    
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             install mysql
      mysql_install:
        enabled: true
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```

##### configure server.cnf
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             mysql 
      mysql:
        enabled: true
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             config server.cnf 
      mysql_config:
        enabled: false
        file: "{{ mysql_const[ansible_os_family]['config_file']}}"
        src: "mysql__server.conf.j2"
        backup: false
        skip-grant-tables: false
        vars:         
          pid-file: "{{ mysql_const[ansible_os_family]['pid-file'] }}"
          socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
          datadir: "{{ mysql_const[ansible_os_family]['datadir'] }}"
          log-error: "{{ mysql_const[ansible_os_family]['log-error'] }}"
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```
