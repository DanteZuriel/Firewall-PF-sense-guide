Ya que realizamos la configuración en la nota \[\[1 Configuración inicial\]\] lo siguiente será asignar una ip a nuestra máquina virtual donde estará conectado a la `LAN`, mostraré 2 ejemplos de cómo podemos asignarla primero empecemos con windows

## Configurar IP en Windows

1. primero vayamos a la siguiente ruta `Control Panel\Network and Internet\Network Connections`  
2. Da click derecho en tu adaptador de internet \--\> selecciona propiedades \--\> Ahora busca IPv4 \--\> da click en propiedades  
3. Ahora en este panel colocaremos una ip de acuerdo al rango que hayamos puesto en nuestro adaptador de la LAN y el default gateway será el la ip del adaptador de la LAN

![Pasted image 20251009160438.png](../../IMAGES/Pasted%20image%2020251009160438.png)

## Configurar IP en Linux

En linux es prácticamente parecido a windows

1. Ve a `configuración/network --> wired` dale click al `engranaje --> dale click en ipv4` y usa la misma configuración que usamos en Windows, recuerda que si hay una opción para establecer conexión DNS automáticamente desactivarla para que funcione la IP estática.

# Configuración inicial con wizard setup

Ahora podemos acceder a la interfaz gráfica de pfsense donde podremos realizar varias configuraciones primero accedemos a ella mediante la IP de la LAN mediante la siguiente sintaxis `http://<IP>` después nos mostrara una pagina de Login y seguiremos los siguientes pasos

- Usar las credenciales por defecto Username \= `admin` Password \= `pfsense`  
- Después entraremos en `System --> setup wizard`. Para realizar la configuración inicial  
- Primero colocamos los datos de host, dominio y dns, como se muestra en la imagen.

![Pasted image 20251011112414.png](../../IMAGES/Pasted%20image%2020251011112414.png)

- Los siguientes pasos hasta `configure WAN interface` los dejamos por defecto a excepción del cambio de la zona horaria, la imagen de abajo podremos ver en qué paso nos situamos

![Pasted image 20251011112937.png](../../IMAGES/Pasted%20image%2020251011112937.png)

- Sin embargo es importante que revises que la opción de RFC1918 esté activado ya que esto bloqueará el rango de las `IP` que establece la `RFC1918`  
- `Block Bogon Networks` aquí se definen todas las redes que no son utilizadas por dispositivos como redes de prueba o no utilizadas, así que si viene tráfico al `firewall` por una de estas redes la va a bloquear

![Pasted image 20251011113101.png](../../IMAGES/Pasted%20image%2020251011113101.png)

- De aquí en adelante en los siguientes pasos solo damos en `next` y solo se configuró la asignación de password y recargamos la página para aplicar cambios.

# Probar conexión con dns

Probaremos conexión con el DNS de google que asignamos a nuestro servidor firewall `8.8.8.8`

## Windows

```cmd
tracerout -d 8.8.8.8
```

&nbsp;

## Linux

```sh
┌──(kali㉿kali)-[~]
└─$ traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  pfSense.home.arpa (192.168.2.1)  2.968 ms  2.960 ms  3.206 ms
 2  192.168.1.254 (192.168.1.254)  5.349 ms  5.163 ms  5.827 ms
 3  bastij28.telnor.net (xxx.xx.xxx.xx)  10.801 ms  10.605 ms  10.536 ms
 4  bb-la-grand-10-ae53_0.uninet.net.mx (xxx.xxx.xxx.xxx)  12.427 ms *  13.589 ms
 5  * * xxx.xxx.xxx.xxx (xxx.xxx.xxx.xxx)  13.180 ms
 6  * * *
 7  dns.google (8.8.8.8)  9.593 ms  9.518 ms  9.303 ms

```

Ambos comandos funcionan de la misma manera pero como notamos en la salida del comando, se trazara la ruta del paquete a `8.8.8.8` y podrás ver que comienza desde `pfsense` hasta los servidores de Google así que, como nuestro comando funciona, esto nos quiere decir que todos los paquetes serán gestionados por el firewall.