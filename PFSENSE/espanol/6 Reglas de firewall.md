Cada interfaz tendrá sus propias reglas, eso quiere decir que si configuras una regla para la WAN solo funcionaran esas reglas en la WAN y no en las otras interfaces, la sintaxis de las reglas es la siguiente `Condicion --> Acciones` en donde la condición son las reglas que aplicará al paquete que está entrando o saliendo del firewall y las acciones dejarán pasar o impedir el paso del paquete y los guiaran por el conducto que se haya especificado en las reglas.

- Una buena práctica sería colocar las reglas más específicas en la parte de arriba de la tabla y poner las menos específicas en la parte de abajo

# Reglas flotantes

Normalmente en el 90% de los casos no es necesario tener reglas flotantes, una regla flotante es una regla que aplicara para todas las interfaces sin embargo lo recomendable es mejor no usarla y configurar cada interfaz por sí solo o crear grupos de interfaces

# Planificación

Antes de empezar a configurar reglas primero tenemos que tener claro qué es lo que queremos hacer por ejemplo si queremos que la WAN se conecte a Internet o que la conexión entre la interfaz LAN y la DMZ sea bloqueada, la siguiente lista es un ejemplo de planificación, más adelante en la interfaz gráfica de pfsense configuraremos algunas reglas

\!\[\[Pasted image 20251022155108.png\]\] Observa que en las reglas que planificamos de la `LAN` las reglas que especifica el tráfico de los puertos `80 y 443` están por encima de la regla que bloquea el tráfico entre la LAN y la DMZ es por ello que si estas 2 reglas cambiaran de lugar primero bloquearía todo el tráfico de la LAN a la DMZ y después al querer aplicar la regla que permite la entrada de paquetes de la LAN a la DMZ mediante los puertos `80 y 443` la regla no podría aplicarse porque ya habíamos bloqueado todo el tráfico entre la LAN y la DMZ, es por ello que el orden en las reglas importa.

# Comenzando a configurar reglas

Nos situaremos en la ruta `Firewall --> Rules` Aquí es donde configuraremos nuestras reglas, la interfaz tiene algunos botones que sirven para desplazar o separar las reglas o añadir una regla nueva, si añadimos una nueva regla, el editor nos dará las siguientes 3 acciones a aplicar para la regla las cuales se explican a continuación

## Actions

- `Pass` Permite el acceso al paquete  
- `Block` Deniega la regla silenciosamente sin notificar el porque se bloqueo el paquete  
- `Reject` Deniega la regla pero notificando, a nivel de la capa 3 mediante Wireshark podremos ver etiquetas como `RST` en `TCP` o `Unreachable` mediante `UDP`

## Source

La fuente será el origen del paquete o en otras palabras quien mandara el paquete, ahora como podemos ver en la siguiente imagen hay varias opciones donde podremos elegir cuál será el origen

\!\[\[Pasted image 20251023144729.png\]\]

- `Any` Se refiere a cualquier dirección ip  
- `Address or Alias` Especificamos alguna ip  
- `Network` se refiere a la red local como por ejemplo `10.10.0.2/24`  
- `address` Podemos ver que las interfaces que ya configuramos aparecen con address eso se refiere a la ip de la interfaz  
- `subnets` Se refiere al rango total de esa interfaz, como estamos usando el switch virtual de virtualbox aparece como `DMZ subnet` pero si lo tuviéramos conectados a algún switch físico cambiaría su nombre a `DMZ net`

\!\[\[Pasted image 20251023155012.png\]\]

Como vemos en la imagen de arriba configuramos una regla para denegar el acceso a Internet de `DMZ --> LAN` y otra para permitir el acceso a Internet, la descripción que habíamos colocado al crear la regla te dirá que es lo que realizará

# Regla específica SSH

Ahora configuraremos reglas específicas en la `LAN` nos guiaremos en base a lo que menciona el subtitulo `PLANIFICACION`.

\!\[\[Pasted image 20251023161317.png\]\]

Como podrás observar especificamos el puerto 22 para conexión remota mediante SSH como no aparece el puerto en el listado utilizamos la opción `other` y especificamos el número de puerto la ip de source es nuestra ip en la máquina ubuntu conectada a la interfaz `LAN` a la máquina parrot conectada a la interfaz `DMZ` si nosotros hacemos pruebas mediante SSH podremos ver que negara el acceso a SSH en dispositivos que no tiene autorizado el uso del puerto

