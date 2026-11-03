Dentro de la opcion `Firewall --> Aliases --> URLs` tenemos la opcion de poner un listado desde un FQDN el cual es desde una pagina web o de un repositorio en github

# Que es una url table

Simplemente es un archivo de texto con dominios de otras paginas web el cual le servirá al pfsenes para que permita el trafico en esos dominios, por ejemplo si creamos un repositorio en github, nosotros solamente dejaremos un archivo de texto con los dominios tal como se muestra en el ejemplo

```plaintext
sip.ringcentral.com
voip.ringcentral.com
media.ringcentral.com
rc-mobile.ringcentral.com
meetings.ringcentral.com
rcvideo.ringcentral.com
stun.l.google.com
turn.ringcentral.com
```

el url que copiaremos sera el de tipo raw, y si todo sale bien nuestra regla quedara de la siguiente manera

![Pasted image 20251103152852.png](../IMAGES/Pasted%20image%2020251103152852.png)

Si quieres seguir viendo el ejemplo de ring central ve a la nota [Reglas ringcentral firewall](Reglas%20ringcentral%20firewall.md)