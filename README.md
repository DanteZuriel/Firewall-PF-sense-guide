# Acerca de Pfsense

Pfsense es un firewall del tipo Stateful y de red perimetral, es decir no solo analiza los paquetes de datos de forma individual, sino que también realiza un seguimiento del estado de las conexiones activas para determinar que el tráfico es legítimo y cuál debe bloquearse.

Está basado en el sistema operativo FreeBSD, la edición "community edition" se puede descargar de manera totalmente gratis en el siguiente link [pfsense](https://www.pfsense.org/download/)

Pfsense al operar mediante la capa 3 y 4 del modelo OSI y gracias a su arquitectura modular de paquetes es posible integrar complementos de terceros los cuales nos pueden dar funcionalidades similares a un NGFW (Next Generation Firewall) como por ejemplo:

- Instalar un IDS/IPS  
- Instalar paquetes como Squid y SquidGuard para el filtrado Web y de contenido  
- Instalar servidores VPN como OpenVPN, IPSec y WireGuard  
- Actuar como enrutador

Sin embargo la mayoría de configuraciones en estos paquetes queda fuera de la explicación de esta guía, únicamente se explicará como instalar y configurar Suricata a modo de ejemplo y para el desarrollo de este proyecto.

# Objetivo del proyecto

A lo largo de este proyecto veremos lo siguiente:

1. Cómo hacer la configuración inicial del firewall.  
2. Cómo configurar reglas básicas.  
3. Usaremos Virtualbox para configurar máquinas virtuales e interactuar con el firewall.  
4. Se explicará como configurar grupos de interfaces, y se explicaran algunas configuraciones de la NAT mediante IPs virtuales.

# Versión

La presente guía usara la versión 2.8.1

# Contenido de la guía

## Guía principal

- [1 Configuración inicial](PFSENSE/espanol/1%20Configuraci%C3%B3n%20inicial.md)
- [2 Accediendo a la interfaz web](PFSENSE/espanol/2%20Accediendo%20a%20la%20interfaz%20web.md)
- [3 DMZ inicio, DHCP Server y reserva de IP](PFSENSE/espanol/3%20DMZ%20inicio%2C%20DHCP%20Server%20y%20reserva%20de%20IP.md)
- [4 Bloqueo de clientes desconocidos](PFSENSE/espanol/4%20Bloqueo%20de%20clientes%20desconocidos.md)
- [5 ARP](PFSENSE/espanol/5%20ARP.md)
- [6 Reglas de firewall](PFSENSE/espanol/6%20Reglas%20de%20firewall.md)
- [7 Reglas flotantes](PFSENSE/espanol/7%20Reglas%20flotantes.md)
- [8 Grupos de interfaces](PFSENSE/espanol/8%20Grupos%20de%20interfaces.md)
- [9 Alias](PFSENSE/espanol/9%20Alias.md)
- [10 NAT](PFSENSE/espanol/10%20NAT.md)
- [11 Enrutamiento](PFSENSE/espanol/11%20Enrutamiento.md)
- [12. Traffic shapper](PFSENSE/espanol/12.%20Traffic%20shapper.md)

## Cheat sheets y troubleshooting

- [Activar opciones avanzadas](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/Activar%20opciones%20avanzadas.md)
- [Certificado](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/Certificado.md)
- [Cheat sheet windows](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/Cheat%20sheet%20windows.md)
- [cheat_sheet_linux](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/cheat_sheet_linux.md)
- [Conexion ssh](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/Conexion%20ssh.md)
- [Diccionario](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/Diccionario.md)
- [Herramientas de ciberseguridad](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/Herramientas%20de%20ciberseguridad.md)
- [Links utiles](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/Links%20utiles.md)
- [Modo puente en router telmex](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/Modo%20puente%20en%20router%20telmex.md)
- [Resolver time out en paginas](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/Resolver%20time%20out%20en%20paginas.md)
- [Restauracion](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/Restauracion.md)
- [Router telmex como AP Access Point](PFSENSE/espanol/CHEAT%20SHEETS%20Y%20TROUBLESHOOTING/Router%20telmex%20como%20AP%20Access%20Point.md)