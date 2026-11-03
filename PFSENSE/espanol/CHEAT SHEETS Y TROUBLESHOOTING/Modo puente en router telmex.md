### Pasos para configurar el modo puente en el router Telmex

1. **Accede a la interfaz**: Ingresa a [http://192.168.1.254](http://192.168.1.254/?referrer=grok.com) (usuario: TELMEX, contraseña: la clave WPA o serial de la etiqueta).
2. **Ve a la sección WAN**: Selecciona la conexión principal (generalmente la de Internet, que puede llamarse "Internet" o "TR069_Internet").
3. **Cambia el Modo WAN**:
    - Selecciona **WAN Puente** (Bridge).
    - Configura el **ID de VLAN** si se requiere (prueba con 10 inicialmente; si no conecta, valores comunes reportados son 881 o consulta logs existentes).
    - Asocia el puente a un puerto LAN específico (elige LAN1 para simplicidad; desactiva los demás si es posible para evitar conflictos).
    - Desactiva DHCP y WiFi en el router Telmex (recomendado para que pfSense maneje todo).
    - Aplica los cambios y reinicia el router Telmex (usa reinicio normal, no reset de fábrica).

**Nota importante**: Activar modo puente puro puede desactivar la telefonía VoIP (si tienes paquete con teléfono fijo), ya que depende del router Telmex. Si necesitas mantener VoIP, configura un modo híbrido (bridge solo en un puerto LAN, dejando la configuración VoIP intacta en otro).

### Configuración en pfSense (WAN)

Una vez en modo puente:

1. **Conexión física**: Conecta un cable Ethernet desde el puerto LAN configurado en puente del router Telmex al puerto WAN de pfSense.
2. **Interfaces > WAN** en pfSense:
    - Tipo de IPv4: **PPPoE**.
    - Credenciales: Usa usuario y contraseña PPPoE de Telmex (generalmente algo como "tmx" + tu número de teléfono o cuenta Prodigy; si no los tienes, solicita a soporte Telmex o extrae de la configuración actual WAN del router antes de cambiar).
    - Si se requiere VLAN: Ve a Interfaces > Assignments > VLANs, crea una VLAN con el mismo ID usado en Telmex (parent interface: el puerto WAN físico), luego asigna esa VLAN como interfaz WAN.
3. Guarda y aplica. pfSense debería obtener la IP pública directamente.

### Verificaciones y precauciones

- Prueba la conexión: Tras cambios, verifica en Status > Dashboard de pfSense que la WAN obtenga IP pública (no privada como 10.x.x.x o 100.x.x.x).
- Si no conecta: Revierte cambios (reset de fábrica en Telmex si es necesario) y regresa a DMZ como backup.
- CGNAT: Si persiste IP privada compartida, tu plan podría tenerlo (común en básicos); modo puente no lo resuelve.

Esta es la configuración óptima para pfSense como gateway principal. Si encuentras errores específicos (por ejemplo, VLAN exacta o credenciales), proporciona más detalles del modelo o capturas (sin datos sensibles) para ajustes precisos.