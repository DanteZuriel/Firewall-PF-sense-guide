# Comandos Linux

# traceroute

| Opción         | Descripción                                                       |
| -------------- | ----------------------------------------------------------------- |
| -4             | Forzar IPv4.                                                      |
| -6             | Forzar IPv6.                                                      |
| -n             | No resolver direcciones IP a nombres de host (más rápido).        |
| -m <max_hops>  | Establecer el número máximo de saltos (por defecto: 30).          |
| -w <timeout>   | Establecer el tiempo de espera para cada sondeo en seg. (def: 5). |
| -q <nqueries>  | Establecer el número de sondeos por salto (por defecto: 3).       |
| -f <first_ttl> | Establecer el valor inicial de tiempo de vida (TTL) (def: 1).     |
| -I             | Usar ICMP ECHO en lugar de UDP para los sondeos.                  |
| -T             | Usar paquetes TCP SYN para los sondeos.                           |
| -p <port>      | Especificar el puerto de destino para sondeos UDP/TCP.            |

# IP

- `OBJETO` (OBJECT): link (enlace), address (dirección), route (ruta), neighbour (vecino), etc.
- `COMANDO` (COMMAND): show (mostrar), add (agregar), delete (eliminar), etc.

## Objetos Comunes
| Objeto | Descripción |
|--------|-------------|
| `link` | Interfaces de red físicas/virtuales |
| `addr` | Direcciones IP de interfaces |
| `route` | Tabla de enrutamiento |
| `neigh` | Tabla ARP/vecinos |
| `maddr` | Direcciones multicast |
| `tunnel` | Interfaces de túnel |


# dhclient

| **Comando**        | **Descripción**                                        | **Ejemplo**                 |
| ------------------ | ------------------------------------------------------ | --------------------------- |
| `dhclient`         | Solicita una dirección IP DHCP en todas las interfaces | `sudo dhclient`             |
| `dhclient eth0`    | Solicita IP para interfaz específica                   | `sudo dhclient eth0`        |
| `dhclient -r`      | Libera la IP actual (release)                          | `sudo dhclient -r`          |
| `dhclient -r eth0` | Libera IP de interfaz específica                       | `sudo dhclient -r eth0`     |
| `dhclient -v`      | Modo detallado (muestra detalles)                      | `sudo dhclient -v eth0`     |
| `dhclient -d`      | Modo depuración (información detallada)                | `sudo dhclient -d`          |
| `-v`, `--verbose`  | Muestra información detallada                          | `dhclient -v`               |
| `-d`, `--debug`    | Modo depuración extenso                                | `dhclient -d`               |
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

### Gestión ARP/Vecinos
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

| Opción           | Descripción                                                                   |
| ---------------- | ----------------------------------------------------------------------------- |
| -d               | No resolver direcciones IP a nombres de host (más rápido).                    |
| -h <max_hops>    | Establecer el número máximo de saltos (por defecto: 30).                      |
| -j <host-list>   | Especificar enrutamiento de origen flexible a lo largo de una lista de hosts. |
| -w <timeout>     | Establecer tiempo de espera para cada respuesta en milisegundos (def.: 4000). |
| -R               | Rastrear la ruta de ida y vuelta (solo IPv6).                                 |
| -S <source_addr> | Especificar la dirección de origen (solo IPv6).                               |
| -4               | Forzar IPv4.                                                                  |
| -6               | Forzar IPv6.                                                                  |
```

# Ejemplos

Reiniciar servicio NetworkManager para volver a pedir una IP al servidor DHCP
```shell
sudo systemctl restart NetworkManager.service
```