# Private server to server network with ansible and wireguard

This role allowes you to deploy a fast, secure and provider agnostic private network between multiple servers. This is usefull for providers like [hetzner cloud](https://hetzner.cloud) that do not provide you with a private network or if you want to connect servers that are spread over multiple regions and providers.

## How

The role installs [wireguard](https://wireguard.com) on Debian or Ubuntu, creates a mesh between all servers by adding them all as peers and configures the wg-quick systemd service.

## Installation

Installation can be done using [ansible galaxy](https://galaxy.ansible.com/mawalu/wireguard_private_networking):

```
$ ansible-galaxy install mawalu.wireguard_private_networking
```

## Setup

Install this role, assign a `vpn_ip` variable to every host that should be part of the network and run the role. Plese make sure to allow the VPN port (default is 5888) in your firewall. Here as a small example configuration:

```yaml
# inventory host file

wireguard:
  hosts:
    1.1.1.1:
      vpn_ip: 10.1.0.1/32
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

## Additional configuration

There are a small number of role variables that can be overwritten.

```yaml
wireguard_port: "5888" # the port to use for server to server connections
wireguard_path: "/etc/wireguard" # location of all wireguard configurations

wireguard_network_name: "private" # the name to use for the config file and wg-quick

debian_enable_testing: true # if the debian testing repos should be added on debian machines
debian_pin_packages: true # if the pin configuration to limit the use of unstable repos should be created on debian machines

client_wireguard_path: "~/wg.conf" (defaul if blank string) # the path to config file which will be generated on localhost
```

## Contributing

Feel free to open issues or MRs if you find problems or have ideas for improvements. I'm especially open for MRs that add support for more operating systems.

