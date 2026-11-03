#### 1. Instalación de pfBlockerNG

1. Accede a la interfaz web de pfSense.
2. Ve a `System > Package Manager > Available Packages` aqui busca pfblocker

#### 2. Configuración Inicial (Wizard y General)

1. Ve a `Firewall > pfBlockerNG`. Si es la primera vez, se inicia el wizard automáticamente.
2. Haz clic en **Next**. El wizard elimina configuraciones previas e instala componentes IP y DNSBL.
3. Configura:
    - **Inbound Firewall Interface**: WAN (para tráfico entrante).
    - **Outbound Firewall Interface**: LAN (o todas las interfaces internas relevantes para tu red).
    - **DNSBL VIP Address**: Ingresa una IP virtual única no usada (ej. 10.10.10.1) para el servidor DNSBL. Habilita IPv6 si lo usas.
4. Haz clic en **Finish**. Aparecerá la página de actualización; permite que descargue listas automáticamente.
5. Ve a **Firewall > pfBlockerNG > General**.
    - Habilita **Enable pfBlockerNG**.
    - Configura **CRON Settings**: Cada hora (ej. a los :15 minutos), para actualizaciones automáticas.
    - Deja el resto en defaults y guarda.

#### 3. Preparación para DNSBL (Requisitos Previos)

1. Asegúrate de que el DNS Resolver esté activo: Ve a `Services > DNS Resolver > General Settings` y habilítalo.
2. En **System > General Setup**, configura DNS forwarders externos (ej. 8.8.8.8 de Google) para resolución externa.
3. En **Services > DHCP Server** (por interfaz), establece el DNS como la IP de pfSense para que los clientes usen el resolver local.
4. Ve a `Firewall > pfBlockerNG > IP`.
    - Habilita **De-duplication**, **CIDR Aggregation**, **Suppression** y **Force Global IP logging**.
    - Para GeoIP (opcional, pero útil para whitelisting por país): Regístrate en MaxMind (gratuito) para una licencia key, ingrésala en **MaxMind License Key**.
5. Guarda los cambios.

#### 4. Configuración de DNSBL con URL Personalizada para Dominios

1. Ve a `Firewall > pfBlockerNG > DNSBL`.
    - Habilita **DNSBL** y selecciona **Unbound Python Mode** (para resolución avanzada de dominios).
    - Habilita: **Wildcard Blocking TLD**, **DNS Reply Logging**, **DNSBL Blocking**, **HSTS Mode**, **CNAME Validation**.
    - Configura **DNSBL Webserver Configuration** con la VIP (ej. 10.10.10.1).
    - En **DNSBL Configuration**, habilita **Permit Firewall Rules** para LAN (esto permite reglas de permiso en lugar de solo bloqueo).
    - Guarda.
