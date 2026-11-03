### Configuración recomendada para usarlo como AP

La mayoría de modelos Telmex no incluyen un "modo Access Point" dedicado en su interfaz estándar, pero se logra manualmente con los siguientes pasos (basados en guías actualizadas y experiencias reportadas en comunidades técnicas al 2025):

1. **Desactivar funciones de router en Telmex**:
    - Deshabilite el servidor **DHCP** (en secciones como LAN > DHCP Server).
    - Desactive el firewall y NAT si es posible (o minimícelos).
    - Cambie la IP LAN del router Telmex a una estática en el rango de pfSense (por ejemplo, si pfSense usa 192.168.1.1, asigne 192.168.1.2 al Telmex para evitar conflictos).
    - Mantenga activo el WiFi con el SSID y contraseña deseados (puede configurar bandas 2.4 GHz y 5 GHz).
2. **Conexión física**:
    - Conecte un cable Ethernet desde un puerto LAN de pfSense (o switch conectado a pfSense) a un puerto LAN del router Telmex (nunca al puerto WAN, para evitar que actúe como router).
3. **Ajustes adicionales**:
    - Desactive UPnP y cualquier servicio de enrutamiento avanzado en Telmex.
    - En pfSense, asegúrese de que el DHCP cubra los dispositivos conectados al Telmex (el tráfico pasará a través de pfSense como gateway principal).

Con esto, el router Telmex funcionará exclusivamente como emisor WiFi (AP), extendiendo la señal en 2.4 GHz y 5 GHz, mientras pfSense gestiona DHCP, reglas de firewall, filtrado y seguridad.