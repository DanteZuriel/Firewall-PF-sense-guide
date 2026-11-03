### Pasos de resolución ordenados por prioridad

1. **Verifique uso real de recursos en tiempo real**
    - Acceda al Dashboard de pfSense o ejecute top en **Diagnostics > Command Prompt**.
    - Observe durante navegación lenta:
        - CPU: ¿se mantiene >70-80 % sostenido?
        - RAM: ¿supera los 5-6 GB usados?
        - States: ¿miles de estados activos?
    - En el hypervisor (Proxmox, ESXi, Hyper-V, etc.): verifique % steal time o CPU ready time para la VM. Si >5-10 %, el host está sobrecomprometido.
2. **Cambie el tipo de NIC virtual (muy común en VMs lentas)**
    - Apague la VM pfSense.
    - En el hypervisor: cambie las interfaces de red de **VirtIO** (si usa) a **Intel e1000** o viceversa.
        - Recomendación actual: use **VirtIO** (paravirtualizado, más eficiente) pero asegúrese de que pfSense tenga los drivers (están incluidos desde 2.5+). Si no, pruebe e1000.
    - Reinicie la VM y pruebe navegación.
3. **Ajuste preciso de Limiters (reduce timeouts en HTTPS)**
    - Edite WAN_UPLOAD y WAN_DOWNLOAD:
        - Bandwidth → **90 %** exacto de su velocidad real (mida sin Limiters activos: ej. 900 Mbps si speedtest da 1000).
        - Quantum → 300 (obligatorio para paquetes pequeños de HTTPS).
        - Target → 5 ms, Interval → 100 ms.
        - Limit → 8192, Flows → 4096.
    - Si persiste, desactive temporalmente las reglas flotantes de Limiters → pruebe. Si mejora, el problema está en el shaping.
4. **Optimice DNS completamente**
    - **System > General Setup**:
        - DNS Servers: solo 1.1.1.1 y 1.0.0.1 (elimine Google si está).
        - DNS Hostname: cloudflare-dns.com para ambos.
        - Desmarque DNS Server Override.
        - DNS Resolution Behavior: Use local DNS (127.0.0.1), fall back to remote.
    - **Services > DNS Resolver**:
        - Marque **Enable Forwarding Mode**.
        - Marque **Use SSL/TLS for outgoing DNS Queries**.
    - Pruebe: **Diagnostics > DNS Lookup** → login.ringcentral.com → tiempo <50 ms.
5. **Verifique MTU y offloading**
    - **Interfaces > WAN**: MTU = 1500 (o 1492 si PPPoE).
    - **System > Advanced > Networking**: marque **Hardware Checksum Offloading**, **Hardware TCP Segmentation Offload**, **Hardware Large Receive Offload** (si la NIC virtual lo soporta).
    - En el hypervisor: asegúrese de que el bridge/switch virtual tenga MTU consistente (1500).
6. **Pruebas adicionales**
    - Bypass pfSense temporalmente: conecte un PC directamente al modem/ONT → pruebe RingCentral/Clio. Si igual de lento → problema ISP o modem.
    - Prueba de bufferbloat: waveform.com/tools/bufferbloat durante carga lenta → si grado C o peor, confirme bufferbloat.
    - Logs: **Status > System Logs > Firewall** y **General** → busque timeouts o drops durante fallos.