---
title: 'Configurar una IP como alias'
slug: network-ipaliasing
excerpt: 'Cómo añadir direcciones Additional IP a la configuración de un servidor'
section: 'Red e IP'
---

> [!primary]
> Esta traducción ha sido generada de forma automática por nuestro partner SYSTRAN. En algunos casos puede contener términos imprecisos, como en las etiquetas de los botones o los detalles técnicos. En caso de duda, le recomendamos que consulte la versión inglesa o francesa de la guía. Si quiere ayudarnos a mejorar esta traducción, por favor, utilice el botón «Contribuir» de esta página.
>

**Última actualización: 10/11/2022**

> [!primary]
>
> Desde el 6 de octubre de 2022, nuestra solución "Failover IP" se denomina desde ahora [Additional IP](https://www.ovhcloud.com/es/network/additional-ip/). Esto no afectará a sus funcionalidades ni al funcionamiento de sus servicios.
>

## Objetivo

El alias de IP (*IP aliasing* en inglés) es una configuración especial de la red de un servidor que permite asociar varias direcciones IP a una única interfaz de red.

**Esta guía explica cómo realizar la operación.**

## Requisitos

- Tener un servidor ([servidor dedicado](https://www.ovhcloud.com/es/bare-metal/){.external}, [VPS](https://www.ovhcloud.com/es/vps/){.external} o [instancia de Public Cloud](https://www.ovhcloud.com/es/public-cloud/){.external}).
- Tener una o más direcciones [Additional IP](https://www.ovhcloud.com/en/bare-metal/ip/){.external}.
- Estar conectado al servidor por SSH (acceso *root*).

> [!warning]
> Esta funcionalidad puede no estar disponible o estar limitada en los [servidores dedicados **Eco**](https://eco.ovhcloud.com/es/about/).
>
> Para más información, consulte nuestra [comparativa](https://eco.ovhcloud.com/es/compare/).

## Procedimiento

A continuación se explica el procedimiento de configuración en las principales distribuciones.

### Debian 10/11

#### 1. Crear una copia de seguridad del archivo de configuración

En primer lugar, realice una copia de seguridad del archivo de configuración de las interfaces para poder volver atrás en cualquier momento.

```sh
cp /etc/network/interfaces.d/50-cloud-init /etc/network/interfaces.d/50-cloud-init.bak
```

#### 2. Editar el archivo de configuración

Ya puede editar el archivo.

```sh
editor /etc/network/interfaces.d/50-cloud-init
```

A continuación, añada una interfaz secundaria.

```bash
auto eth0:0
iface eth0:0 inet static
address ADDITIONAL_IP
netmask 255.255.255.255
```

Para asegurarse de que la interfaz secundaria se active junto con la interfaz **eth0**, añada las siguientes líneas a la configuración de eth0:

```bash
post-up /sbin/ifconfig eth0:0 ADDITIONAL_IP netmask 255.255.255.255 broadcast ADDITIONAL_IP
pre-down /sbin/ifconfig eth0:0 down
```

Si quiere configurar dos Additional IP, el archivo **/etc/network/interfaces.d/50-cloud.init** debería ser similar al siguiente:

```bash
auto eth0
iface eth0 inet dhcp

auto eth0:0
iface eth0:0 inet static
address ADDITIONAL_IP1
netmask 255.255.255.255

auto eth0:1
iface eth0:1 inet static
address ADDITIONAL_IP2
netmask 255.255.255.255
```
O como esto:

```bash
auto eth0
iface eth0 inet dhcp

# IP 1
post-up /sbin/ifconfig eth0:0 ADDITIONAL_IP1 netmask 255.255.255.255 broadcast ADDITIONAL_IP1
pre-down /sbin/ifconfig eth0:0 down

# IP 2
post-up /sbin/ifconfig eth0:1 ADDITIONAL_IP2 netmask 255.255.255.255 broadcast ADDITIONAL_IP2
pre-down /sbin/ifconfig eth0:1 down
```


#### 3. Reiniciar la interfaz

Por último, reinicie la interfaz con el siguiente comando:

```sh
/etc/init.d/networking restart
```

### Debian 6, 7 y 8 (y derivados)

#### 1. Crear una copia de seguridad del archivo de configuración

En primer lugar, realice una copia de seguridad del archivo de configuración de las interfaces para poder volver atrás en cualquier momento.

```sh
cp /etc/network/interfaces /etc/network/interfaces.bak
```

#### 2. Editar el archivo de configuración

Ya puede editar el archivo.

```sh
editor /etc/network/interfaces
```

A continuación, añada una interfaz secundaria.

```
auto eth0:0
iface eth0:0 inet static
address ADDITIONAL_IP
netmask 255.255.255.255
```

Para asegurarse de que la interfaz secundaria se active junto con la interfaz **eth0**, añada las siguientes líneas a la configuración de eth0:

```
post-up /sbin/ifconfig eth0:0 ADDITIONAL_IP netmask 255.255.255.255 broadcast ADDITIONAL_IP
pre-down /sbin/ifconfig eth0:0 down
```

Si quiere configurar dos Additional IP, el archivo **/etc/network/interfaces** debería ser similar al siguiente:

```
auto eth0
iface eth0 inet static
address SERVER_IP
netmask 255.255.255.0
broadcast xxx.xxx.xxx.255
gateway xxx.xxx.xxx.254

auto eth0:0
iface eth0:0 inet static
address ADDITIONAL_IP1
netmask 255.255.255.255

auto eth0:1
iface eth0:1 inet static
address ADDITIONAL_IP2
netmask 255.255.255.255
```

```
# IP 1
post-up /sbin/ifconfig eth0:0 ADDITIONAL_IP1 netmask 255.255.255.255 broadcast ADDITIONAL_IP1
pre-down /sbin/ifconfig eth0:0 down

# IP 2
post-up /sbin/ifconfig eth0:1 ADDITIONAL_IP2 netmask 255.255.255.255 broadcast ADDITIONAL_IP2
pre-down /sbin/ifconfig eth0:1 down
```


#### 3. Reiniciar la interfaz

Por último, reinicie la interfaz con el siguiente comando:

```sh
/etc/init.d/networking restart
```


### Debian 9+, Ubuntu 17+, Fedora 26+

En estas distribuciones, el método de denominación de las interfaces como eth0, eth1, etc. cambia, por lo que utilizaremos de forma más genérica el **systemd-network**.

#### 1. Crear una copia de seguridad del archivo de configuración

En primer lugar, realice una copia de seguridad del archivo de configuración de las interfaces para poder volver atrás en cualquier momento.

```sh
cp /etc/systemd/network/50-default.network /etc/systemd/network/50-default.network.bak
```

#### 2. Editar el archivo de configuración

Ya puede añadir sus Additional IP editando el archivo de la siguiente manera:

```sh
nano /etc/network/50-default.network
```

```
[Address]
Address=22.33.44.55/32
Label=failover1 # optional
```

El **label** es opcional. Se utiliza para ordenar las diferentes Additional IP.

#### 3. Reiniciar la interfaz

Por último, reinicie la interfaz con el siguiente comando:

```sh
systemctl restart systemd-networkd
```


### Arch (systemctl)

#### 0. Añadir interfaz temporal

Para probar primero la IP, añadiremos en caliente la IP la interfaz de red que queramos que escuche (lo podemos averiguar con ```ifconfig```). Por defecto la interfaz principal suele ser ```eth0```.

El parámetro ``label eth0:fo1`` es totalmente opcional.

```sh
ip address add 22.33.44.55/32 broadcast + dev eth0 label eth0:fo1
```

Si podemos hacer peticiones, es el momento de eliminar la IP y crear el servicio.

```sh
ip addr del 22.33.44.55/32 dev eth0
```

#### 1. Crear el servicio

Creamos un nuevo servicio.

```sh
 /etc/systemd/system/failoverIP.service
```

```
[Unit]
Description=Failover Adding IP on boot
# Wait until network is up
Wants=network-online.target
After=network.target network-online.target

[Service]
# Only start once when network works
Type=oneshot
ExecStart=/usr/bin/failoverIP

[Install]
WantedBy=multi-user.target
```

#### 2. Crear el script del servicio

```sh
/usr/bin/failoverIP
```

Aprovecharemos el comando del paso 0
```
#!/bin/bash
ip address add 22.33.44.55/32 broadcast + dev eth0 label eth0:fo1
```

#### 3. Activación del servicio

Por último, tendremos que habilitar el servicio y arrancarlo (si hemos eliminado la IP en el paso 0).

```sh
chmod 755 /usr/bin/failoverIP
systemctl enable failoverIP.service
systemctl start failoverIP.service
``` 


### CentOS y Fedora (25 y anteriores)

#### 1. Crear una copia de seguridad del archivo de configuración

En primer lugar, realice una copia de seguridad del archivo de configuración de las interfaces para poder usarlo como modelo.

```sh
cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth0:0
```

#### 2. Editar el archivo de configuración

Ya puede editar el archivo **eth0:0** para sustituir la IP.

```sh
editor /etc/sysconfig/network-scripts/ifcfg-eth0:0
```

Ahora cambie el nombre del **device** y, luego, la IP existente por su Additional IP.

```
DEVICE="eth0:0"
ONBOOT="yes"
BOOTPROTO="none" # For CentOS use "static"
IPADDR="ADDITIONAL_IP"
NETMASK="255.255.255.255"
BROADCAST="ADDITIONAL_IP"
```

#### 3. Reinicio de la interfaz

Por último, reinicie la interfaz con el siguiente comando:

```sh
ifup eth0:0
```


### Gentoo

#### 1. Crear una copia de seguridad del archivo de configuración

En primer lugar, realice una copia de seguridad del archivo de configuración de las interfaces para poder volver atrás en cualquier momento.

```sh
cp /etc/conf.d/net /etc/conf.d/net.bak
```

#### 2. Editar el archivo de configuración

Edite el archivo para añadir la Additional IP. En Gentoo, los alias se añaden directamente a la interfaz **eth0**, por lo que no hay que crear la interfaz **eth0:0** (como sí ocurre en Red Hat o CentOS).

> [!warning]
>
> La IP por defecto del servidor debe estar en la misma línea que **config_eth0 =** para garantizar el buen funcionamiento de determinadas operaciones específicas de OVHcloud.
> 

Inserte un salto de la línea después de la máscara de red **255.255.255.0** y añada su Additional IP (sustituya «SERVER_IP» por la IP principal de su servidor).

```sh
editor /etc/conf.d/net
```

Añada lo siguiente:

```
config_eth0=( "SERVER_IP netmask 255.255.255.0" "ADDITIONAL_IP netmask 255.255.255.255 brd ADDITIONAL_IP" )
```

El archivo **/etc/conf.d/net** debe contener lo siguiente:


```
# This blank configuration will automatically use DHCP for any net.
# scripts in /etc/init.d. To create a more complete configuration,
# please review /etc/conf.d/net.example and save your configuration
# in /etc/conf.d/net (this file :]!).
config_eth0=( "SERVER_IP netmask 255.255.255.0"
"ADDITIONAL_IP netmask 255.255.255.255 brd ADDITIONAL_IP" )
routes_eth0=( "default gw SERVER_IP.254" )
```

Para poder hacer *ping* a su Additional IP, solo tiene que reiniciar la interfaz de red.


#### 3. Reiniciar la interfaz

Por último, reinicie la interfaz con el siguiente comando:

```sh
/etc/init.d/net.eth0 restart
```


### openSUSE

#### 1. Crear una copia de seguridad del archivo de configuración

En primer lugar, realice una copia de seguridad del archivo de configuración de las interfaces para poder volver atrás en cualquier momento.

```sh
cp /etc/sysconfig/network/ifcfg-ens32 /etc/sysconfig/network/ifcfg-ens32.bak
```

#### 2. Editar el archivo de configuración

A continuación, edite el archivo **/etc/sysconfig/network/ifcfg-ens32** como sigue:

```
IPADDR_1=ADDITIONAL_IP
NETMASK_1=255.255.255.255
LABEL_1=ens32:0
```


### cPanel

#### 1. Crear una copia de seguridad del archivo de configuración

En primer lugar, realice una copia de seguridad del archivo de configuración de las interfaces para poder volver atrás en cualquier momento.

```sh
cp /etc/ips /etc/ips.bak
```

#### 2. Editar el archivo de configuración

Edite el archivo **/etc/ips**.

```sh
editor /etc/ips
```
Añada la Additional IP al archivo.

```bash
ADDITIONAL_IP:255.255.255.255:ADDITIONAL_IP
```
Y después añada la IP en **/etc/ipaddrpool**.


```bash
ADDITIONAL_IP
```

#### 3. Reiniciar la interfaz

Por último, reinicie la interfaz con el siguiente comando:

```sh
/etc/init.d/ipaliases restart
```

### Windows Server

Los servidores Windows suelen usar DHCP para la configuración de red. Si ya ha configurado una Additional IP, o si ha cambiado su configuración a IP fija, vaya directamente al siguiente paso.

De lo contrario, debe cambiar primero la configuración de red de DHCP a configuración de IP fija.

Abra la consola de comandos **CMD** o **PowerShell** e introduzca el siguiente comando:

```sh
ipconfig /all
```

La respuesta tendrá el formato de la imagen:

![Respuesta del comando ipconfig /all](images/guides-network-ipaliasing-windows-2008-1.png){.thumbnail}

En ella puede consultar su IPv4, la máscara de subred, la puerta de enlace predeterminada y el nombre de la tarjeta de red.

En nuestro ejemplo, la IP del servidor es **94.23.229.151**.

Los siguientes pasos pueden realizarse, bien mediante línea comandos, o bien a través de la interfaz gráfica.

#### Mediante línea de comandos (recomendado)

En los comandos indicados más abajo, sustituya los siguientes valores:

|Comando|Valor|
|---|---|
|NETWORK_ADAPTER| Nombre de la tarjeta de red (en el ejemplo, Local Area Connection)|
|IP_ADDRESS| Dirección IP del servidor (en el ejemplo, 94.23.229.151)|
|SUBNET_MASK| Máscara de subred (en el ejemplo, 255.255.255.0)|
|GATEWAY| Puerta de enlace predeterminada (en el ejemplo, 94.23.229.254)|
|ADDITIONAL_IP| Dirección Additional IP que quiera añadir|

> [!warning]
>
> Atención: Si introduce información incorrecta, no será posible acceder al servidor. En ese caso, deberá realizar las correcciones oportunas en modo WinRescue o a través del KVM.
> 

Realice las acciones que se indican a continuación en la consola de comandos.

Cambie a IP fija.

```
netsh interface ipv4 set address name="NETWORK_ADAPTER" static IP_ADDRESS SUBNET_MASK GATEWAY
```
 
Indique el servidor DNS.

```
netsh interface ipv4 set dns name="NETWORK_ADAPTER" static 213.186.33.99
```

Añada una Additional IP.

```
netsh interface ipv4 add address "NETWORK_ADAPTER" ADDITIONAL_IP 255.255.255.255
```

Su Additional IP ya estará activa.


#### A través de la interfaz gráfica

Vaya a `Inicio`{.action} > `Panel de control`{.action} > `Redes e Internet`{.action} > `Centro de redes y recursos compartidos`{.action}. En el menú izquierdo, seleccione `Cambiar configuración del adaptador`{.action}.

Haga clic derecho en `Conexión de área local`{.action} y seleccione `Propiedades`{.action}.

Haga clic en `Protocolo de Internet versión 4 (TCP/IPv4)`{.action} y, a continuación, haga clic en el botón `Propiedades`{.action}.

Marque la opción `Usar la siguiente dirección IP`{.action} e introduzca la IP principal del servidor, la máscara de subred y la puerta de enlace predeterminada obtenidas anteriormente con el comando `ipconfig`. En **servidor DNS preferido**, introduzca **213.186.33.99**.

![Propiedades del protocolo de internet versión 4 (TCP/IPv4)](images/guides-network-ipaliasing-windows-2008-2.png){.thumbnail}


> [!warning]
>
> Atención: Si introduce información incorrecta, no será posible acceder al servidor. En ese caso, deberá realizar las correcciones oportunas en modo WinRescue o a través del KVM.
> 

A continuación, haga clic en `Opciones avanzadas`{.action} (todavía en las propiedades del protocolo de internet versión 4).

![Propiedades del protocolo de internet versión 4 (TCP/IPv4)](images/guides-network-ipaliasing-windows-2008-2.png){.thumbnail}

En la sección **Direcciones IP**, haga clic en `Agregar`{.action}.

![Opciones avanzadas TCP/IPv4](images/guides-network-ipaliasing-windows-2008-3.png){.thumbnail}

Introduzca ahí su Additional IP y la máscara de subred **255.255.255.255**.

![Dirección TCP/IP](images/guides-network-ipaliasing-windows-2008-4.png){.thumbnail}

Haga clic en `Agregar`{.action}.

Su Additional IP ya estará activa.

### Plesk

#### Paso 1: acceder a la gestión de IP de Plesk

En el panel de configuración de Plesk, seleccione `Tools & Settings`{.action} en la columna izquierda.

![acceso a la gestión de las direcciones IP](images/pleskip1.png){.thumbnail}

Haga clic en `IP Addresses`{.action} bajo **Tools & Settings**.

#### Paso 2: añadir la información IP adicional

En esta sección, haga clic en el botón `Add IP Address`{.action}.

![añadir información IP](images/pleskip2-2.png){.thumbnail}

Introduzca su dirección Additional IP como `xxx.xxx.xxx.xxx/32` en el campo "IP address and subnet mask" y haga clic en `OK`{.action}.

![añadir información IP](images/pleskip3-3.png){.thumbnail}

#### Paso 3: comprobar la configuración IP actual

En la sección "Direcciones IP", compruebe que la dirección Additional IP se haya añadido correctamente.

![configuración IP actual](images/pleskip4-4.png){.thumbnail}

### FreeBSD

#### 1. Identificar la interfaz

Identifique el nombre de su interfaz de red principal. Para ello, puede utilizar el comando `ifconfig`.

```sh
ifconfig
```

Dicho comando le dará una respuesta como la siguiente:

```sh
ifconfig
>>> nfe0: flags=8843 metric 0 mtu 1500
>>> options=10b
>>> ether 00:24:8c:d7:ba:11
>>> inet 94.23.196.18 netmask 0xffffff00 broadcast 94.23.196.255
>>> inet 87.98.129.74 netmask 0xffffffff broadcast 87.98.129.74
>>> media: Ethernet autoselect (100baseTX )
>>> status: active
>>> lo0: flags=8049 metric 0 mtu 16384
>>> options=3
>>> inet6 fe80::1%lo0 prefixlen 64 scopeid 0x2
>>> inet6 ::1 prefixlen 128
>>> inet 127.0.0.1 netmask 0xff000000 v comsdvt#
```

En este ejemplo, el nombre de la interfaz es **nfe0**.


#### 2. Crear una copia de seguridad del archivo de configuración

En primer lugar, realice una copia de seguridad del archivo de configuración de las interfaces para poder volver atrás en cualquier momento.

```sh
cp /etc/rc.conf /etc/rc.conf.bak
```

#### 3. Editar el archivo de configuración

Edite el archivo **/etc/rc.conf**.

```sh
editor /etc/rc.conf
```

A continuación, añada al final del archivo la siguiente línea:
`ifconfig_INTERFACE_alias0="inet ADDITIONAL_IP netmask 255.255.255.255 broadcast ADDITIONAL_IP"`
(sustituyendo «INTERFACE» e «ADDITIONAL_IP» por el nombre de su interfaz, identificado en el primer paso, y su Additional IP respectivamente). Por ejemplo:


```
ifconfig_nfe0_alias0="inet 87.98.129.74 netmask 255.255.255.255 broadcast 87.98.129.74"
```

#### 4. Reiniciar la interfaz

Por último, reinicie la interfaz con el siguiente comando:

```
/etc/rc.d/netif restart && /etc/rc.d/routing restart
```

### Solaris

#### 1. Identificar la interfaz

Identifique el nombre de su interfaz de red principal. Para ello, puede utilizar el comando `ifconfig`.

```sh
ifconfig -a
```

Dicho comando le dará una respuesta como la siguiente:

```
ifconfig -a
>>> lo0: flags=2001000849 mtu 8232 index 1 inet 127.0.0.1 netmask ff000000 e1000g0: flags=1000843 mtu 1500 index 2 inet 94.23.41.167 netmask ffffff00 broadcast 94.23.41.255 ether 0:1c:c0:f2:be:42
```

En este ejemplo, el nombre de la interfaz es **e1000g0**.


#### 2. Crear una copia de seguridad del archivo de configuración

En primer lugar, realice una copia de seguridad del archivo de configuración de las interfaces para poder volver atrás en cualquier momento.

```sh
editor /etc/hostname.e1000g0:1
```

#### 3. Editar el archivo de configuración

Introduzca lo siguiente: `ADDITIONAL_IP/32 up` (sustituyendo ADDITIONAL_IP por su Additional IP). Por ejemplo:

```
188.165.171.40/32 up
```

#### 4. Reiniciar la interfaz

Por último, reinicie la interfaz con el siguiente comando:

```sh
svcadm restart svc:/network/physical:default
```


## Más información

Interactúe con nuestra comunidad de usuarios en <https://community.ovh.com/en/>.
