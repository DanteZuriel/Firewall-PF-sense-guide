Estas reglas son útiles cuando tienes `2 o mas interfaces` con una regla en común, por ejemplo que en la LAN y en la DMZ en ambos casos permitas el acceso al puerto 21 en lugar de crear 2 reglas por separado lo más conveniente es hacer un grupo de interfaz

En el siguiente ejemplo haremos un cambio en las reglas que ya habíamos creado en la nota [6 Reglas de firewall](6%20Reglas%20de%20firewall.md)

# Creando la regla de interfaz

Primero crearemos el grupo de interfaces en la ruta de la interfaz `Interfaces --> Interface Groups` aquí solo seleccionaremos las interfaces para posteriormente crear la regla

![Pasted image 20251029153233.png](../../IMAGES/Pasted%20image%2020251029153233.png)

Después nos iremos a la ruta y como podremos ver se nos creó un nueva pestaña con el nombre de la interfaz de grupo que creamos

![Pasted image 20251029153410.png](../../IMAGES/Pasted%20image%2020251029153410.png)

Ahora solo nos queda configurar la regla la cual quedó de la siguiente manera

![Pasted image 20251029154319.png](../../IMAGES/Pasted%20image%2020251029154319.png)