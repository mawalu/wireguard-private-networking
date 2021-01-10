# Private server to server network with ansible and wireguard 
 
[![Ansible Role](https://img.shields.io/ansible/role/d/33136)](https://galaxy.ansible.com/mawalu/wireguard_private_networking)
 
This role allowes you to deploy a fast, secure and provider agnostic private network between multiple servers. This is usefull for providers that do not provide you with a private network or if you want to connect servers that are spread over multiple regions and providers.

## How

The role installs [wireguard](https://wireguard.com) on Debian or Ubuntu, creates a mesh between all servers by adding them all as peers and configures the wg-quick systemd service.

## Installation

Installation can be done using [ansible galaxy](https://galaxy.ansible.com/mawalu/wireguard_private_networking):

```
$ ansible-galaxy install mawalu.wireguard_private_networking
```

## Setup

Install this role, assign a `vpn_ip` variable to every host that should be part of the network and run the role. Plese make sure to allow the VPN port (default is 5888) in your firewall. Here is a small example configuration:

Optionally, you can set a `public_addr` on each host. This address will be used to connect to the wireguard peer instead of the address in the inventory. Useful if you are configuring over a different network than wireguard is using. e.g. ansible connects over a LAN to your peer.

```yaml
# inventory host file

wireguard:
  hosts:
    1.1.1.1:
      vpn_ip: 10.1.0.1/32
      public_addr: "example.com" # optional
    2.2.2.2:
      vpn_ip: 10.1.0.2/32

```

```yaml
# playbook

- name: Configure wireguard mesh
  hosts: wireguard
  remote_user: root
  roles:
    - mawalu.wireguard_private_networking
```

```yaml
# playbook (with client config)
- name: Configure wireguard mesh
  hosts: wireguard
  remote_user: root
  vars:
    client_vpn_ip: 10.1.0.100
    client_wireguard_path: "~/my-client-config.conf"
  roles:
    - mawalu.wireguard_private_networking
```

## Additional configuration

There are a small number of role variables that can be overwritten.

```yaml
wireguard_port: "5888" # the port to use for server to server connections
wireguard_path: "/etc/wireguard" # location of all wireguard configurations

wireguard_network_name: "private" # the name to use for the config file and wg-quick

wireguard_mtu: 1500 # Optionally a MTU to set in the wg-quick file. Not set by default. Can also be set per host

debian_enable_backports: true # if the debian backports repos should be added on debian machines

# Raspberry Pi Zero support
# Needs kernel headers and manual compilation of wireguard, opt in via flag, install `community.general` collection
# Caution: Might trigger a reboot.
allow_build_from_source: true

wireguard_sources_path: "/var/cache" # Location to clone the WireGuard sources if manual build is required

client_vpn_ip: "" # if set an additional wireguard config file will be generated at the specified path on localhost
client_wireguard_path: "~/wg.conf" # path on localhost to write client config, if client_vpn_ip is set

# a list of additional peers that will be added to each server
wireguard_additional_peers:
  - comment: martin
    ip: 10.2.3.4
    key: your_wireguard_public_key
  - comment: other_network
    ip: 10.32.0.0/16
    key: their_wireguard_public_key
    keepalive: 20 
    endpoint: some.endpoint:2230 

wireguard_post_up: "iptables ..." # PostUp hook command
wireguard_post_down: "iptables"   # PostDown hook command
```

## Testing

This role has a small test setup that is created using [molecule](https://github.com/ansible-community/molecule). To run the tests follow the molecule [install guide](https://molecule.readthedocs.io/en/latest/installation.html), ensure that a docker daemon runs on your machine and execute `molecule test`.

## Contributing

Feel free to open issues or MRs if you find problems or have ideas for improvements. I'm especially open for MRs that add support for additional operating systems and more tests.

