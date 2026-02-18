Role Name
=========
[![OpenWrt](https://img.shields.io/badge/OpenWrt-24.10.5-brightgreen?logo=openwrt)](https://openwrt.org/)

Ansible role for installing and configuring a Wireguard server with the option to forward ports for a reverse proxy server and the option to close access to ports 443, 80 and 22 on the WAN.

Requirements
------------

Ssh access to OpenWRT is required before launching the role. If the configuration takes place on the proxmox server, it is necessary to create a virtual machine with OpenWRT, temporarily open port 22 in the firewall for WAN with name of rule 'breach', and interfaces for determining LAN and WAN must be created and configured.,

Role Variables
--------------

| Variable | Default | Description |
|----------|---------|-------------|
| `block_wan_access` | `false` | Blocking of external control ports of the router |
| `wg_server_interface` | `wg0` | Name of creation wireguard interface |
| `wg_server_port` | `51820` | Wireguard listening port 51820 |
| `wg_network` | `10.10.10.1/24` | CIDR Wireguard Server |
| `wg_client_ip` | `10.10.10.70/32` | CIDR Wireguard peer(client) |
| `openwrt_lan_network` | `10.10.0.0/24` | LAN network |
| `wg_client_conf_path` | "{{ playbook_dir }}/openwrt_{{ wg_server_interface }}.conf" | Place to export wireguard configuaration |
| `enable_port_forward` | `false` | Enable port forwarding to reverse proxy |
| `reverse_proxy_ip` | `10.10.10.1` | Add reverse proxy ip (use only with enable_port_forward: true) |
| `forwarding_ports` | `[HTTP: [...], HTTPS: []]` | Add ports to forwarding (use only with enable_port_forward: true), correct define below |

```yml
forwarding_ports:
  - name: "HTTP"
    src_port: 80
    dest_port: 80
    dest_ip: "{{ reverse_proxy_ip }}"

  - name: "HTTPS"
    src_port: 443
    dest_port: 443
    dest_ip: "{{ reverse_proxy_ip }}"
```

Dependencies
------------
There are no hard dependencies on other roles. There is a role for managing template preparation for the OpenWRT VM in proxmox: https://github.com/kar-ansible-roles/ansible-role-openwrt-prepare-template-proxmox . After completing it, you need to create a virtual machine from the template and run it.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:
```yml
- hosts: servers
  become: yes
  pre_tasks:
    - name: Ensure Dropbear authorized_keys directory exists
      ansible.builtin.file:
        path: /etc/dropbear
        state: directory
        mode: '0700'
        owner: root
        group: root

    - name: Deploy public key to Dropbear
      ansible.builtin.copy:
        content: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
        dest: /etc/dropbear/authorized_keys
        mode: '0600'
        owner: root
        group: root
        backup: yes

    - name: Restart Dropbear service
      ansible.builtin.service:
        name: dropbear
        state: restarted
  roles:
    - role: ansible-role-wireguard-installation-openwrt
      vars: 
        - block_wan_access: true
```

License
-------

MIT License

Author Information
------------------

This role was created in 2026 by [Karen Saakov](https://linkedin.com/in/karen-saakov-4905571a1) DevOps Engineer Karo LLC.
