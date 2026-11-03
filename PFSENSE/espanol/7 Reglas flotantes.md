- Pueden actuar en múltiples interfaces en ingreso salida o ambos  
- Si se usan varias `WANs` es recomendable no usar reglas flotantes porque el `reply-to` y sus respuestas se enviarán sólo a la `WAN` por defecto y puede generar problemas  
- Tráfico de ingreso se procesa antes de las reglas sobre las interfaces  
- Tráfico de salida se procesa después de `NAT`  
- Este tipo de reglas se pueden usar para `Traffic Shaping` o aplicar acciones de tráfico generado por el router/firewall  
- `Quick` si se desactiva se procesarán todas las reglas pero si se activa cuando hace match se parara el procesamiento y se aplicará la regla de la interfaz

# Configurando la primera regla flotante

## Diferencias

- `Action` se añade la opción match la cual no niega ni permite el tráfico pero podemos usarla para aplicar acciones secundarias por ejemplo para aplicar `QoS` `policy` `routing` `reflection` `logging` sin embargo esta regla debe ser permitida por una regla `Pass` en la interfaz de lo contrario será bloqueado por la regla anti-spoofing o default deny  
- `Interface` podemos seleccionar una o varias interfaces a manera que la regla flotante que configuremos pueda ser global  
- `Direction` tenemos 3 tipos `any` para tráfico entrante y saliente `in` para tráfico entrante y `out` para tráfico saliente

Una vez que sabemos las diferencias empezaremos probando por bloquear el `ICMP` en todas las direcciones, la siguiente imagen muestra como la configuramos

\!\[\[Pasted image 20251028155438.png\]\]

al usar ping 8.8.8.8 no lo podremos hacer debido a esta regla, sin embargo si recordamos habíamos configurado una regla en la LAN en la que dejamos pasar el tráfico por ICMP en esa dirección ip