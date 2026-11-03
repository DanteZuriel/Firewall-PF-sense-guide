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