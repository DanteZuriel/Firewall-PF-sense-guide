# Limitadores parent

- **Cree o edite el Limiter** (por ejemplo, WAN_UPLOAD):
    - Vaya a **Firewall > Traffic Shaper > Limiters > New Limiter** (o edite uno existente).
    - **Enable**: marque.
    - **Name**: WAN_UPLOAD.
    - **Bandwidth**: 920–950 Mbps (o 95 % de su velocidad de subida real medida).
    - **Mask**: None.
    - **Queue Management Algorithm**: Tail Drop (importante: elija esto, no CoDel aquí).
    - **Scheduler**: seleccione **FQ_CODEL** (si solo ve "NONE", elija lo que aparezca o deje NONE temporalmente; en muchas builds, FQ_CODEL se activa al guardar con Tail Drop).
    - **Queue Length**: 3000–5000 (o deje en blanco para default).
    - **ECN**: marque.
    - Guarde el Limiter (**Save** y **Apply Changes**).
- **Después de guardar**:
    - Regrese inmediatamente a editar el mismo Limiter (clic en su nombre).
    - Ahora deberían aparecer los campos adicionales para FQ_CoDel:
        - **Target**: 5 (ms) — delay objetivo.
        - **Interval**: 100 (ms).
        - **Quantum**: 300 (para priorizar paquetes pequeños como VoIP/RingCentral; si no aparece, use 1514 por defecto).
        - **Limit**: 10240 (o 20480 si tiene mucha RAM y muchos usuarios).
        - **Flows**: 1024 (o 4096–65535 en hardware potente).
    - Configure Quantum en **300** (da mejor desempeño a VoIP y paquetes pequeños en HTTPS).
    - Guarde de nuevo y aplique cambios.
- **Repita exactamente el mismo proceso** para WAN_DOWNLOAD (mismos valores, pero bandwidth de bajada).

## Pasos para crear la child queue de VoIP

1. **Edite el Limiter WAN_UPLOAD**
    - Vaya a **Firewall > Traffic Shaper > Limiters**.
    - Haga clic en el nombre de WAN_UPLOAD (el limiter raíz).
    - En la parte inferior de la página, haga clic en **+ Add new Queue** (o "Add new Queue" según la interfaz).
2. **Configure la child queue para VoIP**
    - **Enable**: Marque la casilla.
    - **Name**: qVoIP (o VoIP_RingCentral; use solo letras, números y guiones bajos).
    - **Mask**: None (o Source Address si quiere per-IP fairness dentro de VoIP, pero None es más simple).
    - **Description**: Cola prioritaria para RingCentral VoIP (opcional).
    - **Weight**: 100 (máximo; da la prioridad más alta relativa dentro del limiter).
    - **Queue Management Algorithm**: CoDel (o Tail Drop si CoDel no aparece; FQ_CoDel en la raíz ya aplica AQM).
    - **Queue Length**: Deje en blanco (hereda del padre) o 1000–2000.
    - **ECN**: Marque (recomendado).
    - Si aparecen campos FQ_CoDel adicionales en la child queue:
        - **Quantum**: 300 (prioriza paquetes pequeños de VoIP).
        - **Target**: 5 ms.
        - **Interval**: 100 ms.
        - **Limit**: 10240 (o más si tiene RAM suficiente).
        - **Flows**: 1024–4096.
    - Guarde la queue.
3. **Repita para WAN_DOWNLOAD**
    - Edite WAN_DOWNLOAD.
    - Agregue la misma child queue (**+ Add new Queue**) con los mismos parámetros (Name: qVoIP, Weight: 100, Quantum: 300, etc.).
    - Guarde y aplique cambios (**Apply Changes**).
4. **Asigne tráfico VoIP a la child queue mediante reglas flotantes**
    - Vaya a **Firewall > Rules > Floating**.
    - Cree una nueva regla (o edite una existente si ya tiene para VoIP):
        - **Action**: Match (recomendado para shaping sin filtrar) o Pass.
        - **Interface**: WAN.
        - **Direction**: out (para upload; cree otra para in si aplica).
        - **Protocol**: UDP (principal para RTP; agregue TCP si usa SIP TLS).
        - **Source**: LAN net (su red interna).
        - **Destination**: any (o cree alias con dominios/IP de RingCentral si quiere precisión).
        - **Destination port range**: From 10000 To 20000 (rango RTP típico de RingCentral) + puerto 5060–5061 para SIP.
            - Para mayor precisión: cree reglas separadas para 5060–5061 (SIP) y 10000–20000 (RTP).
        - **In/Out pipe**: Seleccione WAN_UPLOAD en Out pipe, y en **Queue** (si aparece) elija qVoIP (la child queue).
            - Si no ve opción Queue separada, el In/Out pipe asigna al limiter raíz, pero el weight alto en qVoIP prioriza flujos que coincidan.
    - Cree una regla similar para **Direction: in** (download) usando WAN_DOWNLOAD y qVoIP.
    - **Description**: Match VoIP RingCentral a qVoIP.
    - Coloque estas reglas **arriba** en la lista (antes de reglas genéricas).
    - Guarde y **Apply Changes**.
5. **Verificación final**
    - Resetee estados: **Diagnostics > States > Reset**.
    - Inicie una llamada RingCentral + tráfico saturante (descarga grande).
    - Monitoree: **Status > Traffic Graph** o **Diagnostics > Limiter Info** (busque qVoIP con bajo delay).
    - Pruebe bufferbloat nuevamente (waveform.com/tools/bufferbloat) durante la llamada.

Con quantum 300 en la raíz (o child) y weight 100 en qVoIP, RingCentral debería mantener baja latencia/jitter incluso en saturación. Si no ve la opción de Queue en las reglas flotantes o quantum en la child, FQ_CoDel en la raíz ya ofrece buena protección para VoIP (fairness por flujo favorece paquetes pequeños). 

# Configuracion reglas flotantes

### Resumen de las reglas recomendadas (ambas direcciones)

1. **Regla para llamadas salientes (upload)**
    - Floating rule:
        - Action: Match
        - Interface: WAN
        - Direction: out
        - Protocol: UDP (principal para RTP; agregue TCP para SIP si usa TLS)
        - Source: LAN net
        - Destination: any
        - Destination port: 5060–5061 y 10000–20000 (puede crear dos reglas separadas para precisión)
        - In/Out pipe: WAN_UPLOAD
        - Queue: qVoIP (child queue)
    - Esto prioriza el envío de audio y señalización saliente.
2. **Regla para llamadas entrantes (download)**
    - Floating rule idéntica pero:
        - Direction: in
        - In/Out pipe: WAN_DOWNLOAD
        - Queue: qVoIP
    - Esto asegura que el audio de retorno (entrante) también vaya a la cola prioritaria.

### Recomendaciones adicionales

- Cree un **alias** (Firewall > Aliases) con los dominios/IP de RingCentral (ej. *.ringcentral.com, *.rcapp.com) para mayor precisión en destination/source si lo desea.
- Coloque estas reglas **arriba** en la lista de Floating para que se evalúen antes que las genéricas.
- Después de aplicar: resetee estados (**Diagnostics > States > Reset**) y pruebe una llamada entrante y saliente simultáneamente con tráfico saturante.
- Monitoree latencia/jitter en **Status > Traffic Graph** o durante la llamada.

Con ambas reglas (out e in), las llamadas de RingCentral quedarán protegidas en las dos direcciones. Si observa audio entrecortado solo en una dirección, confirme que la regla "in" esté aplicada correctamente y que el bandwidth del limiter WAN_DOWNLOAD esté ajustado al 92–95 % de su velocidad real de bajada.