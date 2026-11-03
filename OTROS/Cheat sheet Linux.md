# Comandos Linux

# traceroute

| Option         | Description                                         |
| -------------- | --------------------------------------------------- |
| -4             | Force IPv4.                                         |
| -6             | Force IPv6.                                         |
| -n             | Do not resolve IP addresses to hostnames (faster).  |
| -m <max_hops>  | Set maximum number of hops (default: 30).           |
| -w <timeout>   | Set timeout for each probe in seconds (default: 5). |
| -q <nqueries>  | Set number of probes per hop (default: 3).          |
| -f <first_ttl> | Set initial time-to-live (TTL) value (default: 1).  |
| -I             | Use ICMP ECHO instead of UDP for probes.            |
| -T             | Use TCP SYN packets for probes.                     |
| -p <port>      | Specify destination port for UDP/TCP probes.        |

# IP

- `OBJECT`: link, address, route, neighbour, etc.
- `COMMAND`: show, add, delete, etc.

## Objetos Comunes
| Objeto | Descripción |
|--------|-------------|
| `link` | Interfaces de red físicas/virtuales |
| `addr` | Direcciones IP de interfaces |
| `route` | Tabla de enrutamiento |
| `neigh` | Tabla ARP/neighbor |
| `maddr` | Direcciones multicast |
| `tunnel` | Interfaces de túnel |


# dhclient

| **Comando**        | **Descripción**                                        | **Ejemplo**                 |
| ------------------ | ------------------------------------------------------ | --------------------------- |
| `dhclient`         | Solicita una dirección IP DHCP en todas las interfaces | `sudo dhclient`             |
| `dhclient eth0`    | Solicita IP para interfaz específica                   | `sudo dhclient eth0`        |
| `dhclient -r`      | Libera la IP actual (release)                          | `sudo dhclient -r`          |
| `dhclient -r eth0` | Libera IP de interfaz específica                       | `sudo dhclient -r eth0`     |
| `dhclient -v`      | Modo verbose (muestra detalles)                        | `sudo dhclient -v eth0`     |
| `dhclient -d`      | Modo debug (información detallada)                     | `sudo dhclient -d`          |
| `-v`, `--verbose`  | Muestra información detallada                          | `dhclient -v`               |
| `-d`, `--debug`    | Modo debug extenso                                     | `dhclient -d`               |
| `-q`, `--quiet`    | Modo silencioso                                        | `dhclient -q`               |
| `-t`, `--timeout`  | Tiempo de espera en segundos                           | `dhclient -t 60`            |
| `-1`               | Solo una solicitud (no renovar)                        | `dhclient -1 eth0`          |
| `-cf`              | Archivo de configuración                               | `dhclient -cf /ruta/config` |

## Comandos Principales

### Mostrar Información
| Comando | Descripción |
|---------|-------------|
| `ip link show` | Muestra todas las interfaces de red |
| `ip addr show` | Muestra direcciones IP de todas las interfaces |
| `ip route show` | Muestra la tabla de enrutamiento |
| `ip neigh show` | Muestra la tabla ARP |
| `ip -s link show` | Muestra estadísticas de interfaces |
| `ip -br addr` | Formato breve de direcciones IP |

### Configurar Interfaces
| Comando | Descripción |
|---------|-------------|
| `ip link set dev eth0 up` | Activa interfaz eth0 |
| `ip link set dev eth0 down` | Desactiva interfaz eth0 |
| `ip link set dev eth0 mtu 1500` | Establece MTU |
| `ip link set dev eth0 name enp0s3` | Renombra interfaz |

### Configurar Direcciones IP
| Comando | Descripción |
|---------|-------------|
| `ip addr add 192.168.1.10/24 dev eth0` | Agrega IP a interfaz |
| `ip addr del 192.168.1.10/24 dev eth0` | Elimina IP de interfaz |
| `ip addr flush dev eth0` | Elimina todas las IPs de interfaz |
| `ip addr show dev eth0` | Muestra IPs de eth0 |

### Configurar Rutas
| Comando | Descripción |
|---------|-------------|
| `ip route add 192.168.2.0/24 via 192.168.1.1` | Agrega ruta |
| `ip route add default via 192.168.1.1` | Agrega ruta por defecto |
| `ip route del 192.168.2.0/24` | Elimina ruta |
| `ip route flush cache` | Limpia caché de rutas |

### Gestión ARP/Neighbor
| Comando | Descripción |
|---------|-------------|
| `ip neigh show` | Muestra tabla ARP |
| `ip neigh add 192.168.1.100 lladdr 00:11:22:33:44:55 dev eth0` | Agrega entrada ARP |
| `ip neigh del 192.168.1.100 dev eth0` | Elimina entrada ARP |

## Ejemplos Prácticos

### Configuración Básica de Interfaz
```bash
# Activar interfaz y asignar IP
ip link set dev eth0 up
ip addr add 192.168.1.100/24 dev eth0
ip route add default via 192.168.1.1
# Windows
## tracert

| Option           | Description                                                 |
| ---------------- | ----------------------------------------------------------- |
| -d               | Do not resolve IP addresses to hostnames (faster).          |
| -h <max_hops>    | Set maximum number of hops (default: 30).                   |
| -j <host-list>   | Specify loose source routing along a host list.             |
| -w <timeout>     | Set timeout for each reply in milliseconds (default: 4000). |
| -R               | Trace round-trip path (IPv6 only).                          |
| -S <source_addr> | Specify source address (IPv6 only).                         |
| -4               | Force IPv4.                                                 |
| -6               | Force IPv6.                                                 |
```

# Ejemplos

Reiniciar servicio NetworkManager para volver a pedir una ip al DHCP server
```shell
sudo systemctl restart NetworkManager.service
```

