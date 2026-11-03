# IP

|**Command**|**Description**|
|---|---|
|`ip addr`|Display all IP addresses and their properties for network interfaces.|
|`ip addr show dev <interface>`|Show IP address details for a specific interface (e.g., `eth0`, `wlan0`).|
|`ip addr add <ip>/<mask> dev <interface>`|Assign an IP address and subnet mask to an interface (e.g., `192.168.1.100/24`).|
|`ip addr del <ip>/<mask> dev <interface>`|Remove an IP address from an interface.|
|`ip link`|Display the status of all network interfaces.|
|`ip link set <interface> up`|Enable a specific network interface.|
|`ip link set <interface> down`|Disable a specific network interface.|
|`ip route`|Display the routing table.|
|`ip route add default via <gateway>`|Set the default gateway (e.g., `192.168.1.1`).|
|`ip route add <network>/<mask> via <gateway>`|Add a route to a specific network via a gateway.|
|`ip route del <network>/<mask>`|Delete a specific route from the routing table.|
|`ip neigh`|Display the ARP table (neighbor entries).|
|`ip neigh add <ip> lladdr <mac> dev <interface>`|Add an ARP entry for an IP and MAC address.|
|`ip link show`|List all network interfaces and their properties.|
|`ip -s link`|Display statistics for all network interfaces.|
|`ip monitor`|Monitor real-time changes to network state (addresses, routes, etc.).|
|`ip addr flush dev <interface>`|Remove all IP addresses from a specific interface.|
|`ip maddr`|Display multicast addresses for interfaces.|
|`ip maddr add <mcast_addr> dev <interface>`|Add a multicast address to an interface.|
|`ip tunnel`|Display tunnel interfaces and their configurations.|
|`ip link set <interface> mtu <size>`|Set the Maximum Transmission Unit (MTU) for an interface (e.g., `1500`).|
