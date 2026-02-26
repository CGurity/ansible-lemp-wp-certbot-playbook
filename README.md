# Nginx + PHP + MySQL + Wordpress + Certbot Playbook
Ansible playbook and roles to install any of the following depending on configuration
- A static HTML site
- A PHP based site
- A PHP + MySQL site
- A Wordpress site
- All of the above with a Let's Encrypt SSL/TLS certificate

This playbook is based on the [WordPress + Nginx Ansible Playbook](https://github.com/tucsonlabs/ansible-playbook-wordpress-nginx)

### Considerations

- For every site the playbook will asociate any domain to be served with the exact domain configured in global vars and its "www." version. The "www." version can be skipped in the playbook configuration, this is especially useful if configuring sa subdomain.
- For php/wordpress sites a new user will be created to handle all the php operations for security reasons, if one website is compromised in the php level, it won't have access to the other instances, reducing the damage.
- For each website with MySQL a user will be created with a random password, this information  will be reported back to the user for further use.
- All websites files will be stored in ```/var/www/site_domain/```.
- For the wordpress site the playbook will install WP-CLI to manage an unnatended install, set up cronjobs to update the Wordpress core, plugins and themes daily and install Wordfence.
- **IMPORTANT:** For all PHP sites (including WordPress), the resulting permission structure for the directory is set up to "u=rwX,g=rX,o=rX" recursively. Please be mindful of this and check the documentation of the tool you want to deploy to make sure if you would need to adjust permissions further to configure your tool correctly and securely.
- **ALSO IMPORTANT:** Keep in mind that certain tools will require especial configurations on the Nginx configuration. Again, please review the documentation of the tool you want to deploy to ensure you consider any further changes in the configuration. Overlooking this might result in serious security issues, like exposing secrets and unintended interfaces.

### Requirements
- Ansible 2.0.0 or newer
- Ubuntu 16.04 or Debian 9 (could work on other Debian based distros, tested the last time in Ubuntu 24.04)
- SSH access to the target with root user (configured for ssh key access)
- Point your DNS records of the (sub)domain to the IP of your target server (remember that the playbook also might consider the www.domain.com url)

### Instructions:

1. Clone the repository

```
$ git clone https://github.com/cguerrave/ansible-lemp-wp-certbot-playbook.git
$ cd /ansible-lemp-wp-certbot-playbook
```

2. Depending of what do you want to install and configure, set the relevant variables and comment the corresponding lines in the playbook.yml file, some examples below

- For a static site with HTTPS:
```yml
roles:
  - site-static
  # - site-php
  # - site-php-mysql
  # - site-wordpress
  - site-certbot
```

- For a wordpress site with HTTPS:
```yml
roles:
  - site-static
  - site-php
  - site-php-mysql
  - site-wordpress
  - site-certbot
```

- For a php site and a MySQL databse without HTTPS:
```yml
roles:
  - site-static
  - site-php
  - site-php-mysql
  # - site-wordpress
  # - site-certbot
```

3. Copy the file ```hosts.example``` to ```hosts```

```
cp hosts.example hosts
```
and edit.
```
[webservers]
1.2.3.4 ansible_ssh_user=root ansible_ssh_private_key_file=$HOME/.ssh/id_rsa
```

4. run the playbook using ```ansible-playbook playbook.yml -i hosts```.

5. If you selected a website with MySQL look in the folder ```credentials/``` for the MySQL users and passwords for the users root and the specific site database user.

6. Access your server IP or domain to check that everything is in order.
