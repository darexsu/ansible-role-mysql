# Ansible role MySQL

[![CI Molecule](https://github.com/darexsu/ansible-role-mysql/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-mysql/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/58299?color=blue&label=downloads)

  - Role:
      - [platforms](#platforms)
      - [install](#install)
      - [merge behaviour](#merge-behaviour)
  - Playbooks (merge version):
      - [install and configure: MySQL](#install-and-configure-mysql-merge-version)
          - [install: MySQL, repository: distribution](#install-mysql-repository-distribution-repo-merge-version)
          - [install: MySQL, repository: mysql](#install-mysql-repository-mysql-merge-version)
          - [configure: server.conf](#configure-serverconf-merge-version)
  - Playbooks (full version):
      - [install and configure: MySQL](#install-and-configure-mysql-full-version)
          - [install: MySQL, repository: distribution](#install-mysql-repository-distribution-full-version)
          - [install: MySQL, repository: mysql](#install-mysql-repository-mysql-full-version)
          - [configure: server.conf](#configure-serverconf-full-version)

### Platforms

|  Testing         | repo: distribution |  repo: mysql     |
| :--------------: | :----------------: | :-------------:  |
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

### Merge behaviour

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

##### Install and configure: MySQL (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        repo: "mysql"
      # MySQL -> install
      mysql_install:
        enabled: true
      # MySQL -> config
      mysql_config:
        enabled: true
        skip-grant-tables: false
        data:
          pid-file: "{{ mysql_const[ansible_os_family]['pid-file'] }}"
          socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
          datadir: "{{ mysql_const[ansible_os_family]['datadir'] }}"
          log-error: "{{ mysql_const[ansible_os_family][mysql.repo]['log-error'] }}"
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```

##### Install: MySQL, repository: distribution (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        repo: "distribution"
      # MySQL -> install
      mysql_install:
        enabled: true
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```
##### Install: MySQL, repository: mysql (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        repo: "mysql"
        version: "8.0"
      # MySQL -> install
      mysql_install:
        enabled: true
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```

##### Configure: server.conf (merge version)
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
        src: "mysql_server.conf.j2"
        backup: false
        skip-grant-tables: false
        data:
          pid-file: "{{ mysql_const[ansible_os_family]['pid-file'] }}"
          socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
          datadir: "{{ mysql_const[ansible_os_family]['datadir'] }}"
          log-error: "{{ mysql_const[ansible_os_family][mysql.repo]['log-error'] }}"
  
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
        repo: "mysql"
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
      # MySQL -> config
      mysql_config:
        enabled: true
        file: "{{ mysql_const[ansible_os_family]['config_file']}}"
        src: "mysql_server.conf.j2"
        backup: false
        skip-grant-tables: false
        data:
          pid-file: "{{ mysql_const[ansible_os_family]['pid-file'] }}"
          socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
          datadir: "{{ mysql_const[ansible_os_family]['datadir'] }}"
          log-error: "{{ mysql_const[ansible_os_family][mysql.repo]['log-error'] }}"
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```

##### Install: MySQL, repository: distribution (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        repo: "distribution"
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
##### Install: MySQL, repository: mysql (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # MySQL
      mysql:
        enabled: true
        repo: "mysql"
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
        repo: "distribution"
        version: ""
        service:
          state: "started"
          enabled: true
      # MySQL -> config
      mysql_config:
        enabled: true
        file: "{{ mysql_const[ansible_os_family]['config_file']}}"
        src: "mysql_server.conf.j2"
        backup: false
        skip-grant-tables: false
        data:
          pid-file: "{{ mysql_const[ansible_os_family]['pid-file'] }}"
          socket: "{{ mysql_const[ansible_os_family]['socket'] }}"
          datadir: "{{ mysql_const[ansible_os_family]['datadir'] }}"
          log-error: "{{ mysql_const[ansible_os_family][mysql.repo]['log-error'] }}"
  
  tasks:
    - name: include role darexsu.mysql
      include_role: 
        name: darexsu.mysql
```