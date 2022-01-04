---
title: Instalar Dispatcher
seo-title: Installing AEM Dispatcher
description: Aprenda a instalar el módulo Dispatcher en Microsoft Internet Information Server, en el servidor web Apache y en Sun Java Web Server-iPlanet.
seo-description: Learn how to install the AEM Dispatcher module on Microsoft Internet Information Server, Apache Web Server and Sun Java Web Server-iPlanet.
uuid: 2384b907-1042-4707-b02f-fba2125618cf
contentOwner: User
converted: true
topic-tags: dispatcher
content-type: reference
discoiquuid: f00ad751-6b95-4365-8500-e1e0108d9536
exl-id: 9375d1c0-8d9e-46cb-9810-fa4162a8c1ba
source-git-commit: bd03499fae4096fe5642735eb466276f1a179dec
workflow-type: ht
source-wordcount: '3693'
ht-degree: 100%

---

# Instalar Dispatcher {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

Utilice la página [Notas de la versión de Dispatcher](release-notes.md) para obtener el archivo de instalación más reciente de Dispatcher para su sistema operativo y servidor web. Los números de la versión de Dispatcher son independientes de los números de versión de Adobe Experience Manager y son compatibles con las versiones de Adobe Experience Manager 6.x, 5.x y Adobe CQ 5.x.

>[!NOTE]
>
>Tenga en cuenta que Adobe Experience Manager 6.5 requiere la versión 4.3.2 o superior de Dispatcher. Dicho esto, las versiones de Dispatcher son independientes de AEM, por ejemplo, la versión 4.3.2 de Dispatcher también es compatible con Adobe Experience Manager 6.4.

Se utiliza la siguiente convención de nomenclatura de archivos:

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

Por ejemplo, el archivo `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` contiene la versión 4.3.1 de Dispatcher para un servidor web Apache 2.4 que se ejecuta en Linux i686 y está empaquetado con el formato **tar**.

La siguiente tabla muestra el identificador del servidor web que se utiliza en los nombres de archivo para cada servidor web:

| Servidor web | Kit de instalación |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt;otros parámetros> |
| Microsoft Internet Information Server 7.5, 8, 8.5 | dispatcher-**iis**-&lt;otros parámetros> |
| Sun Java Web Server iPlanet | dispatcher-**ns**-&lt;otros parámetros> |

>[!CAUTION]
>
>Debe instalar la versión más reciente de Dispatcher que esté disponible para su plataforma. Anualmente, debe actualizar la instancia de Dispatcher para utilizar la versión más reciente y aprovechar las mejoras del producto.

