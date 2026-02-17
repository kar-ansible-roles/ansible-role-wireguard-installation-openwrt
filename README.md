Role Name
=========
[![OpenWrt](https://img.shields.io/badge/OpenWrt-24.10.5-brightgreen?logo=openwrt)](https://openwrt.org/)

Ansible role for installing and configuring a Wireguard server with the option to forward ports for a reverse proxy server and the option to close access to ports 443, 80 and 22 on the WAN.

Requirements
------------

Ssh access to OpenWRT is required before launching the role. If the configuration takes place on the proxmox server, it is necessary to create a virtual machine with OpenWRT, temporarily open port 22 in the firewall for WAN, and interfaces for determining LAN and WAN must be created and configured.,

Role Variables
--------------

| Variable | Default | Description |
|----------|---------|-------------|
| `wg_interface` | `wg0` | Name of creation wireguard interface |
| `wg_port` | `51820` | Wireguard listening port 51820 |
| `wg_network` | `10.10.10.1/24` | CIDR Wireguard Server |
| `wg_client_ip` | `10.10.10.70/32` | CIDR Wireguard peer(client) |
| `openwrt_lan_network` | `10.10.0.0/24` | LAN network |
| `client_conf_path` | "{{ playbook_dir }}/openwrt_{{ wg_interface }}.conf" | Place to export wireguard configuaration |
| `block_wan_access` | `true` | Blocking of external control ports of the router |


Dependencies
------------
There are no hard dependencies on other roles. There is a role for managing template preparation for the OpenWRT VM in proxmox: https://github.com/kar-ansible-roles/ansible-role-openwrt-prepare-template-proxmox . After completing it, you need to create a virtual machine from the template and run it.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:
```yml
- hosts: servers
  become: yes
  roles:
    - role: ansible-role-wireguard-installation-openwrt
      vars: 
        - client_conf_path: "{{ playbook_dir }}/custom.conf"
```

License
-------

MIT License

Author Information
------------------

This role was created in 2026 by [Karen Saakov](www.linkedin.com/in/karen-saakov-4905571a1) DevOps Engineer Karo LLC.
