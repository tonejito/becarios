# Configuración de OpenVPN para Debian 8

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

+ Renombrar el certificado de cliente y descifrar la llave privada

```
    # cp -v becarios.tonejito.info.crt client.crt
    # openssl rsa -in becarios.tonejito.info.key -out client.key
```

+ Instalar OpenVPN en el servidor

```
    # apt-get install openvpn
```

+ Mover el certificado de la CA y la configuración del cliente al directorio /etc/openvpn

```
    # mv -v ca.crt client.conf /etc/openvpn
```

+ Mover el certificado y la llave privada del cliente al directorio /etc/openvpn

```
    # mv -v client.crt client.key /etc/openvpn
```

+ Editar el archivo /etc/default/openvpn y descomentar la linea AUTOSTART=all

```
    AUTOSTART="all"
```

+ Recargar la configuración de systemctl

```
    # systemctl daemon-reload
```

+ Reiniciar el servicio de OpenVPN

```
    # service openvpn restart
```
