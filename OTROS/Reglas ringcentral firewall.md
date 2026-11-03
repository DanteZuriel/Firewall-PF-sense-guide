
# Configuración de los aliases

Primero armaremos un repositorio en github con un archivo con extensión .txt con el siguiente contenido

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

copiamos el url en formato raw y pegamos la url en un alias con tipo url table, lo dejaremos en una frecuencia de 1 para que sea revisado cada día

![[Pasted image 20251103152852.png]]

Ahora realizamos otros 2 aliases para los puertos TCP en los puertos 80, 443, 5060, 5061, 5090, 5091, 5096, 5097, 636 y UDP en los puertos 123, 3478-3479, 5060-5091, 16384-16482, 20000-39999, 40000-49999, 50000-59999, 60000-64999

- Es importante que el rango de puertos en pfsense se separan por dos puntos `:` y los colocaremos tal como se muestra la imagen

![[Pasted image 20251104154619.png]]

La siguiente imagen muestra como quedarían nuestras reglas

![[Pasted image 20251104154718.png]]

Ahora solo queda asignar estos alias a las reglas flotantes.
# Configuración de reglas flotantes
nota: para activar estas opciones avanzadas ver la nota 

- Lo siguiente sera la configuración que colocaremos en la interfaz gráfica

- **Regla 1: Match TCP – RingCentral (QoS + Marcado)**
    - **Action**: **Match**
    - **Interface**: **LAN, WAN**
    - **Direction**: **Any**
    - **Protocol**: **TCP**
    - **Source**: **LAN net** (ej. 192.168.1.0/24)
    - **Source Port**: **Any**
    - **Destination**: **RingCentral_Destinations** (o RingCentral_Destinations_Final si usas híbrida)
    - **Destination Port**: **RingCentral_TCP_Ports** (alias con 80, 443, 5060, 5061, 5090, 5091, 5096, 5097, 636)
    - **Log**: ☑ (opcional para depuración)
    - **Description**: QoS: RingCentral TCP (SIP, HTTPS, LDAP)
    - **Advanced > Set DSCP**: **EF (46)**
    - **Advanced > Queue**: qVoIP_High (crear en Traffic Shaper)

- **Regla 2: Match UDP – RingCentral (QoS + Marcado)**
    - **Action**: **Match**
    - **Interface**: **LAN, WAN**
    - **Direction**: **Any**
    - **Protocol**: **UDP**
    - **Source**: **LAN net**
    - **Source Port**: **Any**
    - **Destination**: **RingCentral_Destinations** (o RingCentral_Destinations_Final)
    - **Destination Port**: **RingCentral_UDP_Ports** (alias con 123, 3478-3479, 5060-5091, 16384-16482, 20000-39999, 40000-49999, 50000-59999, 60000-64999)
    - **Log**: ☑ (opcional)
    - **Description**: QoS: RingCentral UDP (RTP, STUN, NTP)
    - **Advanced > Set DSCP**: **EF (46)** para voz, **AF41 (34)** para video
    - **Advanced > Queue**: qVoIP_High (o qVideo si separas)


# Reglas adicionales

en **Traffic Shaper > Wizards** o **Shaper > Queues**, utilizando el wizard "Penalty Box" o "HFSC" para priorizar el tráfico de RingCentral:

- Cree queues para clases de tráfico (e.g., Voz: alta prioridad, Video: media, Datos: baja).
- Aplique filtros basados en puertos/protocols de las reglas previas (e.g., UDP 50000-60000 para RTP) y marcas DSCP.
- Limite el ancho de banda total del WAN para evitar bursts que excedan la capacidad contratada.

RingCentral recomienda clasificar y marcar el tráfico con valores DSCP específicos (por ejemplo, `EF` para voz expedita y `AF41` para video) para su tratamiento prioritario en el WAN. 

