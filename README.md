Ansible Role: init_password
=========

本 Role 用于在在 CentOS, Ubuntu 和 AmazonLinux 服务器上初始化随机密码

## Requirements

运行本 Role，请确认符合如下的必要条件：

| **Items**      | **Details** |
| ------------------| ------------------|
| Operating system | CentOS7.x Ubuntu AmazonLinux |
| Python 版本 | Python2  |
| Python 组件 |    |
| Runtime | MySQL, MariaDB, PostgreSQL, MongoDB |


## Related roles

本 Role 在语法上引用了主变量，程序运行时需要确保已经运行： mysql | mariadb | postgresql | mongodb 等 Role。以 mysql 为例：

```
  roles:
   - {role: role_common, tags: "role_common"}   
   - {role: role_cloud, tags: "role_cloud"}
   - {role: role_mysql, tags: "role_mysql"}
   - {role: role_docker, tags: "role_docker"}
   - {role: role_docker_phpmyadmin, tags: "role_docker_phpmyadmin"}
   - {role: role_init_password, tags: "role_init_password"} 
```


## Variables

本 Role 主要变量以及使用方法如下：

| **Items**      | **Details** | **Format**  | **是否初始化** |
| ------------------| ------------------|-----|-----|
| init_db | mysql/mariadb/postgresql/moongodb[ user: username,  password: password ]   | 字典 | 否 |
| init_application | [...]   | 字典 | 否 |

注意：
1. init_application 和 init_application 初始化在项目主变量文件中统一修改。



## Example

```
- name: MySQL
  hosts: all
  become: yes
  become_method: sudo 
  vars_files:
    - vars/main.yml 

  roles:
   - {role: role_common, tags: "role_common"}   
   - {role: role_cloud, tags: "role_cloud"}
   - {role: role_mysql, tags: "role_mysql"}
   - {role: role_docker, tags: "role_docker"}
   - {role: role_docker_phpmyadmin, tags: "role_docker_phpmyadmin"}
   - {role: role_init_password, tags: "role_init_password"}
   - {role: role_end, tags: "role_end"} 
```

## 说明

**随机密码生成统一采用**

  `pwgen -ncCs 10 1`

```
参数：
-c or –capitalize
密码中至少包含一个大写字母

-n or –numerals
密码中至少包含一个数字

-C
在列中打印生成的密码

-s or –secure
生成完全随机密码
```

### 数据库额外变量(用于创建数据库和用户)
```
额外创建的数据库用户
extra_db_user

额外创建的数据库
extra_db

额外创建的数据库密码
extra_db_password

mysql role 判断以上变量是否有值和变量是否定义,未定义变量或者变量未赋值将不会创建用户或者数据库 password.txt 模板将不会保存额外创建的数据库/用户/密码等信息
```


说明:  项目入口处加入变量

```
- name: main
  hosts: all
  become: yes
  become_method: sudo
  become_user: root
  vars:
    extra_db_user: db_user
    extra_db: db_name
    extra_db_password: db_pass

```


### 应用自己创建数据库或数据库用户,可以参考以下方式获取数据库信息,然后赋值变量

```
# 获取数据库名称,并存放在注册变量
- name: Get DB Name
  shell: cat /xx/xxx/xx
  register: db_name

# 获取数据库用户称,并存放在注册变量
- name: Get DB User
  shell: cat /xx/xxx/xx
  register: db_user

# 获取数据库用户密码,并存放在注册变量
- name: Get DB pass
  shell: cat /xx/xxx/xx
  register: db_pass

# 将注册变量重新赋值,以供项目中password.txt以及后面的initdb role使用
- set_fact:
    extra_db_user: '{{db_name.stdout}}'
    extra_db: '{{db_user.stdout}}'
    extra_db_password: '{{db_pass.stdout}}'



```

## FAQ


