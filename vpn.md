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

+ Verificar criptográficamente que el certificado corresponda a la llave privada

  * Comprobar la firma SHA1 del módulo de la llave privada

```
$ openssl rsa -in client.key -noout -modulus | sha1sum -
42f6fa9d842f1acd9a6543fe93a1eed1f871c10c  -
```

  * Comprobar la firma SHA1 del módulo de la llave pública que contiene el certificado. La cadena **debe** ser idéntica a la de la llave privada

```
$ openssl x509 -in client.crt -noout -modulus | sha1sum -
42f6fa9d842f1acd9a6543fe93a1eed1f871c10c  -
```

+ Detener el servicio `openvpn`

```
# service openvpn stop
```

+ Iniciar `openvpn` manualmente. Después de algunos segundos debe aparecer el mensaje `Initialization Sequence Completed`

```
# openvpn --verb 3 --config client.conf
Sun Oct  9 11:34:10 2016 OpenVPN 2.2.1 x86_64-linux-gnu [SSL] [LZO2] [EPOLL] [PKCS11] [eurephia] [MH] [PF_INET6] [IPv6 payload 20110424-2 (2.2RC2)] built on Dec  1 2014
Sun Oct  9 11:34:10 2016 NOTE: OpenVPN 2.1 requires '--script-security 2' or higher to call user-defined scripts or executables
Sun Oct  9 11:34:10 2016 WARNING: file 'client.key' is group or others accessible
Sun Oct  9 11:34:10 2016 LZO compression initialized
Sun Oct  9 11:34:10 2016 Control Channel MTU parms [ L:1542 D:138 EF:38 EB:0 ET:0 EL:0 ]
Sun Oct  9 11:34:10 2016 Socket Buffers: R=[212992->131072] S=[212992->131072]
Sun Oct  9 11:34:10 2016 Data Channel MTU parms [ L:1542 D:1450 EF:42 EB:135 ET:0 EL:0 AF:3/1 ]
Sun Oct  9 11:34:10 2016 Local Options hash (VER=V4): '41690919'
Sun Oct  9 11:34:10 2016 Expected Remote Options hash (VER=V4): '530fdded'
Sun Oct  9 11:34:10 2016 UDPv4 link local: [undef]
Sun Oct  9 11:34:10 2016 UDPv4 link remote: [AF_INET]104.245.36.21:1195
Sun Oct  9 11:34:11 2016 TLS: Initial packet from [AF_INET]104.245.36.21:1195, sid=e2a73e09 3453d8e8
Sun Oct  9 11:34:11 2016 VERIFY OK: depth=1, /C=MX/ST=CDMX/L=Coyoacan/O=UNAM-CERT/OU=Plan_de_Becarios/CN=ca.becarios.tonejito.info/emailAddress=admin@becarios.tonejito.info
Sun Oct  9 11:34:11 2016 Validating certificate key usage
Sun Oct  9 11:34:11 2016 ++ Certificate has key usage  00a0, expects 00a0
Sun Oct  9 11:34:11 2016 VERIFY KU OK
Sun Oct  9 11:34:11 2016 Validating certificate extended key usage
Sun Oct  9 11:34:11 2016 ++ Certificate has EKU (str) TLS Web Server Authentication, expects TLS Web Server Authentication
Sun Oct  9 11:34:11 2016 VERIFY EKU OK
Sun Oct  9 11:34:11 2016 VERIFY OK: depth=0, /C=MX/ST=CDMX/L=Coyoacan/O=UNAM-CERT/OU=Plan_de_Becarios/CN=vpn.becarios.tonejito.info/emailAddress=admin@becarios.tonejito.info
Sun Oct  9 11:34:13 2016 Data Channel Encrypt: Cipher 'BF-CBC' initialized with 128 bit key
Sun Oct  9 11:34:13 2016 Data Channel Encrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
Sun Oct  9 11:34:13 2016 Data Channel Decrypt: Cipher 'BF-CBC' initialized with 128 bit key
Sun Oct  9 11:34:13 2016 Data Channel Decrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
Sun Oct  9 11:34:13 2016 Control Channel: TLSv1, cipher TLSv1/SSLv3 DHE-RSA-AES256-SHA, 4096 bit RSA
Sun Oct  9 11:34:13 2016 [vpn.becarios.tonejito.info] Peer Connection Initiated with [AF_INET]104.245.36.21:1195
Sun Oct  9 11:34:15 2016 SENT CONTROL [vpn.becarios.tonejito.info]: 'PUSH_REQUEST' (status=1)
Sun Oct  9 11:34:15 2016 PUSH: Received control message: 'PUSH_REPLY,route 172.16.0.0 255.255.255.0,topology net30,ping 10,ping-restart 120,ifconfig 172.16.0.142 172.16.0.141'
Sun Oct  9 11:34:15 2016 OPTIONS IMPORT: timers and/or timeouts modified
Sun Oct  9 11:34:15 2016 OPTIONS IMPORT: --ifconfig/up options modified
Sun Oct  9 11:34:15 2016 OPTIONS IMPORT: route options modified
Sun Oct  9 11:34:15 2016 ROUTE default_gateway=192.168.1.1
Sun Oct  9 11:34:15 2016 TUN/TAP device tun0 opened
Sun Oct  9 11:34:15 2016 TUN/TAP TX queue length set to 100
Sun Oct  9 11:34:15 2016 do_ifconfig, tt->ipv6=0, tt->did_ifconfig_ipv6_setup=0
Sun Oct  9 11:34:15 2016 /sbin/ifconfig tun0 172.16.0.142 pointopoint 172.16.0.141 mtu 1500
Sun Oct  9 11:34:15 2016 /sbin/route add -net 172.16.0.0 netmask 255.255.255.0 gw 172.16.0.141
Sun Oct  9 11:34:15 2016 Initialization Sequence Completed
```

+ En otra terminal, ver la configuración de la interfaz `tun0`, debería tener una dirección IP del segmento `172.16.0.0/24`

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

+ Si todos los datos son correctos, presionar `Control-C` para cacelar `openvpn`

```
Sun Oct  9 11:34:15 2016 Initialization Sequence Completed
^C
Sun Oct  9 11:34:16 2016 event_wait : Interrupted system call (code=4)
Sun Oct  9 11:34:16 2016 TCP/UDP: Closing socket
Sun Oct  9 11:34:16 2016 /sbin/route del -net 172.16.0.0 netmask 255.255.255.0
Sun Oct  9 11:34:16 2016 Closing TUN/TAP interface
Sun Oct  9 11:34:16 2016 /sbin/ifconfig tun0 0.0.0.0
Sun Oct  9 11:34:16 2016 SIGINT[hard,] received, process exiting
```

+ Iniciar el servicio `openvpn`

```
# service openvpn restart
```
