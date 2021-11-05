# Playbook Example

The following provides a simple example of a complete Netbox configuration using 
Ansible. It is *not suitable for production* and is intended to only demonstrate how
the required components can be brought together.

At the completion of this play netbox will be available at: `http://<host_ip>:8080`

## Install Roles

    $ ansible-galaxy install jvoss.netbox geerlingguy.postgresql geerlingguy.redis caddy_ansible.caddy_ansible

## Host variables

    # netbox.example.com.yml

    netbox_home: /opt/netbox
    netbox_version_tag: v3.0.9
    netbox_db_username: netbox
    netbox_db_password: password
    netbox_secret_key: "-nit@y=2#)u2dz-e(de1$t*4mxpy4d9o(b4j5xf@6!ql=r-14o"

    netbox_superusers:        
      - username: admin
        password: admin
        email: admin@example.com

    # caddy_ansible.caddy_ansible configuration
    caddy_config: |
      :8080 {
        route /static* {
          uri strip_prefix /static
          root * /opt/netbox/current/netbox/static
          file_server
        }

        reverse_proxy http://127.0.0.1:8001
      }

    # geerlingguy.postgresql configuration
    postgresql_users:
      - name: "{{ netbox_db_username }}"
        password: "{{ netbox_db_password }}"
        db: "{{ netbox_db_name }}"
    postgresql_databases:
      - name: "{{ netbox_db_name }}"
        owner: "{{ netbox_db_username }}"

## Playbook

    # playbook-netbox.yml

    - hosts: netbox.example.com
      gather_facts: yes
      become: yes

      roles:
        - role: geerlingguy.postgresql
        - role: geerlingguy.redis
        - role: jvoss.netbox
        - role: caddy_ansible.caddy_ansible

## Notes

For testing on minimal installations be sure the following tools are available on the
host: `python3` `sudo` `ssh` `unzip` `tar`

CentOS minimal example:

    $ yum install python3 sudo openssh-server unzip tar
    $ service sshd start
