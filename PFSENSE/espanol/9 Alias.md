Hay casos en los que tenemos que configurar reglas que son aplicables para 2 o más interfaces y por lo tanto tendríamos que hacer una regla individual en cada interfaz para permitir el acceso al paquete, sin embargo los Alias nos ayudarán a evitar replicar la misma regla en todas las interfaces donde aplique, además podremos incluso colocar un aliar al puerto o puertos que configuremos en esta opción

a continuación se mostrará como configurar un alias

# Configuración de alias

Empezaremos por hacer un alias para el puerto `80` y `443` al cual nombraremos TraficoWeb

- Primero nos dirigimos a la ruta `Firewall --> Aliases` aquí tenemos varias opciones, podemos por ejemplo crear una regla con un conjunto de puertos, IPs o incluso con dominios, la siguiente imagen mostrará cómo configuramos una regla para puertos

\*\*\!\[\[Pasted image 20251030154047.png\]\]

como pudiste notar agrupamos 2 puertos en un solo alias, más adelante podremos usar el nombre de este alias en alguna regla que queramos configurar

- Ahora vayamos a la ruta `Firewall --> Rules` para comenzar a usar este alias, si recuerdas bien habíamos configurado 2 reglas para que permitan el tráfico en los puertos 80 y 443, borraremos esas reglas y usaremos una sola regla para que apliquen a esos puertos y como podrás notar en la imagen, nos aparece el alias si colocamos el nombre del alias en la sección del puerto dentro de la regla

\!\[\[Pasted image 20251030160008.png\]\]

también si configuras un rango de IP o FQDN aparecerá el nombre que le hayas puesto al alias en el apartado de la IP