>[!NOTE]
>
>Los clientes que actualicen de forma específica de la versión 4.3.3 a la 4.3.4 notarán un comportamiento diferente en la forma en que se establecen los encabezados de caché para el contenido que no se puede almacenar en caché. Para obtener más información acerca de este cambio, consulte la página [Notas de la versión](/help/using/release-notes.md#nov).

Cada archivo contiene los siguientes archivos:

* los módulos de Dispatcher
* un archivo de configuración de ejemplo
* el archivo LÉAME que contiene instrucciones de instalación e información de última hora
* el archivo CAMBIOS que enumera los problemas corregidos en las versiones actuales y pasadas

>[!NOTE]
>
>Compruebe el archivo LÉAME para ver si hay cambios de última hora o notas específicas de la plataforma antes de iniciar la instalación.

<!-- 

Comment Type: draft

<h3>Supported Web Servers</h3>

 -->

<!-- 

Comment Type: draft

<p>The following web servers are supported for use with Dispatcher version 4.1.12:</p>

 -->

<!-- 

Comment Type: draft

<p>The following sections detail the specific web server installation procedures.</p>

 -->

## Microsoft Internet Information Server {#microsoft-internet-information-server}

Para obtener información sobre cómo instalar este servidor web, consulte los siguientes recursos:

* Documentación propia de Microsoft sobre Internet Information Server
* [&quot;El sitio oficial de Microsoft IIS&quot;](https://www.iis.net/)

### Componentes IIS necesarios {#required-iis-components}

Las versiones 8.5 y 10 de IIS requieren que estén instalados los siguientes componentes de IIS:

* Extensiones ISAPI

Además, debe agregar el rol Servidor Web (IIS). Utilice el Administrador del servidor para añadir la función y los componentes.

## Microsoft IIS: instalación del módulo de Dispatcher {#microsoft-iis-installing-the-dispatcher-module}

El archivo requerido para Microsoft Internet Information System es:

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

El archivo ZIP contiene los siguientes archivos:

| Archivo | Descripción |
|--- |--- |
| `disp_iis.dll` | El archivo de la biblioteca de vínculos dinámicos de Dispatcher. |
| `disp_iis.ini` | Archivo de configuración para IIS. Este ejemplo se puede actualizar según sus necesidades. **Nota**: El archivo ini debe tener el mismo nombre-raíz que el archivo dll. |
| `dispatcher.any` | Archivo de configuración de ejemplo para Dispatcher. |
| `author_dispatcher.any` | Archivo de configuración de ejemplo para Dispatcher que trabaja con la instancia de autor. |
| LÉAME | El archivo Léame contiene instrucciones de instalación e información de última hora. **Nota**: Compruebe este archivo antes de iniciar la instalación. |
| CAMBIOS | El archivo de cambios enumera los problemas corregidos en las versiones actuales y anteriores. |

Utilice el siguiente procedimiento para copiar los archivos de Dispatcher en la ubicación correcta.

1. Utilice el Explorador de Windows para crear el directorio `<IIS_INSTALLDIR>/Scripts`, por ejemplo, `C:\inetpub\Scripts`.

1. Extraiga los siguientes archivos del paquete Dispatcher en este directorio de Scripts:

   * `disp_iis.dll`
   * `disp_iis.ini`
   * Uno de los siguientes archivos depende de si Dispatcher está trabajando con una instancia de autor o de publicación de AEM:
      * Instancia de autor: `author_dispatcher.any`
      * Instancia de publicación: `dispatcher.any`

## Microsoft IIS: Configurar el archivo INI de Dispatcher {#microsoft-iis-configure-the-dispatcher-ini-file}

Edite el archivo `disp_iis.ini` para configurar la instalación de Dispatcher. El formato básico del archivo `.ini` es el siguiente:

```xml
[main]
configpath=<path to dispatcher.any>
loglevel=1|2|3
servervariables=0|1
replaceauthorization=0|1
```

En la siguiente tabla se describe cada propiedad.

| Parámetro | Descripción |
|--- |--- |
| configpath | La ubicación de `dispatcher.any` dentro del sistema de archivos local (ruta absoluta). |
| logfile | La ubicación del archivo `dispatcher.log`. Si no se ha configurado, los mensajes de registro se dirigen al registro de eventos de Windows. |
| loglevel | Define el nivel de registro utilizado para generar mensajes en el registro de eventos. Se pueden especificar los siguientes valores: Nivel de registro para el archivo de registro: <br/>0 - solo mensajes de error. <br/>1 - errores y advertencias. <br/>2 - errores, advertencias y mensajes informativos <br/>3 - errores, advertencias, mensajes informativos y de depuración. <br/>**Nota**: Se recomienda establecer el nivel de registro en 3 durante la instalación y la prueba, luego en 0 cuando se ejecute en un entorno de producción. |
| replaceauthorization | Especifica cómo se administran los encabezados de autorización en la solicitud HTTP. Los siguientes valores son válidos:<br/>0 - Los encabezados de autorización no se modifican. <br/>1 - Reemplaza cualquier encabezado denominado &quot;Autorización&quot; que no sea &quot;Básico&quot; por su `Basic <IIS:LOGON\_USER>` equivalente.<br/> |
| servervariables | Define cómo se procesan las variables del servidor.<br/>0 - Las variables del servidor IIS no se envían ni a Dispatcher ni a AEM. <br/>1 - todas las variables del servidor IIS (como `LOGON\_USER, QUERY\_STRING, ...`) se envían a Dispatcher, junto con los encabezados de solicitud (y también a la instancia de AEM si no se almacenan en caché).  <br/>Las variables de servidor incluyen `AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` y muchas otras. Consulte la documentación de IIS para obtener la lista completa de variables, con detalles. |
| enable_chunked_transfer | Define si se desea habilitar (1) o deshabilitar (0) la transferencia interrumpida para la respuesta del cliente. El valor predeterminado es 0. |

Un ejemplo de configuración:

```xml
[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0
```

### Configurar Microsoft IIS {#configuring-microsoft-iis}

Configure IIS para integrar el módulo ISAPI de Dispatcher. En IIS se utiliza la asignación de aplicación comodín.

### Configurar el acceso anónimo: IIS 8.5 y 10 {#configuring-anonymous-access-iis-and}

El agente de replicación de vaciado predeterminado en la instancia de autor está configurado para que no envíe credenciales de seguridad con solicitudes de vaciado. Por lo tanto, el sitio web que esté utilizando la caché de Dispatcher debe permitir el acceso anónimo.

Si su sitio web utiliza un método de autenticación, el agente de replicación de vaciado debe configurarse como corresponde.

1. Abra el Administrador de IIS y seleccione el sitio web que está utilizando como caché de Dispatcher.
1. Con el modo Vista de características, en la sección IIS haga doble clic en Autenticación.
1. Si la autenticación anónima no está activada, seleccione Autenticación anónima y, en el área Acciones, haga clic en Habilitar.

### Integrar el módulo ISAPI de Dispatcher - IIS 8.5 y 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Utilice el siguiente procedimiento para agregar el módulo ISAPI de Dispatcher a IIS.

1. Abra el Administrador de IIS.
1. Seleccione el sitio web que está utilizando como caché de Dispatcher.
1. Con el modo Vista de características, en la sección IIS haga doble clic en Asignaciones de controladores.
1. En el panel Acciones de la página Asignaciones de controladores, haga clic en Agregar mapa de script comodín, agregue los siguientes valores de propiedad y, a continuación, haga clic en Aceptar:

   * Ruta de solicitud: *
   * Ejecutable: la ruta absoluta del archivo disp_iis.dll, por ejemplo `C:\inetpub\Scripts\disp_iis.dll`.
   * Nombre: un nombre descriptivo para la asignación de controladores, por ejemplo `Dispatcher`.

1. Para agregar la biblioteca disp_iis.dll a la lista Restricciones ISAPI y CGI, haga clic en Sí en el cuadro de diálogo que aparece.

   Se ha completado la configuración de IIS 7.0 y 7.5. Continúe con los pasos restantes si está configurando IIS 8.0.

1. (IIS 8.0) En la lista de asignaciones de controladores, seleccione la que acaba de crear y, en el área Acciones, haga clic en Editar.
1. (IIS 8.0) En el cuadro de diálogo Editar mapa de script, haga clic en el botón Solicitar restricciones.
1. (IIS 8.0) Para asegurarse de que el controlador se utiliza para archivos y carpetas que aún no se han almacenado en caché, anule la selección de Invocar controlador solo si la solicitud está asignada a y, a continuación, haga clic en Aceptar.
1. (IIS 8.0) En el cuadro de diálogo Editar mapa de script, haga clic en Aceptar.

### Configurar el acceso a la caché: IIS 8.5 y 10 {#configuring-access-to-the-cache-iis-and}

Proporcione acceso de escritura al usuario predeterminado del grupo de aplicaciones para la carpeta que se está utilizando como caché de Dispatcher.

1. Haga clic con el botón derecho en la carpeta raíz del sitio web que esté utilizando como caché de Dispatcher y haga clic en Propiedades, como `C:\inetpub\wwwroot`.
1. En la pestaña Seguridad, haga clic en Editar y, a continuación, en el cuadro de diálogo Permisos, haga clic en Agregar. Se abrirá un cuadro de diálogo para seleccionar cuentas de usuario. Haga clic en el botón Ubicaciones, seleccione el nombre del equipo y, a continuación, haga clic en Aceptar.

   Mantenga este cuadro de diálogo abierto mientras completa el siguiente paso.

1. En el Administrador de IIS, seleccione el sitio de IIS que está utilizando para la caché de Dispatcher y, en el lado derecho de la ventana, haga clic en Configuración avanzada.
1. Seleccione el valor de la propiedad Grupo de aplicaciones y cópielo en el portapapeles.
1. Vuelva al cuadro de diálogo abierto. Escriba los nombres de los objetos que desea seleccionar, escriba `IIS AppPool\` y pegue el contenido del portapapeles. El valor debe ser similar al siguiente ejemplo:

   `IIS AppPool\DefaultAppPool`

1. Haga clic en el botón Comprobar nombres. Cuando Windows resuelva la cuenta de usuario, haga clic en Aceptar.
1. En el cuadro de diálogo Permisos de la carpeta de Dispatcher, seleccione la cuenta que acaba de agregar, habilite todos los permisos de la cuenta **excepto Control total** y haga clic en Aceptar. Haga clic en Aceptar para cerrar el cuadro de diálogo de la carpeta Propiedades.

### Registrar el tipo MIME de JSON: IIS 8.5 y 10 {#registering-the-json-mime-type-iis-and}

Utilice el siguiente procedimiento para registrar el tipo MIME JSON, cuando quiera que Dispatcher permita llamadas JSON.

1. En el Administrador de IIS, seleccione el sitio web y, mediante la Vista de características, haga doble clic en Tipos MIME.
1. Si la extensión JSON no está en la lista, en el panel Acciones haga clic en Agregar, introduzca los siguientes valores de propiedad y, a continuación, haga clic en Aceptar:

   * Extensión de nombre de archivo: `.json`
   * Tipo MIME: `application/json`

### Eliminar el segmento oculto Bin - IIS 8.5 y 10 {#removing-the-bin-hidden-segment-iis-and}

Utilice el siguiente procedimiento para eliminar el segmento oculto `bin`. Los sitios web que no son nuevos pueden contener este segmento oculto.

1. En el Administrador de IIS, seleccione el sitio web y, mediante la Vista de características, haga doble clic en Solicitar filtrado.
1. Seleccione el segmento `bin`, haga clic en Quitar y, en el cuadro de diálogo de confirmación, haga clic en Sí.

### Registrar mensajes IIS en un archivo: IIS 8.5 y 10 {#logging-iis-messages-to-a-file-iis-and}

Utilice el siguiente procedimiento para escribir los mensajes de registro de Dispatcher en un archivo de registro en lugar de en el registro de eventos de Windows. Debe configurar Dispatcher para que utilice el archivo de registro y proporcionar a IIS acceso de escritura al archivo.

1. Utilice el Explorador de Windows para crear una carpeta denominada `dispatcher` debajo de la carpeta de registros de la instalación de IIS. La ruta de esta carpeta para una instalación tradicional es `C:\inetpub\logs\dispatcher`.

1. Haga clic con el botón derecho en la carpeta de Dispatcher y haga clic en Propiedades.
1. En la pestaña Seguridad, haga clic en Editar y, a continuación, en el cuadro de diálogo Permisos, haga clic en Agregar. Se abrirá un cuadro de diálogo para seleccionar cuentas de usuario. Haga clic en el botón Ubicaciones, seleccione el nombre del equipo y, a continuación, haga clic en Aceptar.

   Mantenga este cuadro de diálogo abierto mientras completa el siguiente paso.

1. En el Administrador de IIS, seleccione el sitio de IIS que está utilizando para la caché de Dispatcher y, en el lado derecho de la ventana, haga clic en Configuración avanzada.
1. Seleccione el valor de la propiedad Grupo de aplicaciones y cópielo en el portapapeles.
1. Vuelva al cuadro de diálogo abierto. Escriba los nombres de los objetos que desea seleccionar, escriba `IIS AppPool\` y pegue el contenido del portapapeles. El valor debe ser similar al siguiente ejemplo:

   `IIS AppPool\DefaultAppPool`

1. Haga clic en el botón Comprobar nombres. Cuando Windows resuelva la cuenta de usuario, haga clic en Aceptar.
1. En el cuadro de diálogo Permisos de la carpeta de Dispatcher, seleccione la cuenta que acaba de agregar, habilite todos los permisos de la cuenta **excepto Control total** y haga clic en Aceptar. Haga clic en Aceptar para cerrar el cuadro de diálogo de la carpeta Propiedades.
1. Utilice un editor de texto para abrir el archivo `disp_iis.ini`.
1. Agregue una línea de texto similar al siguiente ejemplo para configurar la ubicación del archivo de registro y, a continuación, guarde el archivo:

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### Siguientes pasos {#next-steps}

Antes de empezar a utilizar Dispatcher, debe saber:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configurar AEM](page-invalidate.md) para que funcione con Dispatcher.

## Servidor web Apache {#apache-web-server}

>[!CAUTION]
>
>Aquí se tratan las instrucciones de instalación en **Windows** y **Unix**. Tenga cuidado al realizar los pasos.

### Instalar el servidor web Apache {#installing-apache-web-server}

Para obtener información sobre cómo instalar un servidor web Apache, lea el manual de instalación: [online](https://httpd.apache.org/) o proporcionado.

>[!CAUTION]
>
>Si está creando un binario de Apache compilando los archivos de origen, asegúrese de activar el **soporte de módulos dinámicos**. Esto se puede hacer utilizando cualquiera de las opciones **—enable-shared**. Como mínimo, incluya el módulo `mod_so`.
>
>Puede encontrar más información en el manual de instalación del servidor web Apache.

Consulte también los [Consejos de seguridad](https://httpd.apache.org/docs/2.4/misc/security_tips.html) e [Informes de seguridad](https://httpd.apache.org/security_report.html) del servidor HTTP Apache.

### Servidor web Apache: Añadir el módulo de Dispatcher {#apache-web-server-add-the-dispatcher-module}

Dispatcher se presenta como:

* **Windows**: una biblioteca de vínculos dinámicos (DLL)
* **Unix**: un objeto compartido dinámico (DSO)

Los archivos de instalación contienen los siguientes archivos, en función de si ha seleccionado Windows o Unix:

| Archivo | Descripción |
|--- |--- |
| disp_apache&lt;x.y>.dll | Windows: archivo de biblioteca de vínculos dinámicos de Dispatcher. |
| dispatcher-apache&lt;x.y>-&lt;rel-nr>.so | Unix: archivo de biblioteca de objetos compartidos de Dispatcher. |
| mod_dispatcher.so | Unix: vínculo de ejemplo. |
| http.conf.disp&lt;x> | Archivo de configuración de ejemplo para el servidor Apache. |
| dispatcher.any | Archivo de configuración de ejemplo para Dispatcher. |
| LÉAME | El archivo Léame contiene instrucciones de instalación e información de última hora. **Nota**: Compruebe este archivo antes de iniciar la instalación. |
| CAMBIOS | Archivo de cambios que enumera los problemas corregidos en las versiones actuales y anteriores. |

Siga los siguientes pasos para agregar Dispatcher a su servidor web Apache:

1. Coloque el archivo Dispatcher en el directorio de módulos Apache apropiado:

   * **Windows**: Lugar `disp_apache<x.y>.dll` `<APACHE_ROOT>/modules`
   * **Unix**: Localice el directorio `<APACHE_ROOT>/libexec` o `<APACHE_ROOT>/modules`según su instalación.\
      Copie `dispatcher-apache<options>.so` en este directorio.\
      Para simplificar el mantenimiento a largo plazo, también puede crear un vínculo simbólico denominado `mod_dispatcher.so` a Dispatcher:\
      `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Copie el archivo dispatcher.any en el directorio `<APACHE_ROOT>/conf`.

   **Nota:** Puede colocar este archivo en una ubicación diferente, siempre y cuando la propiedad DispatcherLog del módulo de Dispatcher esté configurada como corresponde. (Consulte las Entradas de configuración específicas de Dispatcher a continuación).

### Servidor web Apache: Configurar propiedades de SELinux {#apache-web-server-configure-selinux-properties}

Si está ejecutando Dispatcher en RedHat Linux Kernel 2.6 con SELinux habilitado, puede encontrarse con mensajes de error como este en el archivo de registro de Dispatcher.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

Esto probablemente se deba a que la seguridad SELinux está habilitada. A continuación, debe realizar las siguientes tareas:

* Configure el contexto SELinux del archivo del módulo de Dispatcher.
* Habilite los scripts y módulos HTTPD para realizar conexiones de red.
* Configure el contexto SELinux de docroot, donde se almacenan los archivos en caché.

Introduzca los siguientes comandos en una ventana de terminal, reemplazando `[path to the dispatcher.so file]` por la ruta al módulo de Dispatcher que instaló en el servidor web Apache, y *`path to the docroot`* por la ruta donde se encuentra el docroot (por ejemplo, `/opt/cq/cache`):

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_content_t "[path to the docroot](/.*)?"
```

### Servidor web Apache: Configurar el servidor web Apache para Dispatcher {#apache-web-server-configure-apache-web-server-for-dispatcher}

El servidor web Apache debe configurarse mediante `httpd.conf`. En el kit de instalación de Dispatcher encontrará un archivo de configuración de ejemplo llamado `httpd.conf.disp<x>`.

Estos pasos son obligatorios:

1. Vaya a `<APACHE_ROOT>/conf`.
1. Abra `httpd.conf` para editarlo.
1. Debe añadir las siguientes entradas de configuración, en el orden indicado:

   * **LoadModule** para cargar el módulo al iniciarlo.
   * Entradas de configuración específicas de Dispatcher, incluidas **DispatcherConfig, DispatcherLog** y **DispatcherLogLevel**.
   * **SetHandler** para activar Dispatcher. **LoadModule**.
   * **ModMimeUsePathInfo** para configurar el comportamiento de **mod_mime**.

1. (Opcional) Se recomienda cambiar el propietario del directorio htdocs:

   * El servidor Apache se inicia como raíz, aunque los procesos secundarios lo harán como daemon (por motivos de seguridad). DocumentRoot (`<APACHE_ROOT>/htdocs`) debe pertenecer al usuario daemon:

      ```xml
      cd <APACHE_ROOT>  
      chown -R daemon:daemon htdocs
      ```

**LoadModule**

En la siguiente tabla se enumeran los ejemplos que se pueden utilizar; las entradas exactas están en base a su servidor web Apache específico:

|  |  |
|--- |--- |
| Windows | `... LoadModule dispatcher_module modules\disp_apache.dll ...` |
| Unix (suponiendo vínculo simbólico) | `... LoadModule dispatcher_module libexec/mod_dispatcher.so ...` |

>[!NOTE]
>
>El primer parámetro de cada enunciado debe escribirse exactamente como en los ejemplos anteriores.
>
>Consulte los archivos de configuración de ejemplo proporcionados y la documentación del servidor web Apache para obtener información detallada sobre este comando.

**Entradas de configuración específicas de Dispatcher**

Las entradas de configuración específicas de Dispatcher se colocan después de la entrada LoadModule. En la siguiente tabla se muestra un ejemplo de configuración aplicable tanto a Unix como a Windows:

**Windows y Unix**

```
...
<fModule disp_apache2.c>
DispatcherConfig conf/dispatcher.any
DispatcherLog logs/dispatcher.log DispatcherLogLevel 3
DispatcherNoServerHeader 0 DispatcherDeclineRoot 0
DispatcherUseProcessedURL 0
DispatcherPassError 0
DispatcherKeepAliveTimeout 60
</IfModule>
...
```

Los parámetros de configuración individuales:

| Parámetro | Descripción |
|--- |--- |
| DispatcherConfig | Ubicación y nombre del archivo de configuración de Dispatcher. <br/>Cuando esta propiedad se encuentra en la configuración del servidor principal, todos los hosts virtuales heredan el valor de la propiedad. Sin embargo, los hosts virtuales pueden incluir una propiedad DispatcherConfig para anular la configuración principal del servidor. |
| DispatcherLog | Ubicación y nombre del archivo de registro. |
| DispatcherLogLevel | Nivel de registro del archivo de registro: <br/>0 - Errores <br/>1: Advertencias <br/>2: Info <br/>3: Depuración <br/>**Nota**: Se recomienda establecer el nivel de registro en 3 durante la instalación y la prueba, luego en 0 cuando se ejecute en un entorno de producción. |
| DispatcherNoServerHeader | *Este parámetro está obsoleto y ya no tiene ningún efecto.*<br/><br/> Define el encabezado del servidor que se va a utilizar: <br/><ul><li>undefined o 0: el encabezado del servidor HTTP contiene la versión de AEM. </li><li>1 - se utiliza el encabezado del servidor Apache.</li></ul> |
| DispatcherDeclineRoot | Define si se rechazan las solicitudes en la raíz &quot;/&quot;: <br/>**0**: aceptar solicitudes a / <br/>**1** - las solicitudes a / no son administradas por Dispatcher; utilice mod_alias para la asignación correcta. |
| DispatcherUseProcessedURL | Define si se deben usar direcciones URL preprocesadas para todo el procesamiento posterior de Dispatcher: <br/>**0**: utilice la URL original que se pasó al servidor web. <br/>**1**: Dispatcher utiliza la dirección URL que ya han procesado los controladores anteriores a Dispatcher (es decir, `mod_rewrite`) en lugar de la URL original que se pasa al servidor web.  Por ejemplo, la dirección URL original o procesada coincide con los filtros de Dispatcher. La URL también se utiliza como base para la estructura de archivos de caché.   Consulte la documentación del sitio web Apache para obtener información sobre mod_rewrite; por ejemplo, Apache 2.4. Al utilizar mod_rewrite, se aconseja utilizar el indicador &#39;passthrough | PT&#39; (pasar al siguiente controlador) para forzar al motor de reescritura a establecer el campo uri de la estructura request_rec interna en el valor del campo filename. |
| DispatcherPassError | Define cómo se admiten los códigos de error para la administración de ErrorDocument: <br/>**0**: Dispatcher pone en cola todas las respuestas de error al cliente. <br/>**1**: Dispatcher no envía una respuesta de error al cliente (donde el código de estado es bueno o igual a 400), pero pasa el código de estado a Apache, que, por ejemplo, permite que una directiva ErrorDocument procese dicho código de estado. <br/>**Rango de códigos**: especifique un rango de códigos de error para los que la respuesta se pasará a Apache. Se pasan otros códigos de error al cliente. Por ejemplo, la siguiente configuración pasa al cliente las respuestas del error 412 y todos los demás errores se pasan a Apache: DispatcherPassError 400-411,413-417 |
| DispatcherKeepAliveTimeout | Especifica el tiempo de espera de la conexión persistente, en segundos. A partir de la versión 4.2.0 de Dispatcher, el valor predeterminado de la conexión persistente es 60. El valor 0 deshabilitará la conexión persistente. |
| DispatcherNoCanonURL | Si se establece este parámetro en Activado, se pasará la URL sin procesar al servidor en lugar de la canonicalizada y se anulará la configuración de DispatcherUseProcessedURL. El valor por defecto es 0. <br/>**Nota**: Las reglas de filtro de la configuración de Dispatcher siempre se evaluarán con la URL saneada, no con la URL sin procesar. |




>[!NOTE]
>
>Las entradas de ruta son relativas al directorio raíz del servidor web Apache.

>[!NOTE]
>
>La configuración predeterminada para el encabezado del servidor es:
>
>`ServerTokens Full`
>
>`DispatcherNoServerHeader 0`
>
>La cual muestra la versión de AEM (con fines estadísticos). Si desea deshabilitar que esta información esté disponible en el encabezado, puede establecer:
>
>`ServerTokens Prod`
>
>Consulte la [Documentación de Apache sobre la Directiva de ServerTokens (por ejemplo, para Apache 2.4)](https://httpd.apache.org/docs/2.4/mod/core.html) para obtener más información.

**SetHandler**

Después de estas entradas, debe agregar un enunciado **SetHandler** al contexto de la configuración (`<Directory>`, `<Location>`) para que Dispatcher administre las solicitudes entrantes. El siguiente ejemplo configura Dispatcher para que administre las solicitudes del sitio web completo:

**Windows y Unix**

```
...  
<Directory />  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

El siguiente ejemplo configura Dispatcher para que administre las solicitudes de un dominio virtual:

**Windows**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot _\[cache-path\]_\\docs  
<Directory _\[cache-path\]_\\docs>  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

**Unix**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot /usr/apachecache/docs  
<Directory /usr/apachecache/docs>  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

>[!NOTE]
>
>El parámetro del enunciado **SetHandler** debe escribirse *exactamente como en los ejemplos anteriores*, ya que es el nombre del controlador definido en el módulo.
>
>Consulte los archivos de configuración de ejemplo proporcionados y la documentación del servidor web Apache para obtener información detallada sobre este comando.

**ModMimeUsePathInfo**

Después del enunciado **SetHandler** también debe agregar la definición **ModMimeUsePathInfo**.

>[!NOTE]
>
>El parámetro `ModMimeUsePathInfo` solo debe usarse y configurarse si utiliza la versión 4.0.9 o superior de Dispatcher.
>
>(Tenga en cuenta que la versión 4.0.9 de Dispatcher se publicó en 2011. Si utiliza una versión anterior, sería oportuno actualizar a una versión reciente de Dispatcher).

El parámetro **ModMimeUsePathInfo** debe establecerse `On` para todas las configuraciones de Apache:

`ModMimeUsePathInfo On`

El módulo mod_mime (consulte, por ejemplo, [el módulo Apache mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)) se utiliza para asignar metadatos de contenido al contenido seleccionado para una respuesta HTTP. La configuración predeterminada significa que cuando mod_mime determina el tipo de contenido, solo se tendrá en cuenta la parte de la dirección URL que se asigna a un archivo o directorio.

Cuando `On`, el parámetro `ModMimeUsePathInfo` especifica que `mod_mime` es para determinar el tipo de contenido en función de la dirección URL *completa*; esto significa que los recursos virtuales tendrán información de metadatos aplicada según su extensión.

El siguiente ejemplo activa **ModMimeUsePathInfo**:

**Windows y Unix**

```
...  
<Directory />  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
ModMimeUsePathInfo On  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

### Habilitar compatibilidad con HTTPS (Unix y Linux) {#enable-support-for-https-unix-and-linux}

Dispatcher utiliza OpenSSL para implementar la comunicación segura a través de HTTP. A partir de la versión de Dispatcher **4.2.0**, se admiten OpenSSL 1.0.0 y OpenSSL 1.0.1. Dispatcher utiliza OpenSSL 1.0.0 de forma predeterminada. Para utilizar OpenSSL 1.0.1, utilice el siguiente procedimiento para crear vínculos simbólicos de modo que Dispatcher utilice las bibliotecas OpenSSL instaladas.

1. Abra un terminal y cambie el directorio actual al directorio donde están instaladas las bibliotecas OpenSSL, por ejemplo:

   ```shell
   cd /usr/lib64
   ```

1. Para crear los vínculos simbólicos, introduzca los siguientes comandos:

   ```shell
   ln -s libssl.so libssl.so.1.0.1
   ln -s libcrypto.so libcrypto.so.1.0.1
   ```

>[!NOTE]
>
>Si utiliza una versión personalizada de Apache, asegúrese de que Apache y Dispatcher estén compilados con la misma versión de [OpenSSL](https://www.openssl.org/source/).

### Siguientes pasos {#next-steps-1}

Antes de empezar a utilizar Dispatcher, ahora debe:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configurar AEM](page-invalidate.md) para que funcione con Dispatcher.

## Sun Java System Web Server / iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
>
>Aquí se tratan las instrucciones para los entornos Windows y Unix.
>
>Tenga cuidado al seleccionar qué ejecutar.

### Sun Java System Web Server / iPlanet - Instalar el servidor web {#sun-java-system-web-server-iplanet-installing-your-web-server}

Para obtener información completa sobre cómo instalar estos servidores web, consulte su documentación correspondiente:

* Sun Java System Web Server
* Servidor web iPlanet

### Sun Java System Web Server / iPlanet - Añadir el módulo Dispatcher {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

Dispatcher se presenta como:

* **Windows**: una biblioteca de vínculos dinámicos (DLL)
* **Unix**: un objeto compartido dinámico (DSO)

Los archivos de instalación contienen los siguientes archivos, en función de si ha seleccionado Windows o Unix:

| Archivo | Descripción |
|---|---|
| `disp_ns.dll` | Windows: archivo de biblioteca de vínculos dinámicos de Dispatcher. |
| `dispatcher.so` | Unix: archivo de biblioteca de objetos compartidos de Dispatcher. |
| `dispatcher.so` | Unix: vínculo de ejemplo. |
| `obj.conf.disp` | Archivo de configuración de ejemplo para el servidor web iPlanet/Sun Java System. |
| `dispatcher.any` | Archivo de configuración de ejemplo para Dispatcher. |
| LÉAME | El archivo Léame contiene instrucciones de instalación e información de última hora. Nota: Compruebe este archivo antes de iniciar la instalación. |
| CAMBIOS | Archivo de cambios que enumera los problemas corregidos en las versiones actuales y anteriores. |

Siga los siguientes pasos para agregar Dispatcher a su servidor web:

1. Coloque el archivo Dispatcher en el directorio `plugin` del servidor web:

### Sun Java System Web Server / iPlanet: Configurar para Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

Es necesario configurar el servidor web mediante `obj.conf`. En el kit de instalación de Dispatcher encontrará un archivo de configuración de ejemplo llamado `obj.conf.disp`.

1. Vaya a `<WEBSERVER_ROOT>/config`.
1. Abra `obj.conf` para editarlo.
1. Copie la línea de inicio:\
   `Service fn="dispService"`\
   de `obj.conf.disp` a la sección de inicialización de `obj.conf`.

1. Guarde los cambios.
1. Abra `magnus.conf` para editarlo.
1. Copie las dos líneas de inicio:\
   `Init funcs="dispService, dispInit"`\
   y\
   `Init fn="dispInit"`\
   de `obj.conf.disp` a la sección de inicialización de `magnus.conf`.

1. Guarde los cambios.

>[!NOTE]
>
>Las siguientes configuraciones deben estar todas en una línea y los valores `$(SERVER_ROOT)` y `$(PRODUCT_SUBDIR)` deben reemplazarse por los valores respectivos.

**Init**

En la siguiente tabla se enumeran los ejemplos que se pueden utilizar; las entradas exactas están según el servidor web específico:

**Windows y Unix**

```
...  
Init funcs="dispService,dispInit" fn="load-modules" shlib="$(SERVER\_ROOT)/plugins/dispatcher.so"  
Init fn="dispInit" config="$(PRODUCT\_SUBDIR)/dispatcher.any" loglevel="1" logfile="$(PRODUCT\_SUBDIR)/logs/dispatcher.log"  
keepalivetimeout="60"  
...
```

dónde:

| Parámetro | Descripción |
|--- |--- |
| Configuración | Ubicación y nombre del archivo de configuración `dispatcher.any.` |
| logfile | Ubicación y nombre del archivo de registro. |
| loglevel | Nivel de registro para al escribir mensajes en el archivo de registro: <br/>**0** Errores <br/>**1** Advertencias <br/>**2** Info <br/>**3** Depuración <br/>**Nota:** Se recomienda establecer el nivel de registro en 3 durante la instalación y prueba, y en 0 cuando se ejecute en un entorno de producción. |
| keepalivetimeout | Especifica el tiempo de espera de la conexión persistente, en segundos. A partir de la versión 4.2.0 de Dispatcher, el valor predeterminado de la conexión persistente es 60. El valor 0 deshabilitará la conexión persistente. |

Según sus necesidades, puede definir Dispatcher como un servicio para sus objetos. Para configurar Dispatcher para todo el sitio web, modifique el objeto predeterminado:


**Windows**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)\\dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*\\\*"  
...
```

**Unix**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)/dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*/\*"  
...
```

### Siguientes pasos {#next-steps-2}

Antes de empezar a utilizar Dispatcher, ahora debe:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configurar AEM](page-invalidate.md) para que funcione con Dispatcher.
