1. Primero generamos las claves ssh
usamos RSA para mayor seguridad
```shell
ssh-keygen -t rsa -b 4096 -f vps-ssh
```

2. La clave privada ssh y la carpeta donde se alojan deben tener los siguientes permisos

```shell
chmod 700 /home/kali/.ssh # permisos para la carpeta ssh
chmod 600 /hone.kali/.ssh/id_ed25519 # permisos para la clave privada
```

3. Realizamos ajustes en la configuración ssh los cuales se encuentran el la ruta `/etc/ssh/sshd_config`
4. creamos reglas con `ufw` o `iptables` para el acceso al puerto 22
5. Dentro de la carpeta ssh puedes agregar claves publicas de otras maquinas sin embargo la configuración de permitpublickey del archivo de configuración `sshd_config` debe estar activada
6. usamos systemctl para iniciar el servicio, reiniciar el daemon de ssh o habilitar el servicio, por ejemplo
```shell
# comando para habilitar el servicio
sudo systemctl enable ssh

# comando para reiniciar el servicio
sudo systemctl reload ssh
```

6. Por ultimo recuerda tener descargado openssh para que puedas iniciar el servicio