Para asegurar de que el firewall sea expuesto en la WAN debemos hacer una configuración en el router activando [[Modo puente en router telmex]] sin embargo si no es posible el modo puente en el router se recomienda lo siguiente para que pfsense sea el encargado de gestionar los paquetes de Internet

# DMZ en router

En la siguiente imagen podremos ver la configuración de un router de telnor, en la opción de DMZ solo tenemos que agregar la ip del firewall de esta manera el firewall es el que sera expuesto a Internet en lugar de que sea el router

![Pasted image 20251217135009.png](../IMAGES/Pasted%20image%2020251217135009.png)

