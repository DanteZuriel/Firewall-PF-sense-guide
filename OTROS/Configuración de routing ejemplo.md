

| Dispositivo | Interfaz      | Opcion 1 mediante la red del pfsense principal | Opcion 2 si el pfsense secundario es tiene otra red |
| ----------- | ------------- | ---------------------------------------------- | --------------------------------------------------- |
| pfSense-1   | OPT1 (enlace) | 10.0.2.15/24                                   | 10.0.3.15/24                                        |
| pfSense-1   | Ruta estática | 192.168.3.0/24 → 10.0.2.25                     | 192.168.3.0/24 → 10.0.3.1                           |
| pfSense-2   | WAN (enlace)  | 10.0.2.25/24                                   | 10.0.3.1/24                                         |
| pfSense-2   | Default GW    | 10.0.2.15                                      | 10.0.3.15                                           |
| pfSense-2   | Ruta estática | 192.168.2.0/24 → 10.0.2.15                     | 192.168.2.0/24 → 10.0.3.15                          |
|             |               |                                                |                                                     |

# Problemas con la table routes

ve a la ruta Firewall → Diagnostics → States → Reset States y reinicia los states de la tabla de rutas
