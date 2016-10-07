# Configuración de OpenVPN para Debian 8

## cliente headless (openvpn como servicio)

+ Descargar el certificado de la CA y la configuración del cliente de la siguiente ubicación

https://becarios.tonejito.info/vpn/

```
# ls -l
-rw-r--r-- 1 root root 2236 Oct  7 15:55 ca.crt
-rw-r--r-- 1 root root 3472 Oct  7 15:55 client.conf
```

+ Verificar que se tenga el certificado y llave de cliente en el directorio home de `root` o de `unix`

```
# ls -l
-rw-r--r-- 1 root root 2228 Oct  7 16:50 becarios.tonejito.info.crt
-rw-r--r-- 1 root root 3311 Oct  7 16:21 becarios.tonejito.info.key
```

+ Renombrar el certificado de cliente y descifrar la llave privada, la contraseña es "`becarios`" (sin comillas)

```
# cp -v becarios.tonejito.info.crt client.crt
# openssl rsa -in becarios.tonejito.info.key -out client.key
```

+ Instalar OpenVPN en el servidor

```
# apt-get install openvpn
```

+ Mover el certificado de la CA y la configuración del cliente al directorio `/etc/openvpn`

```
# mv -v ca.crt client.conf /etc/openvpn
```

+ Mover el certificado y la llave privada del cliente al directorio `/etc/openvpn`

```
# mv -v client.crt client.key /etc/openvpn
```

+ Editar el archivo `/etc/default/openvpn` y descomentar la linea `AUTOSTART=all`

```
AUTOSTART="all"
```

+ Recargar la configuración de `systemctl`

```
# systemctl daemon-reload
```

+ Reiniciar el servicio de OpenVPN

```
# service openvpn restart
```

## Cliente con ambiente gráfico

### Gnome

+ Instalar el paquete de openvpn para NetwokManager

```
# apt-get install network-manager-openvpn-gnome
```
### Mac OS X

+ Instalar TunnelBlick

https://tunnelblick.net/

+ Crear un directorio tblk

```
% mkdir becarios
```

+ Copiar la configuración, certificados y llaves al directorio tblk

```
% cp -v client.conf ca.crt client.crt client.key becarios
```

+ Renombrar el directorio, debe cambiar el icono que aparece en el Finder

```
% mv -v becarios becarios.tblk
% cd becarios.tblk
% cp -v client.conf client.ovpn
```

+ Dar doble clic en el directorio becarios.tblk y seguir las instrucciones de TunnelBlick

+ Más información

https://tunnelblick.net/documents.html
https://www.privateinternetaccess.com/pages/client-support/osx-openvpn-tunnelblick

### Windows

https://www.asus.com/support/faq/1004469/
