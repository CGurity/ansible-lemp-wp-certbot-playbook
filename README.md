# Nginx + PHP + MySQL + Wordpress + Certbot Playbook
Ansible playbook and roles to install any of the following depending on configuration
- A static HTML site
- A PHP based site
- A PHP + MySQL site
- A Wordpress site
- All of the above with a Let's Encrypt SSL/TLS certificate

This playbook is based on the [WordPress + Nginx Ansible Playbook](https://github.com/tucsonlabs/ansible-playbook-wordpress-nginx)

### Considerations

- For every site the playbook will asociate any domain to be served with the exact domain configured in global vars and it "www." version
- For php/wordpress sites a new user will be created to handle all the php operations for security reasons, if one website is compromised in the php level, it won't have access to the other instances minimizing the damage.
- For each website with MySQL a user will be created with a random password, this information  will be reported back to the user for further use.
- All the static websites files will be stored in ```/var/www/static/site_domain/```, all the php websites files in ```/var/www/php/site_domain/``` and the wordpress files will be in ```/var/www/wordpress/site_domain/```.
- For the wordpress site the playbook will install WP-CLI to manage unnatended install, set up cronjobs to update the Wordpress core, plugins and themes daily and install Wordfence.

### Requirements
- Ansible 2.0.0 or newer
- Ubuntu 16.04 or Debian 9 (could work on other Debian based distros)
- SSH access to the target with root user (configured for key access)
- Point your DNS records to the IP of your target server (remember that the playbook considers also the www.domain.com url)

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
