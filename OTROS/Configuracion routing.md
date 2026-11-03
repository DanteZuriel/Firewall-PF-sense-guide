| Dispositivo | Interfaz      | IP             | Default Gateway  | Ruta estática necesaria    | interfaz en virtual box |
| ----------- | ------------- | -------------- | ---------------- | -------------------------- | ----------------------- |
| pfSense-1   | LAN           | 192.168.2.1/24 | (NAT hacia host) | 192.168.3.0/24 → 10.0.3.1  | NAT                     |
| pfSense-1   | OPT1 (enlace) | 10.0.2.15/24   | -                | -                          | NAT                     |
| pfSense-2   | WAN (enlace)  | 10.0.3.1/24    | 10.0.2.1         | 192.168.2.0/24 → 10.0.3.15 | internal network        |
| pfSense-2   | LAN           | 192.168.3.1/24 | -                | -                          | internal network        |


Firewall → Diagnostics → States → Reset States

| Dispositivo | Interfaz      | Opcion 1 mediante la red del pfsense principal | Opcion 2 si el pfsense secundario es tiene otra red |
| ----------- | ------------- | ---------------------------------------------- | --------------------------------------------------- |
| pfSense-1   | OPT1 (enlace) | 10.0.2.15/24                                   | 10.0.3.15/24                                        |
| pfSense-1   | Ruta estática | 192.168.3.0/24 → 10.0.2.25                     | 192.168.3.0/24 → 10.0.3.1                           |
| pfSense-2   | WAN (enlace)  | 10.0.2.25/24                                   | 10.0.3.1/24                                         |
| pfSense-2   | Default GW    | 10.0.2.15                                      | 10.0.3.15                                           |
| pfSense-2   | Ruta estática | 192.168.2.0/24 → 10.0.2.15                     | 192.168.2.0/24 → 10.0.3.15                          |