# Reglas

# Consideraciones:

Antes de que comiences a aplicar reglas es importante que hagamos nuestras pruebas utilizando el comando `ping <IP_address>` para verificar cómo es que estas reglas permiten e impiden la comunicación entre nuestras máquinas virtuales y la WAN.

## WAN

Podrás notar que antes de crear nuestras reglas ya tenemos 2 reglas creadas con la siguiente descripción (Block private networks) y (Block bogon networks), por defecto pfsense configura automáticamente la regla que bloquea las redes privadas pero la regla que bloquea las redes bogon se creó gracias a la configuración en la nota \[\[3 DMZ inicio, DHCP Server y reserva de IP\]\]

Realizamos lo siguiente:

1. Primera y segunda regla ya están colocadas por defecto de acuerdo a la configuración de esta guía.  
2. Tercera y cuarta regla: Creamos 2 reglas para permitir el tráfico `HTTP` y `HTTPS` de la `WAN` a `DMZ`, de esta manera, la máquina que funcionara como `DMZ` tendrá acceso a Internet, sin embargo también debemos crear una interfaz en el DMZ para que permita el tráfico hacia la WAN  
3. En la última regla bloqueamos todo el tráfico hacia la `WAN`. \!\[\[Pasted image 20251027155224.png\]\]

## LAN

1. La primera regla por defecto con la descripción (Anti-Lockout Rule) es una regla de configuración en la cual podemos habilitar `SSH` para entrar a la configuración de pfsense o permitir el acceso a pfsense mediante HTTPS entre otras configuraciones.  
2. Segunda regla: Permitimos `ICMP` hacia cualquier dirección.  
3. Tercera regla: Permitimos `HTTPS` de `192.168.2.2` hacia `DMZ`  
4. Cuarta regla: Permitimos `SSH` de `192.168.2.2` hacia `DMZ`  
5. Las últimas 2 reglas ya están colocadas por defecto de acuerdo con la configuración de esta guía. \!\[\[Pasted image 20251027155507.png\]\]

## DMZ

1. La primera regla ya está puesta por defecto de acuerdo a la configuración de esta guía.  
2. Segunda regla: permitimos el acceso a Internet de las subredes de `DMZ` hacia cualquier dirección.  
3. Tercera regla: Rechazamos los paquetes de las subredes de `DMZ` hacia las subredes de la `LAN` \!\[\[Pasted image 20251027155309.png\]\]

Como podrás notar en las reglas de la `LAN` agregamos una regla para `ICMP` de `LAN` \--\> `WEB SERVER` esto es debido a que si esta regla no está presente e intentamos hacer un `ping` al webserver, la terminal no nos mostrará los mensajes del comando `ping`

# Opciones avanzadas

- `Source OS` Mediante TCP pfsense hace un análisis mediante `SYN` para determinar que sistema operativo utiliza  
- `Diffserve Code Point` `DSCP` Para priorizar el tráfico  
- `Allow IP options` Si se marca se permite opciones de IP  
- `Disable reply-to` La forma en que se mandaran las respuestas cuando entrara a la interfaz, se mandaran a un equipo destino  
- `Max states` Cantidad de conexiones totales independientemente de su estado  
- `Max src nodes` Se refiere a la cantidad de direcciones `origen` que harán match con la regla  
- `Max connections` Limita el máximo de conexiones actuales establecidas en TCP  
- `Max src states` Cantidad máxima de entradas de una conexión origen  
- `Max src conn rate` Limita el máximo de conexiones nuevas  
- `Max src conn rates` Se refiere a la velocidad de las conexiones  
- `State timeout` Cierra conexión la conexión y se declara inactiva si no hay interacción  
- `TCP flags` aquí puedes marcar o desmarcar las etiquetas del paquete como `SYN` o `ACK` podrías hacer acciones como no permitir el acceso a algún servidor si le bloqueamos la etiqueta `SYN` interrumpiendo el freeway handshake  
- `Schedule` para determinar el tiempo que estará la regla activa, sin embargo primero hay que crear un schedule  
- `Gateway` permitirá trabajar con policie based routing

# Crear un schedule

En la ruta `Firewall --> Schedules --> Edit` podemos configurar un rango de días y horas en los que queremos que la regla este activa, la imagen de abajo mostrará un ejemplo para configurar una jornada de trabajo regular

\!\[\[Pasted image 20251028144056.png\]\]