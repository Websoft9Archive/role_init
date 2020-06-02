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
2. 默认数据库管理员密码初始化范例（程序已经自动处理随机密码脚本与其service的先后关系）
    ```
    init_db: 
      mongodb:
        admin: root
        users: ["react"]
        password: "123456"

    init_db: 
      mysql:
        admin: root
        users: ["wordpress","discuz"]
        password: "123456"

    init_db: 
      postgresql:
        admin: root
        users: ["wordpress","discuz"]
        password: "123456"
        
    init_db: 
      mysql:
        admin: root
        users: ["discuz"]
        password: "123456"  
        config_paths:
        - /data/wwwroot/discuz/upload/config/config_global_default.php
        
    init_db:   
      docker:
        password: "123456"
        path: /data/compose.yml
    ```
3. 默认应用管理员密码初始范例（此方案只适用于修改文件）
    ```
    init_application:
      wordpress:
        username: admin
        password: "123456"
        service_before:
        service_after: php-fpm.service
        config_paths: 
          - /data/wwwroot/wordpress/wp-config.php
          - /data/wwwroot/wordpress/wp-config2.php
        commands: 
          - wp change -u default_account -p default_password to $new_password
          - systemctl restart xxxx  
          
       discuz:   
         username: admin
         password: "123456"
         service_before:
         service_after: php-fpm.service
         config_paths: 
            - /data/wwwroot/wordpress/wp-config.php
            - /data/wwwroot/wordpress/wp-config3.php
         commands: 
            - wp change -u default_account -p default_password to $new_password
            - systemctl restart xxxx  
       
    init_application:
      grafana:
        username: admin
        password: "admin"
        service_before:
        service_after: grafana-server
        commands: 
          - grafana-cli admin reset-admin-password $new_password        
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

#### 采用哪种方式实现开机运行一次？

systemd

