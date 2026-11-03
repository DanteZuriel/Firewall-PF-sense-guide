Tuve el siguiente problema con el query time al hacer un dns lookup

\!\[\[Pasted image 20251226153739.png\]\]

Lo solucione de la siguiente manera

- **Active Forwarding Mode en el DNS Resolver**:  
  - Vaya a **Services \> DNS Resolver**.  
  - Marque la casilla **Enable DNS Query Forwarding** (o "DNS Forwarder" en algunas versiones).  
  - Asegúrese de que **Enable Forwarding Mode** esté activado si aparece separado.  
  - Desmarque **Enable DNSSEC Support** (temporalmente, para descartar lentitud por validación).  
  - Guarde y aplique cambios.  
  - Esto hace que Unbound forwardee las consultas directamente a los servidores de **System \> General Setup** (1.1.1.1, etc.), que responden en \<100 ms.  
- **Verifique y ajuste en System \> General Setup**:  
  - Mantenga solo Cloudflare: 1.1.1.1 y 1.0.0.1 (elimine Google si no lo necesita).  
  - DNS Hostname: cloudflare-dns.com para ambos (para soporte TLS si usa DoT).  
  - Desmarque **DNS Server Override** si está marcado.  
  - DNS Resolution Behavior: seleccione **Use local DNS (127.0.0.1), fall back to remote DNS Servers** (predeterminado y recomendado).  
- **Opcional: Active DNS over TLS para mayor seguridad y consistencia**:  
  - En **Services \> DNS Resolver** → marque **Use SSL/TLS for outgoing DNS Queries to Forwarding Servers**.  
  - Con forwarding activado, esto usa DoT a Cloudflare, que es rápido y privado.  
- **Pruebas posteriores**:  
  - Limpie caché DNS en pfSense: **Diagnostics \> Command Prompt** → ejecute unbound-control flush (o reinicie el servicio DNS Resolver).  
  - Repita la prueba en **Diagnostics \> DNS Lookup** → el tiempo en 127.0.0.1 debería bajar drásticamente (a \<100 ms, idealmente cerca de los externos).  
  - Limpie caché en su navegador/cliente (Ctrl+Shift+R o modo incógnito).  
  - Pruebe carga de RingCentral/Clio con varias pestañas abiertas.

Después tuve los siguiente resultados

\!\[\[Pasted image 20251226153928.png\]\]