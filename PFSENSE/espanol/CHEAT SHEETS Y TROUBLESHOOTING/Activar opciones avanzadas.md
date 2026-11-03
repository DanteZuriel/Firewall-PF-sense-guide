Este ejemplo de activacion se basa para activar la opcion qVoIP 

### Paso 1: Activar la visualización de opciones avanzadas (una sola vez)

1. Ve a **System → Advanced → Admin Access**
2. Desplázate hasta la sección **“Display Options”**
3. Marca la casilla **Display Advanced Settings by default**
4. Pulsa **Save** abajo del todo

A partir de ahora, en todas las reglas de firewall aparecerán muchas más opciones.

### Paso 2: Crear la cola qVoIP_High (solo la primera vez)

1. Ve a **Firewall → Traffic Shaper → Queues**
2. Pulsa **+ Add new queue**
3. Configuración recomendada para VoIP/VPN prioritario:

|Campo|Valor recomendado|Nota|
|---|---|---|
|Enable|✓||
|Queue Name|qVoIP_High|Exactamente este nombre|
|Interface|WAN (o la interfaz que uses para salida)||
|Scheduler|HFSC o CBQ (da igual)||
|Bandwidth|100% (o el % que quieras reservar)||
|Priority|7 (máxima prioridad)||
|Description|Cola alta prioridad para VoIP y VPN||

4. Save → Apply Changes