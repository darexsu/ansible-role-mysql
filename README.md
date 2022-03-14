# Ansible role MySQL

[![CI Molecule](https://github.com/darexsu/ansible-role-mysql/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-mysql/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/58299?color=blue&label=downloads)

  - Role:
      - [platforms](#platforms)
      - [install](#install)
      - [behaviour](#behaviour)
  - Playbooks (short version):
      - [install and configure: MySQL](#install-and-configure-mysql-short-version)
          - [install: MySQL from official repo](#install-mysql-from-official-repo-short-version)
          - [install: MySQL from third-party repo](#install-mysql-from-third-party-repo-short-version)
          - [configure: server.conf](#configure-serverconf-short-version)
  - Playbooks (full version):
      - [install and configure: MySQL](#install-and-configure-mysql-full-version)
          - [install: MySQL from official repo](#install-mysql-from-official-repo-full-version)
          - [install: MySQL from third-party repo](#install-mysql-from-third-party-repo-full-version)
          - [configure: server.conf](#configure-serverconf-full-version)

### Platforms

|  Testing         |  Official repo     |  Third-party repo |
| :--------------: | :----------------: | :-------------:   |
| Debian 11        |  No                |    mysql.com     |
| Debian 10        |  No                |    mysql.com     |
| Ubuntu 20.04     |  No                |    mysql.com     |
| Ubuntu 18.04     |  No                |    mysql.com     |
| Oracle Linux 8   |  mysql 8.0         |    mysql.com     |
| Rocky Linux 8    |  mysql 8.0         |    mysql.com     |

### Install

```
ansible-galaxy install darexsu.mysql --force
```

### Behaviour

Replace or Merge dictionaries (with "hash_behaviour=replace" in ansible.cfg):
```
# Replace             # Merge
---                   ---
  vars:                 vars:
    dict:                 merge:
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"

# How does merge work?
Your vars [host_vars]  -->  default vars [current role] --> default vars [include role]
  
  dict:          dict:              dict:
    a: "1" -->     a: "1"    -->      a: "1"
                   b: "2"    -->      b: "2"
                                      c: "3"
    
```

##### Install and configure: MySQL (short version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        src: "distribution"
      # MySQL -> install
      mysql_install:
        enabled: true
      # MySQL -> config
      mysql_config:
        enabled: true
        skip-grant-tables: false
        vars:
          pid-file: "{{ mysql_const[ansible_os_family]['pid-file'] }}"
          socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
          datadir: "{{ mysql_const[ansible_os_family]['datadir'] }}"
          log-error: "{{ mysql_const[ansible_os_family][mysql.src]['log-error'] }}"
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```

##### Install: MySQL from official repo (short version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        src: "distribution"
      # MySQL -> install
      mysql_install:
        enabled: true
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```
##### Install: MySQL from third-party repo (short version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        src: "third_party"
        version: "8.0"
      # MySQL -> install
      mysql_install:
        enabled: true
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```

##### Configure: server.conf (short version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
      # MySQL -> config
      mysql_config:
        enabled: true
        file: "{{ mysql_const[ansible_os_family]['config_file']}}"
        src: "mysql__server.conf.j2"
        backup: false
        skip-grant-tables: false
        vars:
          pid-file: "{{ mysql_const[ansible_os_family]['pid-file'] }}"
          socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
          datadir: "{{ mysql_const[ansible_os_family]['datadir'] }}"
          log-error: "{{ mysql_const[ansible_os_family][mysql.src]['log-error'] }}"
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```
##### Install and configure: MySQL (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        src: "distribution"
        version: ""
        service:
          state: "started"
          enabled: true
      # MySQL -> install
      mysql_install:
        enabled: true
        packages:
          Debian: [mysql-server]
          RedHat: [mysql-server]
        dependencies:
          Debian: [gnupg2, python3, python3-pymysql]
          RedHat: [python3, python3-PyMySQL]
      # MySQL -> config
      mysql_config:
        enabled: true
        file: "{{ mysql_const[ansible_os_family]['config_file']}}"
        src: "mysql__server.conf.j2"
        backup: false
        skip-grant-tables: false
        vars:
          pid-file: "{{ mysql_const[ansible_os_family]['pid-file'] }}"
          socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
          datadir: "{{ mysql_const[ansible_os_family]['datadir'] }}"
          log-error: "{{ mysql_const[ansible_os_family][mysql.src]['log-error'] }}"
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```

##### Install: MySQL from official repo (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        src: "distribution"
        version: ""
        service:
          state: "started"
          enabled: true
      # MySQL -> install
      mysql_install:
        enabled: true
        packages:
          Debian: [mysql-server]
          RedHat: [mysql-server]
        dependencies:
          Debian: [gnupg2, python3, python3-pymysql]
          RedHat: [python3, python3-PyMySQL]
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```
##### Install: MySQL from third-party repo (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        src: "third_party"
        version: "8.0"
        service:
          state: "started"
          enabled: true
      # MySQL -> install
      mysql_install:
        enabled: true
        packages:
          Debian: [mysql-server]
          RedHat: [mysql-server]
        dependencies:
          Debian: [gnupg2, python3, python3-pymysql]
          RedHat: [python3, python3-PyMySQL]
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```

##### Configure: server.conf (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        src: "distribution"
        version: ""
        service:
          state: "started"
          enabled: true
      # MySQL -> config
      mysql_config:
        enabled: true
        file: "{{ mysql_const[ansible_os_family]['config_file']}}"
        src: "mysql__server.conf.j2"
        backup: false
        skip-grant-tables: false
        vars:
          pid-file: "{{ mysql_const[ansible_os_family]['pid-file'] }}"
          socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
          datadir: "{{ mysql_const[ansible_os_family]['datadir'] }}"
          log-error: "{{ mysql_const[ansible_os_family][mysql.src]['log-error'] }}"
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```