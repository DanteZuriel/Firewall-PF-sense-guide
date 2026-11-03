ARP funciona mediante una tabla en la cual la máquina local ya había tenido una comunicación con otra dirección ip, en la siguiente imagen podremos ver como funciona esta comunicación en la cual la ip pregunta al broadcast sobre la ip que está tratando de encontrar y una vez encontrada se guarda la información de la ip en la tabla de arp

![Pasted image 20251020155231.png](../../IMAGES/Pasted%20image%2020251020155231.png)

# Amarre mac-ip

Por defecto pfsense solo guarda la información de una ip durante un par de horas, lo podemos ver en la siguiente imagen, en la cual nos ubicamos en la ruta `Diagnostics --> ARP Table`

![Pasted image 20251021141657.png](../../IMAGES/Pasted%20image%2020251021141657.png)

Como puedes ver algunas interfaces tienen tiempo de expiración (IP no estática) e IPs permanentes (IP estática), si nosotros quisiéramos que solo las IP estáticas tengan acceso a Internet y que las que tengan tiempo de expiración no tengan acceso a Internet, para lograrlo tenemos que ir a  `Services --> DHCP Server --> <elegimos la interfaz en este caso la LAN>` y marcaremos la siguiente opción

![Pasted image 20251021142538.png](../../IMAGES/Pasted%20image%2020251021142538.png)

ADVERTENCIA: Al activar esta opción notarás que las IP que no configuraste como estáticas perderán el acceso a Internet por ello es importante que si no agregas una IP como estática y quieres volver a una configuración anterior puedes usar la `opción 15` del firewall, la cual es útil por si quieres deshacer los últimos cambios que realizaste.