2. Agrega la feed personalizada:
    - Ve a `Firewall > pfBlockerNG > DNSBL > DNSBL Groups`.
    - Haz clic en **Add** para crear un nuevo grupo (ej. "RingCentral_Domains").
    - En el grupo, ve a **DNSBL Source Definitions** y haz clic en **Add**.
    - Configura:
        - **Format**: URL (para tu URL con dominios, uno por línea en formato texto plano).
        - **State**: On.
        - **Source**: Ingresa tu URL (ej. [https://tu-url.com/dominios.txt](https://tu-url.com/dominios.txt)).
        - **Header/Label**: Un nombre descriptivo (ej. "RingCentral_Custom").
        - **Update Frequency**: Cada hora o diario.
        - **Action**: Unbound (para resolución DNS).
    - En las settings del grupo:
        - **List Action**: Alias Native o Alias Allow (para whitelisting/permitir en lugar de bloquear).
        - **Enable Logging**: Habilitado para depuración.
        - **States Removal**: Habilitado.
    - Si necesitas agregar dominios manuales, usa el ícono **+** en la lista.
3. Guarda el grupo y ve a `Firewall > pfBlockerNG > Update`.
    - Selecciona **Force Option**: Update o Reload.
    - Selecciona **Reload Option**: DNSBL.
    - Haz clic en **Run** para forzar la actualización. pfBlockerNG descargará la lista de dominios y los resolverá vía DNS.

#### 5. Configuración de Whitelisting (Permitir Tráfico)

1. Para whitelisting: Ve a `Firewall > pfBlockerNG > Reports > Alerts`.
    - Si un dominio de RingCentral aparece bloqueado, haz clic en **+** para agregarlo a la whitelist.
    - Elige wildcard (para subdominios) o específico, y confirma.
2. En las settings de la feed/grupo (en `DNSBL Groups o IP > IPv4 si usas IP resolution`):
    - Cambia **Action** a **Alias Allow** o **Permit** para que el grupo actúe como whitelist (permite tráfico en lugar de bloquear).
    - Para GeoIP (si aplica): En `Firewall > pfBlockerNG > IP > Reputation`, establece **ccWhite Action** a Allow para países relevantes (ej. EE.UU. para RingCentral).
3. pfBlockerNG genera aliases automáticamente (ej. pfB_RingCentral_Domains_v4) con las IPs resueltas de los dominios.

#### 6. Creación de Aliases y Integración en Reglas Flotantes

1. Los aliases se crean automáticamente después de la actualización. Verifícalos en **Firewall > Aliases** (busca nombres como pfB_[Grupo]_v4).
    - Si es DNSBL, los dominios se resuelven a IPs dinámicamente vía Unbound.
2. Configura auto-reglas: En **Firewall > pfBlockerNG > IP > IP Interface/Rules Configuration**.
    - Habilita **Floating Rules**.
    - Establece **Inbound/Outbound Firewall Rules** a tus interfaces (WAN para in, LAN para out).
    - **Firewall ‘Auto’ Rule Order**: Default.
    - **Kill States**: Habilitado (elimina estados bloqueados inmediatamente).
    - Guarda; pfBlockerNG genera reglas flotantes automáticamente.
3. Crea o edita la regla flotante para RingCentral:
    - Ve a **Firewall > Rules > Floating** y agrega una regla.
    - Configura como antes:
        - **Action**: Pass (para permitir).
        - **Interface**: WAN, LAN o Any.
        - **Direction**: In y Out (crea dos si necesitas bidireccional).
        - **Address Family**: IPv4.
        - **Protocol**: TCP/UDP para SIP, UDP para RTP.
        - **Source**: LAN net.
        - **Destination**: Selecciona el alias generado (ej. pfB_RingCentral_Domains_v4). ¡Ahora debería aparecer!
        - **Destination Port Range**: 5060 (SIP TCP/UDP), 10000-20000 (RTP UDP).
        - **State Type**: Sloppy State.
        - **Description**: "RingCentral VoIP - Permitir con pfBlockerNG Alias".
    - Guarda y aplica cambios. pfBlockerNG integra el alias para permitir tráfico a las IPs resueltas de los dominios.

#### 7. Verificación y Troubleshooting

1. Fuerza una actualización: **Firewall > pfBlockerNG > Update > Run**.
2. Verifica aliases: En **Firewall > pfBlockerNG > Reports > Block Stats** o **Firewall > Aliases** (hover para ver IPs).
3. Logs: **Status > System Logs > Firewall** (busca pfBlockerNG o RingCentral).
4. Pruebas: Usa nslookup para un dominio de RingCentral (debe resolver a IP real, no VIP si whitelisteado). Ejecuta el test de RingCentral y verifica estados en **Diagnostics > States**.
5. Problemas comunes:
    - Alias no aparece: Fuerza reload (Update > Reload > All) y recarga la página.
    - Recursos altos: Limita feeds o deshabilita agregación CIDR.
    - Falsos positivos: Agrega a whitelist vía Reports > Alerts.
    - Si no resuelve dominios: Verifica DNS forwarders y Unbound mode.