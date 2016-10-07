# UNAM-CERT - Plan de Becarios G11 2016
## Proyecto Administración y Seguridad en Linux / UNIX

Descripción de los servidores

### WEB

 Servicios de red | Aplicaciones
------------------|-------------
 http             | Drupal
 https            |

Descripción

* Debe guardar los htdocs de las aplicaciones en el servidor STORAGE
* Se deben instalar dos instancias de la aplicación web Drupal
  * Deben autenticar a los usuarios por medio del directorio LDAP ubicado en el servidor DIRECTORY
  * Una instancia guardará su base de datos en MySQL y otra en PostgreSQL


### MAIL

 Servicios de red | Aplicaciones
------------------|-------------
 smtp             | 
 submission       |
 imaps            |

Descripción

* Debe implementar los servicios **smtp** y **submission** con Postfix
* El servicio **imaps** puede ser implementado con cualquier software
* Los usuarios de correo deben ser autenticados con el directorio LDAP ubicado en el servidor DIRECTORY
* Los buzones de correo deben almacenarse en el servidor STORAGE, se recomienda utilizar Maildir como formato de buzón

### DIRECTORY

 Servicios de red | Aplicaciones
------------------|-------------
 ldap             | ldap-account-manager
 ldaps            | ldap-toolbox (ltb) self-service


Descripción

* Este equipo provee el servicio de directorio para los demás equipos
* Debe proporcionar conexiones a través del servicio **ldaps**, el uso del servicio **ldap** debe ser interno en el mismo host
* El servicio de directorio debe ser privado, es decir no debe aceptar conexiones ldap desde equipos externos
* Se debe garantizar el servicio puesto que los otros hosts dependen de el

### STORAGE

 Servicios de red | Aplicaciones
------------------|-------------
 nfs              | ownCloud
                  | cuotas

Descripción

* Este equipo provee el servicio **nfs** para establecer un almacenamiento compartido para que los demas equipos puedan guardar los datos de las aplicaciones
* Las cuentas de usuario deben ser leidas desde el directorio LDAP ubicado en el servidor DIRECTORY
* Se deben establecer **cuotas** en el sistema de archivos para los usuarios que se obtengan de LDAP
* Para dar acceso a los archivos almacenados se debe proporcionar el servicio **ownCloud** autenticando a los usuarios por medio del sistema operativo o de LDAP
* Se debe garantizar el servicio puesto que los otros hosts dependen de el

### DATABASE

Servicios de red

* mysql
* pgsql
* syslog
 Servicios de red | Aplicaciones
------------------|-------------
 mysql            | 
 pgsql            | 
 syslog           |

Descripción

* Este equipo proporcionará los servicios de base de datos **MySQL** y **PostgreSQL** para que los equipos puedan procesar sus bases de datos en el
* Se debe garantizar el servicio puesto que los otros hosts dependen de el
* Los archivos binarios que contienen la base de datos deben ser guardados en el servidor STORAGE
* Este equipo debe servir como concentrador de bitacoras utilizando el protocolo syslog
* Las bitácoras recolectadas deben guardarse en el servidor STORAGE como archivos separados dependiendo del host del que provengan

	/ruta/hacia/bitacoras
	  10.0.0.1.log
	  172.16.0.1.log
	  192.168.0.1.log
