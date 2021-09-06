# Docker

## Routing traffic for some containers over a specific interface

Use case: you want to route _all_ traffic from some containers over a VPN tunnel, regardless of the system's routing table.
In combination with a proxy server, this can be used to enable URL-based routing over a VPN.

Distilled from https://maxammann.org/posts/2020/04/routing-docker-container-over-vpn/

You need:
* A network to run the containers in:
    ```bash
    docker network create \
      # set the bridge name to something readable i/o a random string
      -o 'com.docker.network.bridge.name'='vpn' \
      # optional; an available one will be assigned if not specified
      --subnet=172.18.0.1/16 \
      vpn
    ```
* A routing table for the network
    ```bash
    sudo echo "100     vpn" >> /etc/iproute2/rt_tables
    ```
* A VPN tunnel

Set up the rules and routes:
```bash
# make sure the gateway is routed correctly
# this should be the case after starting the VPN connection
# if not: ip route add <VPN endpoint> via dev eth0
docker_net=172.18.0.0/16
local_net=192.168.1.0/24
eth_dev=enp2s0f1
tun_dev=tun0

# all containers on the vpn network should use the vpn routing table
ip rule add from ${docker_net} lookup vpn
# default route for the vpn
ip route add default dev ${tun_dev} table vpn
# required for OpenVPN
# see http://www.dd-wrt.com/phpBB2/viewtopic.php?t=277001
ip route add 0.0.0.0/1 dev ${tun_dev} table vpn
ip route add 128.0.0.0/1 dev ${tun_dev} table vpn
# local traffic is the only thing that shouldn't use the VPN
ip route add ${local_net} dev ${eth_dev} table vpn
# everything to the docker net should use the vpn table
ip route add ${docker_net} dev vpn table vpn
```

To run a container within the `vpn` network, specify `--network vpn` when starting it.

If needed, you can explicitly block traffic from the vpn network from being routed over the main interface using iptables.
