Ansible Role: init_password
=========

本 Role 用于在在 CentOS, Ubuntu 和 AmazonLinux 服务器上初始化数据库以及应用程序的随机密码

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
| init_db | 参考下方  | 字典 | 否 |
| init_application | [...]   | 字典 | 否 |

注意：
1. init_application 和 init_application 初始化在项目主变量文件中统一修改。
2. 默认数据库管理员密码初始化范例
    ```
    init_db: 
      mongodb:
        user: root
        password: "123456"

    init_db: 
      mysql:
        user: root
        password: "123456"

    init_db: 
      postgresql:
        user: root
        password: "123456"
    ```
3. 默认应用管理员密码初始范例
    ```
      jenkins:
        random_password: True
        default_account: jenkins
        default_password: 123456
        random_password_method: 
          - jenkins change password $new_password
    ```


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

## FAQ

#### 当前的随机密码采用什么算法？

当前采用 `pwgen -ncCs 10 1` 生成随机密码

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

