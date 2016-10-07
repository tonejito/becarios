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

## Diagnóstico de conexión

+ Ver la configuración de la interfaz `tun0`, debería tener una dirección IP del segmento `172.16.0.0/24`

```
# ifconfig -a tun0
tun0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  
          inet addr:172.16.0.6  P-t-P:172.16.0.5  Mask:255.255.255.255
          inet6 addr: fe80::3ae9:2a6e:affa:b4f7/64 Scope:Link
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500  Metric:1
          RX packets:39 errors:0 dropped:0 overruns:0 frame:0
          TX packets:42 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:100 
          RX bytes:3276 (3.1 KiB)  TX bytes:3420 (3.3 KiB)
```
+ Revisar la tabla de rutas, deben existir rutas por la interfaz `tun0`

```
# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
...
172.16.0.0      172.16.0.5      255.255.255.0   UG    0      0        0 tun0
172.16.0.5      0.0.0.0         255.255.255.255 UH    0      0        0 tun0
```

+ Revisar la conectividad utilizando `ping` hacia `172.16.0.1` y `172.16.0.6`

```
# ping -c 4 172.16.0.1
PING 172.16.0.1 (172.16.0.1) 56(84) bytes of data.
64 bytes from 172.16.0.1: icmp_seq=1 ttl=64 time=75.5 ms
64 bytes from 172.16.0.1: icmp_seq=2 ttl=64 time=75.5 ms
64 bytes from 172.16.0.1: icmp_seq=3 ttl=64 time=75.6 ms
64 bytes from 172.16.0.1: icmp_seq=4 ttl=64 time=75.6 ms

--- 172.16.0.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 75.553/75.610/75.680/0.202 ms
```
```
# ping -c 4 172.16.0.6
PING 172.16.0.6 (172.16.0.6) 56(84) bytes of data.
64 bytes from 172.16.0.6: icmp_seq=1 ttl=64 time=0.024 ms
64 bytes from 172.16.0.6: icmp_seq=2 ttl=64 time=0.038 ms
64 bytes from 172.16.0.6: icmp_seq=3 ttl=64 time=0.029 ms
64 bytes from 172.16.0.6: icmp_seq=4 ttl=64 time=0.034 ms

--- 172.16.0.6 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 2997ms
rtt min/avg/max/mdev = 0.024/0.031/0.038/0.006 ms
```
