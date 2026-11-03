# Tabla de rutas

Nos dirigimos a la ruta `Diagnostics --> Routes` aquí podremos ver la siguiente información

\!\[\[Pasted image 20251118152036.png\]\]

Destination: Aquí veremos las ip y la red

Gateway: Primero vemos la ip del router y los campos donde dice Link 1 en adelante quiere decir que están conectadas estas redes.

Flags:

1. `U` Significa red usable o activas  
2. `G` Gateway o el router intermediario que necesita ayuda de otro dispositivo  
3. `S` Static quiere decir que se agregó la ruta de forma manual  
4. `H` Host es la dirección ip así que no aparecerán en rangos de ip

Uses: Veces que se han utilizado esas rutas para envío de paquetes

MTU: Max Transmision Unit, es el tamaño de paquetes que se puede enviar en esa dirección la medida de tamaño es en `bytes`

Interface: La interfaz a la que pertenece la ip o la red, `em` son las interfaces de pfsense y si configuraste una ip virtual aparecerá como por ejemplo `lo0` lo cual significa loopback 0

# Preparación

## Adaptadores de red

Antes de proceder a enrutar primero colocaremos nuestra máquina pfsense, eso lo podemos hacer de manera sencilla dando dando click derecho en nuestra máquina virtual en la opción clone, una vez clonado configuramos los adaptadores en la configuración de network de virtualbox (ten en cuenta que para hacer un enrutamiento de este tipo en un servidor pfsense físico es necesario que tenga otra entrada ethernet para que se conecte el otro servidor pfsense). Nuestra configuración quedaría de la siguiente manera

### Servidor pfsense original

- En las opciones de network habilitamos otro adaptador virtual, al igual que lo hicimos al principio para configurar el adaptador de la LAN y la DMZ, le podemos poner el nombre que queramos, por ejemplo en la imagen le pusimos el nombre de zona 2 \!\[\[Pasted image 20251119134210.png\]\]

### Servidor pfsense clon

En el servidor clon nos conectamos a la interfaz que hicimos en el servidor original, y dejamos un segundo adaptador para la LAN igualmente en internal network

### Cliente

Ahora necesitamos una máquina virtual que se conecte al servidor clon así que la conectaremos a la nueva interfaz de LAN que creamos en el servidor clon

## Preparación interfaz grafica

Una vez que tenemos preparados los adaptadores de red, vamos a la interfaz gráfica y en la ruta `Assignments --> Interface assignments` allí solo habilitaremos la interfaz del servidor clon pfsense y configuraremos la interfaz, en este caso solo modificamos la dirección ip estática en ipv4 con CIDR /24

Ya que habilitamos la interfaz creamos una regla que permita todo

\!\[\[Pasted image 20251119141759.png\]\]

## Servidor clon

Ahora configuraremos el servidor clon mediante su terminal, veremos que hay que asignar una nueva interfaz, así que le asignaremos a la interfaz em0 como WAN y a em1 como LAN sin embargo cambiaremos esta interfaz más adelante.

\!\[\[Pasted image 20251119142142.png\]\]

En esta configuración que estamos haciendo como clonamos el firewall tenemos que usar la opción 4 de la terminal para restaurar las configuraciones por defecto antes de proceder a la configuración mediante la interfaz gráfica.

## Interfaz gráfica en servidor clon

Entramos en la interfaz y configuramos la ip de nuestro servidor, recuerda que como se usó la opción 4 en la terminal accederemos a la interfaz con las credenciales por defecto `admin:pfsense`, una vez que entramos en este caso nos saltamos el wizard y vamos directamente a la opción `Interfaces` y asignamos una IP a la `WAN` y la `LAN` además es importante que las opciones de `Reserved Networks` estén desmarcadas ya que no es una interfaz que accede a internet

\!\[\[Pasted image 20251119145116.png\]\]

En este punto como cambiamos la IP de la LAN nos quedaremos sin internet en la maquina cliente así que asignamos un ip estática para poder volver a entrar y configurar el servidor dhcp en la ruta `Services --> DHCP` recordemos que hay que cambiar los siguientes parámetros en base a la configuración que estamos realizando en este ejemplo

- Cambiar rango de dhcp pool  
- Asignar gateway  
- Asignar servidores DNS recordemos que estamos trabajando con los servidores de cloudflare `1.1.1.1` y google `8.8.8.8`

Con esto ya tenemos listo la preparación para el enrutamiento

# Enrutamiento

## Asignando las redes en firewall original y permitir tráfico

Primeramente debemos entender que el enrutamiento consistirá en la comunicación de una máquina host del firewall original a una máquina host de la máquina clon, la opción que nos ayudara a esto en la interfaz gráfica se encuentra en la ruta `System --> Routing --> Gateways --> Edit` aqui asignamos una dirección disponible en este caso elegimos la direccion `10.0.3.15` dentro de la interfaz grafica del firewall original

\!\[\[Pasted image 20251122132940.png\]\]

si tienes problemas para colocar el gateway revisa la interfaz del firewall clone, así como se muestra la siguiente imagen la cual muestra las interfaces disponibles en el firewall original

\!\[\[Pasted image 20251119160502.png\]\]

Ya que tenemos el gateway, nos movemos a `System --> Routing --> Static routes` aquí colocaremos la red a la que queremos conectarnos, así que seleccionamos la red de la `LAN 2` y nuestra regla quedaría de la siguiente manera

\!\[\[Pasted image 20251122133517.png\]\]

- Ahora crearemos la regla en nuestro firewall original para que tenga acceso la `LAN 2` la regla consistirá que el tráfico pueda pasar de las redes internas del firewall principal a las redes internas del firewall clon, asi que nuestra regla queda de la siguiente manera

\!\[\[Pasted image 20251122133149.png\]\]

# Asignando redes en firewall clon

Ahora realizaremos el mismo proceso añadimos la ruta `gateway` la ip de nuestro gateway de zona 2 o sea el firewall clon, además este gateway que creamos lo colocamos por defecto así como se ver en la parte de abajo de la imagen

\!\[\[Pasted image 20251122133647.png\]\]

Nuestra regla estática va a apunta a la red del firewall principal

\!\[\[Pasted image 20251122133738.png\]\]

estas configuraciones las podemos comprobar en `Diagnostics --> Routes` , como configuración de seguridad podemos desactivar la `NAT` del firewall clon en la ruta `Firewall --> NAT --> Outbound`

- Ahora añadimos la regla para permitir todo el tráfico desde el firewall principal al firewall clon esta regla es sencilla ya que solo tenemos que configurarla para que permita todo