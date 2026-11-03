### **Pasos para generar un certificado en pfSense**

Hay dos enfoques principales para generar un certificado: **crear un certificado autofirmado** o **usar una Autoridad de Certificación (CA) interna** en pfSense y luego generar un certificado firmado por esa CA. El segundo enfoque es más profesional y recomendado para tu aprendizaje, ya que simula un entorno empresarial. A continuación, detallo el proceso usando la interfaz web de pfSense, que es más sencilla y visual.

#### a. **Acceder a la interfaz web de pfSense**

1. Abre un navegador desde una VM en la LAN (Ubuntu o Parrot con IP como 192.168.10.100) o desde el host (si configuraste un adaptador Host-Only, por ejemplo, 192.168.56.1).
2. Navega a [https://192.168.10.1](https://192.168.10.1).
3. Ignora la advertencia de certificado (por el certificado autofirmado predeterminado) e inicia sesión (usuario admin, contraseña configurada durante la instalación).

#### b. **Crear una Autoridad de Certificación (CA) interna**

1. **Navegar al administrador de certificados**:
    - Ve a **System > Cert. Manager > CAs**.
    - Haz clic en **Add** para crear una nueva CA.
2. **Configurar la CA**:
    - **Descriptive name**: Ingresa un nombre, por ejemplo, MiEmpresaCA.
    - **Method**: Selecciona **Create an internal Certificate Authority**.
    - **Key Type**: Selecciona **RSA** (recomendado para compatibilidad).
    - **Key Length**: Selecciona **2048 bits** (o 4096 para mayor seguridad, pero más lento).
    - **Digest Algorithm**: Selecciona **SHA256** (estándar seguro).
    - **Lifetime**: Ingresa 3650 (10 años) o un valor adecuado.
    - **Distinguished Name**:
        - **Common Name (CN)**: MiEmpresaCA (o un nombre que identifique tu CA).
        - **Country Code**: Selecciona tu país (por ejemplo, MX para México).
        - **State or Province**: Ingresa tu estado (por ejemplo, Ciudad de México).
        - **City**: Ingresa tu ciudad.
        - **Organization**: Ingresa el nombre de tu empresa (por ejemplo, MiEmpresa).
    - Haz clic en **Save** para crear la CA.
3. **Verificar**:
    - La nueva CA aparecerá en la lista en **System > Cert. Manager > CAs**.

#### c. **Generar un certificado para la interfaz web**

1. **Navegar a certificados**:
    - Ve a **System > Cert. Manager > Certificates**.
    - Haz clic en **Add/Sign** para crear un nuevo certificado.
2. **Configurar el certificado**:
    - **Method**: Selecciona **Create an internal Certificate**.
    - **Descriptive name**: Ingresa un nombre, por ejemplo, pfSenseWebCert.
    - **Certificate Authority**: Selecciona la CA que creaste (MiEmpresaCA).
    - **Key Type**: Selecciona **RSA**.
    - **Key Length**: Selecciona **2048 bits**.
    - **Digest Algorithm**: Selecciona **SHA256**.
    - **Lifetime**: Ingresa 3650 (10 años) o un valor adecuado.
    - **Type**: Selecciona **Server Certificate** (para la interfaz web).
    - **Distinguished Name**:
        - **Common Name (CN)**: Ingresa la IP de la LAN (192.168.10.1) o un nombre DNS si usas resolución local (por ejemplo, pfsense.local).
        - **Country Code**, **State or Province**, **City**, **Organization**: Usa los mismos valores que en la CA para consistencia.
    - **Alternative Names (opcional)**:
        - Agrega la IP 192.168.10.1 o nombres DNS adicionales (por ejemplo, pfsense.local) como **SAN (Subject Alternative Name)** para mayor compatibilidad con navegadores modernos.
        - Selecciona **Type: IP Address** o **DNS** según corresponda.
    - Haz clic en **Save** para generar el certificado.
3. **Verificar**:
    - El nuevo certificado aparecerá en **System > Cert. Manager > Certificates**.

#### d. **Aplicar el certificado a la interfaz web**

1. **Configurar la interfaz web**:
    - Ve a **System > Advanced > Admin Access**.
    - En la sección **Web GUI SSL Certificate**:
        - Selecciona el certificado que creaste (pfSenseWebCert) en el desplegable **SSL Certificate**.
    - (Opcional) Si deseas forzar HTTPS, asegúrate de que **WebGUI Redirect** esté habilitado (para redirigir HTTP a HTTPS).
    - Haz clic en **Save** y **Apply Changes**.
2. **Reiniciar la interfaz web**:
    - La interfaz web se reiniciará automáticamente. Si no, reinicia manualmente desde la consola:
        - Selecciona **opción 16 (Restart Web GUI)** en la consola de pfSense.
3. **Verificar el acceso**:
    - Desde Ubuntu o Parrot, abre un navegador y navega a [https://192.168.10.1](https://192.168.10.1).
    - Si configuraste correctamente el certificado, no debería haber advertencias (o serán mínimas si la CA no está importada en el cliente).
    - Inicia sesión para confirmar que todo funciona.

#### e. **Importar la CA en los clientes (Ubuntu/Parrot)**

Para eliminar advertencias completamente, importa la CA (MiEmpresaCA) en los clientes para que confíen en el certificado:

1. **Exportar la CA**:
    - En pfSense, ve a **System > Cert. Manager > CAs**.
    - Haz clic en el ícono de exportación (flecha hacia abajo) junto a MiEmpresaCA para descargar el archivo .crt (por ejemplo, MiEmpresaCA.crt).
    - Transfiere el archivo a las VMs de Ubuntu y Parrot (por ejemplo, usando SCP o una carpeta compartida en VirtualBox).
2. **Importar en Ubuntu**:
    - Copia el certificado a la carpeta de CAs:
        
        bash
        
        ```
        sudo cp MiEmpresaCA.crt /usr/local/share/ca-certificates/
        ```
        
    - Actualiza el almacén de certificados:
        
        bash
        
        ```
        sudo update-ca-certificates
        ```
        
    - Verifica en el navegador (por ejemplo, Firefox):
        - Abre **Preferencias > Privacidad y Seguridad > Certificados > Ver Certificados > Autoridades**.
        - Importa MiEmpresaCA.crt y marca “Confiar en esta CA para identificar sitios web”.
3. **Importar en Parrot**:
    - Similar a Ubuntu (Parrot está basado en Debian):
        
        bash
        
        ```
        sudo cp MiEmpresaCA.crt /usr/local/share/ca-certificates/
        sudo update-ca-certificates
        ```
        
    - Para el navegador, sigue el mismo proceso que en Ubuntu.
4. **Verificar**:
    - Accede a [https://192.168.10.1](https://192.168.10.1) desde Ubuntu/Parrot. No debería haber advertencias si la CA está importada correctamente.

#### f. **(Opcional) Crear un certificado autofirmado**

Si no quieres usar una CA interna (menos recomendado, pero más simple):

1. Ve a **System > Cert. Manager > Certificates**.
2. Haz clic en **Add/Sign** > **Create an internal Certificate**.
3. Configura:
    - **Method**: Create an internal Certificate.
    - **Descriptive name**: pfSenseWebCertAutofirmado.
    - **Certificate Authority**: Deja en blanco (autofirmado).
    - **Key Type**: RSA, **Key Length**: 2048 bits, **Digest Algorithm**: SHA256.
    - **Type**: Server Certificate.
    - **Common Name**: 192.168.10.1.
    - **Alternative Names**: Agrega 192.168.10.1 como IP Address.
4. Aplica el certificado en **System > Advanced > Admin Access** como se explicó.
5. Nota: Los navegadores seguirán mostrando advertencias, ya que el certificado no está firmado por una CA confiable.

### 3. **Verificar la configuración de red**

Para asegurar que el acceso a la interfaz web funcione:

- **pfSense**:
    - Confirma que em2 (LAN) tiene la IP 192.168.10.1/24 en **Status > Interfaces**.
    - Verifica que el DHCPv4 esté habilitado en **Services > DHCP Server > LAN** (rango 192.168.10.100-200).
    - Confirma que DHCPv6 y Router Advertisements estén desactivados en **Services > DHCPv6 Server & RA > LAN**.
    - Revisa las reglas de firewall en **Firewall > Rules > LAN** para permitir TCP/443 (HTTPS) a 192.168.10.1.
- **Ubuntu/Parrot**:
    - Verifica que estén en el mismo **Internal Network** (intnet) que em2.
    - Confirma que tengan IPs en 192.168.10.0/24 (por ejemplo, 192.168.10.100 para Ubuntu, 192.168.10.101 para Parrot).
    - Haz ping a 192.168.10.1 y prueba curl https://192.168.10.1.
- **Acceso desde el host**:
    - Si necesitas acceso desde el host, configura un adaptador Host-Only en pfSense (por ejemplo, Adaptador 4, IP 192.168.56.1/24) y aplica el certificado también a esa interfaz. 

### **Recomendaciones para avanzar**

- **Verificar el certificado**:
    - Accede a [https://192.168.10.1](https://192.168.10.1) desde Ubuntu/Parrot y confirma que no hay advertencias (después de importar la CA).
    - Usa curl https://192.168.10.1 para verificar la conexión HTTPS.
- **Importar la CA en el host**:
    - Si accedes desde el host (con un adaptador Host-Only), importa MiEmpresaCA.crt en el navegador o sistema operativo del host.
- **Evitar conflictos**: Verifica que 192.168.10.0/24 no esté en uso en la red física (Netgear, Steren, router/módem) con nmap -sn 192.168.10.0/24.