---
title: 'Implementar Veeam Backup & Replication'
slug: veeam/veeam-backup-replication
excerpt: 'Cómo instalar un servidor Veeam Backup & Replication con Veeam Enterprise'
section: 'Veeam Backup & Replication'
---

**Última actualización: 08/03/2022**

## Objetivo

Veeam Backup & Replication es un programa que permite gestionar la seguridad de los datos. Ofrece diversas posibilidades de backup, replicación y restauración.

**Esta guía explica cómo instalar un servidor Veeam Backup & Replication y cómo registrarlo en el servidor de licencias Veeam Enterprise de OVHcloud.**


## Requisitos

* Tener una [solución Veeam Enterprise](https://www.ovhcloud.com/es-es/storage-solutions/veeam-enterprise/){.external}.
* Disponer de un máquina Windows Server 2012 o de una versión superior.

## Procedimiento

### Instalar Veeam Backup & Replication

Descargue la solución **Veeam Backup & Replication** desde la [web de Veeam](https://www.veeam.com/downloads.html?ad=top-sub-menu){.external}. Si todavía no tiene una cuenta, deberá crear una (es gratuita).

El archivo es una imagen de disco en formato ISO. Después de transferirlo a su servidor, seleccione el lector de CD de la máquina y elija la imagen.

En la máquina, ejecute el instalador. Haga clic en `Install`{.action} para el componente **Veeam Backup & Replication**.

![Add OVH Storage](images/veeamBandR_inst_01.png){.thumbnail}

Lea el contrato de licencia, acéptelo y haga clic en `Next`{.action}.

![Add OVH Storage](images/veeamBandR_inst_02.png){.thumbnail}

Omita la introducción del archivo de licencia haciendo clic en `Next`{.action}.

![Add OVH Storage](images/veeamBandR_inst_03.png){.thumbnail}

En la selección de los componentes que se van a instalar, no es necesario cambiar nada, aunque, según sus necesidades, puede cambiar la ruta de destino de la instalación. Acepte haciendo clic en `Next`{.action}.

![Add OVH Storage](images/veeamBandR_inst_04.png){.thumbnail}

El instalador realizará un control de los requerimientos. Si parte de una instalación bruta de Windows, faltarán algunos componentes, pero no se preocupe: el instalador los descargará e instalará automáticamente. Acepte haciendo clic en `Next`{.action}.

![Add OVH Storage](images/veeamBandR_inst_05.png){.thumbnail}

Espere mientras se instalan los requerimientos.

![Add OVH Storage](images/veeamBandR_inst_06.png){.thumbnail}

A continuación, acepte la instalación de **Veeam Backup & Replication** haciendo clic en `Next`{.action}.

![Add OVH Storage](images/veeamBandR_inst_07.png){.thumbnail}

En la etapa de personalización de la instalación, acepte la configuración por defecto haciendo clic en `Install`{.action}.

![Add OVH Storage](images/veeamBandR_inst_08.png){.thumbnail}

Espere a que finalice la instalación.

![Add OVH Storage](images/veeamBandR_inst_09.png){.thumbnail}

Cuando haya terminado, salga del instalador haciendo clic en `Finish`{.action}.

![Add OVH Storage](images/veeamBandR_inst_10.png){.thumbnail}

El sistema le redirigirá al asistente de instalación. Solo tiene que cerrar la ventana.

### Crear una cuenta de servicio Veeam Enterprise

#### Crear la cuenta de servicio

En primer lugar, es necesario generar una contraseña **compleja**. Para ello puede utilizar un generador de contraseñas.

En primer lugar, ejecute Windows Powershell como administrador.

A continuación, cree una cuenta de servicio introduciendo el siguiente comando desde un acceso de administrador:

```powershell
New-LocalUser "OVHVeeamEnterprise" -Password (ConvertTo-SecureString -AsPlainText "P@ssword01" -Force) -Description "OVH Service Account for Veeam Enterprise" -PasswordNeverExpires:$true -UserMayNotChangePassword:$true -AccountNeverExpires:$true
```

Tenga en cuenta que el nombre de la cuenta y la contraseña son un ejemplo y deben sustituirse:

* Nombre de la cuenta: OVHVeeamEnterprise
* Contraseña: P@ssword01


#### Establecer las autorizaciones de la cuenta de servicio

Abra la consola Veeam y conéctese.

![Add OVH Storage](images/veeamBandR_use_12.png){.thumbnail}

Compruebe que se encuentre en modo **Community Edition** en la esquina inferior derecha.

![Add OVH Storage](images/Veeamcommunity.png){.thumbnail}

En el menú, haga clic en `Users and Roles`{.action}.

![Add OVH Storage](images/veeamBandR_conf_2.png){.thumbnail}

En la ventana **Security**, haga clic en `Add...`{.action}.

![Add OVH Storage](images/veeamBandR_conf_3.png){.thumbnail}

En la ventana **Add User**, introduzca la cuenta de servicio anteriormente creada. Seleccione el rol **Veeam Backup Administrator** y acepte con `OK`{.action}.

![Add OVH Storage](images/veeamBandR_conf_4.png){.thumbnail}

Al volver a la ventana **Security**, compruebe que aparezca la cuenta.

![Add OVH Storage](images/veeamBandR_conf_5.png){.thumbnail}

#### Autorizaciones de ejecución y activación

El usuario de OVHVeeamEnterprise solo está disponible localmente. Para activar la conexión a distancia, es necesario añadir permisos en la interfaz gráfica de usuario Windows.

Desde la interfaz gráfica de usuario:

1. En la barra de búsqueda de Windows, introduzca `Component Services`{.action} e inicie el servicio.
2. En el menú de la izquierda, haga clic en `Component Services`{.action} y seleccione `Computers`{.action}. A continuación, haga clic en `My Computer`{.action}.
3. A la derecha, en la pestaña `Actions`{.action}, haga clic en `More Actions`{.action} y seleccione `Properties`{.action}.
4. Vaya a `COM Security`{.action} y, bajo la segunda opción `Launch and Activation Permissions`{.action}, haga clic en `Edit Limits`{.action}. A continuación, haga clic en `Add...`{.action}.

![Launch and Activation Permissions](images/veeamuseradd.png){.thumbnail}

<ol start="5">
 <li>Haga clic en <code class="action">Advanced...</code> para localizar la cuenta de servicio anteriormente añadida. Haga clic en <code class="action">Find Now</code> y seleccione el usuario <code class="action">OVHVeeamEnterprise</code> en la lista de usuarios.</li>
</ol>

![Launch and Activation Permissions](images/veeamuseradd1.png){.thumbnail}

<ol start="6">
 <li>Haga clic en <code class="action">OK</code> para confirmar la selección y en <code class="action">OK</code> para validar. A continuación, active todos los permisos en el usuario <code class="action">OVHVeeamEnterprise</code>.</li>
</ol>

![Launch and Activation Permissions](images/veeamuseradd3.png){.thumbnail}


<ol start="7">
 <li>Haga clic en <code class="action">OK</code> para confirmar y <code class="action">Apply</code> para aceptar los cambios.</li>
</ol>

Ya puede acceder a su usuario de OVHVeeamEnterprise local y a distancia.

#### Registrar el servidor Veeam Backup & Replication

## Desde el área de cliente de OVHcloud

Conéctese a su [área de cliente de OVHcloud](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.es/&ovhSubsidiary=es){.external}, acceda a la sección `Hosted Private Cloud`{.action} y seleccione `Plataformas y servicios`{.action}. Seleccione el servicio **backupserverenterprise** y haga clic en `Activar la licencia`{.action} en la sección `Atajos`.

![](images/veeam001.png){.thumbnail}

En la nueva ventana, introduzca la siguiente información:

* la dirección IP pública a través de la cual es posible comunicar con el servidor **Veeam Backup & Replication**;
* el puerto del servidor **Veeam Backup & Replication** (normalmente **9392/TCP**);
* el usuario de la cuenta de servicio anteriormente creada;
* la contraseña de la cuenta de servicio.

Valide haciendo clic en `OK`{.action}.

![](images/veeam03.png){.thumbnail}

Una vez completada la activación, podrá encontrar la información principal en la página del servicio.

![](images/veeam02.png){.thumbnail}

#### **Utilizando la API de OVHcloud**

En primer lugar, obtenga el serviceName:

> [!api]
>
> @api {GET} /veeam/veeamEnterprise
>

A continuación, regístrelo:

> [!api]
>
> @api {POST} /veeam/veeamEnterprise/{serviceName}/register
>

Necesitará la siguiente información:

* la dirección IP pública a través de la cual es posible comunicar con el servidor **Veeam Backup & Replication**;
* el puerto del servidor **Veeam Backup & Replication** (normalmente **9392/TCP**);
* el usuario de la cuenta de servicio anteriormente creada;
* la contraseña de la cuenta de servicio.

Puede obtener la dirección IP pública que utiliza Veeam Enterprise para comunicar con el servidor **Veeam Backup & Replication** de la siguiente forma:

> [!api]
>
> @api {GET} /veeam/veeamEnterprise/{serviceName}
>

#### Comprobar el registro

Abra la consola Veeam y conéctese.

![Add OVH Storage](images/veeamBandR_use_12.png){.thumbnail}

En el menú, haga clic en `License`{.action}.

![Add OVH Storage](images/veeamBandR_lic_1.png){.thumbnail}

Compruebe que la información mostrada corresponde a su licencia de OVHcloud.

![Add OVH Storage](images/veeamBandR_lic_2.png){.thumbnail}

## Más información

Interactúe con nuestra comunidad de usuarios en <https://community.ovh.com/en/>.