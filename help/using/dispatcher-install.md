---
title: Instalación de Dispatcher
seo-title: Instalación de AEM Dispatcher
description: Obtenga información sobre cómo instalar el módulo Dispatcher en Microsoft Internet Information Server, Apache Web Server y Sun Java Web Server-iPlanet.
seo-description: Obtenga información sobre cómo instalar el módulo AEM Dispatcher en Microsoft Internet Information Server, Apache Web Server y Sun Java Web Server-iPlanet.
uuid: 2384b907-1042-4707-b02f-fba2125618cf
contentOwner: Usuario
converted: verdadero
topic-tags: dispatcher
content-type: referencia
discoiquuid: f00ad751-6b95-4365-8500-e1e0108d9536
translation-type: tm+mt
source-git-commit: eed7c3f77ec64f2e7c5cfff070ef96108886a059

---


# Instalación de Dispatcher {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher insertado en la documentación de una versión anterior de AEM.

Utilice la página [Notas](release-notes.md) de revisión de Dispatcher para obtener el archivo de instalación más reciente de Dispatcher para el sistema operativo y el servidor web. Los números de versión de Dispatcher son independientes de los números de versión de Adobe Experience Manager y son compatibles con las versiones de Adobe Experience Manager 6.x, 5.x y Adobe CQ 5.x.

Se utiliza la siguiente convención de nomenclatura de archivos:

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

Por ejemplo, el `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` archivo contiene Dispatcher versión 4.3.1 para un servidor web Apache 2.4 que se ejecuta en Linux i686 y se empaqueta con el formato **tar** .

En la tabla siguiente se muestra el identificador de servidor Web que se utiliza en los nombres de archivo para cada servidor Web:

| Servidor web | Kit de instalación |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt;otros parámetros&gt; |
| Microsoft Internet Information Server 7.5, 8, 8.5 | dispatcher-**iis**-&lt;otros parámetros&gt; |
| Sun Java Web Server iPlanet | dispatcher-**ns**-&lt;otros parámetros&gt; |

>[!NOTE]
>
>Debe instalar la versión más reciente de Dispatcher que está disponible para su plataforma. Cada año, debe actualizar la instancia de Dispatcher para utilizar la versión más reciente y aprovechar las mejoras del producto.

Cada archivo contiene los siguientes archivos:

* los módulos Dispatcher
* un archivo de configuración de ejemplo
* el archivo README que contiene instrucciones de instalación e información de última hora
* el archivo CHANGES que enumera los problemas solucionados en las versiones actuales y anteriores

>[!NOTE]
>
>Verifique el archivo README para ver si hay cambios de última hora o notas específicas de la plataforma antes de iniciar la instalación.

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

* Documentación propia de Microsoft en Internet Information Server
* ["El sitio oficial de Microsoft IIS"](https://www.iis.net/)

### Componentes IIS necesarios {#required-iis-components}

Las versiones 8.5 y 10 de IIS requieren que estén instalados los siguientes componentes de IIS:

* Extensiones ISAPI

Además, debe agregar la función Servidor web (IIS). Utilice el Administrador del servidor para agregar la función y los componentes.

## Microsoft IIS: instalación del módulo Dispatcher {#microsoft-iis-installing-the-dispatcher-module}

El archivo requerido para Microsoft Internet Information System es:

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

El archivo ZIP contiene los siguientes archivos:

| Archivo | Descripción |
|--- |--- |
| `disp_iis.dll` | Archivo de biblioteca de vínculos dinámicos de Dispatcher. |
| `disp_iis.ini` | Archivo de configuración para IIS. Este ejemplo se puede actualizar según sus necesidades. **Nota**: El archivo ini debe tener el mismo nombre-raíz que el archivo dll. |
| `dispatcher.any` | Un archivo de configuración de ejemplo para Dispatcher. |
| `author_dispatcher.any` | Un archivo de configuración de ejemplo para Dispatcher que trabaja con la instancia de creación. |
| README | Archivo Léame que contiene instrucciones de instalación e información de última hora. **Nota**: Verifique este archivo antes de iniciar la instalación. |
| CAMBIOS | Archivo de cambios que enumera los problemas corregidos en las versiones actuales y anteriores. |

Utilice el siguiente procedimiento para copiar los archivos de Dispatcher en la ubicación correcta.

1. Utilice el Explorador de Windows para crear el `<IIS_INSTALLDIR>/Scripts` directorio, por ejemplo, `C:\inetpub\Scripts`.

1. Extraiga los siguientes archivos del paquete Dispatcher en este directorio Scripts:

   * `disp_iis.dll`
   * `disp_iis.ini`
   * Uno de los siguientes archivos depende de si Dispatcher está trabajando con una instancia de creación o publicación de AEM:
      * Instancia de autor: `author_dispatcher.any`
      * Instancia de publicación: `dispatcher.any`

## Microsoft IIS: configurar el archivo INI de Dispatcher {#microsoft-iis-configure-the-dispatcher-ini-file}

Edite el `disp_iis.ini` archivo para configurar la instalación de Dispatcher. El formato básico del `.ini` archivo es el siguiente:

```xml
[main]
configpath=<path to dispatcher.any>
loglevel=1|2|3
servervariables=0|1
replaceauthorization=0|1
```

En la tabla siguiente se describe cada propiedad.

| Parámetro | Descripción |
|--- |--- |
| configpath | Ubicación de `dispatcher.any` dentro del sistema de archivos local (ruta absoluta). |
| archivo de registro | Ubicación del `dispatcher.log` archivo. Si no se establece, los mensajes de registro se dirigen al registro de eventos de Windows. |
| loglevel | Define el nivel de registro utilizado para enviar mensajes al registro de eventos. Se pueden especificar los siguientes valores:nivel de registro para el archivo de registro: <br/>0 - solo mensajes de error. <br/>1 - errores y advertencias. <br/>2 - errores, advertencias y mensajes informativos <br/>3 - errores, advertencias, mensajes informativos y de depuración. <br/>**Nota**: Se recomienda establecer el nivel de registro en 3 durante la instalación y la prueba y, a continuación, en 0 cuando se ejecute en un entorno de producción. |
| replacepermission | Especifica cómo se administran los encabezados de autorización en la solicitud HTTP. Los siguientes valores son válidos:<br/>0 - Los encabezados de autorización no se modifican. <br/>1 - Reemplaza cualquier encabezado llamado "Autorización" que no sea "Básico" por su `Basic <IIS:LOGON\_USER>` equivalente.<br/> |
| servervariables | Define cómo se procesan las variables de servidor.<br/>0: las variables de servidor IIS no se envían ni al despachante ni a AEM. <br/>1: todas las variables de servidor IIS (como `LOGON\_USER, QUERY\_STRING, ...`) se envían al despachante, junto con los encabezados de solicitud (y también a la instancia de AEM si no se almacenan en caché).  <br/>Las variables de servidor incluyen `AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` y muchas otras. Consulte la documentación de IIS para ver la lista completa de variables, con detalles. |
| enable_chunked_transfer | Define si se debe habilitar (1) o deshabilitar (0) la transferencia fragmentada para la respuesta del cliente. El valor predeterminado es 0. |

Una configuración de ejemplo:

```xml
[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0
```

### Configuración de Microsoft IIS {#configuring-microsoft-iis}

Configure IIS para integrar el módulo ISAPI de Dispatcher. En IIS se utiliza la asignación de aplicaciones comodín.

### Configuración del acceso anónimo: IIS 8.5 y 10 {#configuring-anonymous-access-iis-and}

El agente de replicación de vaciado predeterminado de la instancia de Autor está configurado para que no envíe credenciales de seguridad con solicitudes de vaciado. Por lo tanto, el sitio web que está utilizando la caché de Dispatcher debe permitir el acceso anónimo.

Si su sitio Web utiliza un método de autenticación, el agente de replicación de vaciado debe configurarse en consecuencia.

1. Abra el Administrador de IIS y seleccione el sitio Web que está utilizando como caché de Disptcher.
1. Con el modo de vista de características, en la sección IIS, haga doble clic en Autenticación.
1. Si la autenticación anónima no está habilitada, seleccione Autenticación anónima y, en el área Acciones, haga clic en Habilitar.

### Integración del módulo ISAPI de Dispatcher - IIS 8.5 y 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Utilice el siguiente procedimiento para agregar el módulo ISAPI de Dispatcher a IIS.

1. Abra el Administrador de IIS.
1. Seleccione el sitio Web que está utilizando como caché de despachante.
1. Con el modo de vista de características, en la sección IIS, haga doble clic en Asignaciones de controladores.
1. En el panel Acciones de la página Asignaciones de controladores, haga clic en Agregar mapa de secuencias de comandos de comodines, agregue los siguientes valores de propiedad y, a continuación, haga clic en Aceptar:

   * Ruta de solicitud: *
   * Ejecutable: Ruta absoluta del archivo disp_iis.dll, por ejemplo `C:\inetpub\Scripts\disp_iis.dll`.
   * Nombre: Un nombre descriptivo para la asignación de controladores, por ejemplo `Dispatcher`.

1. En el cuadro de diálogo que aparece, para agregar la biblioteca disp_iis.dll a la lista Restricciones ISAPI y CGI, haga clic en Sí.

   Para IIS 7.0 y 7.5, la configuración se ha completado. Continúe con los pasos restantes si va a configurar IIS 8.0.

1. (IIS 8.0) En la lista de asignaciones de controlador, seleccione la que acaba de crear y, en el área Acciones, haga clic en Editar.
1. (IIS 8.0) En el cuadro de diálogo Editar mapa de secuencias de comandos, haga clic en el botón Solicitar restricciones.
1. (IIS 8.0) Para asegurarse de que el controlador se utiliza para archivos y carpetas que aún no se han almacenado en caché, anule la selección de Invocar controlador solo si la solicitud está asignada a y, a continuación, haga clic en Aceptar.
1. (IIS 8.0) En el cuadro de diálogo Editar mapa de secuencias de comandos, haga clic en Aceptar.

### Configuración del acceso a la caché: IIS 8.5 y 10 {#configuring-access-to-the-cache-iis-and}

Proporcione al usuario predeterminado del grupo de aplicaciones acceso de escritura a la carpeta que se está utilizando como caché de Dispatcher.

1. Haga clic con el botón secundario en la carpeta raíz del sitio Web que está utilizando como caché de Dispatcher y, a continuación, haga clic en Propiedades, como `C:\inetpub\wwwroot`.
1. En la ficha Seguridad, haga clic en Editar y, a continuación, en el cuadro de diálogo Permisos, haga clic en Agregar. Se abre un cuadro de diálogo para seleccionar cuentas de usuario. Haga clic en el botón Ubicaciones, seleccione el nombre del equipo y, a continuación, haga clic en Aceptar.

   Mantenga este cuadro de diálogo abierto mientras completa el paso siguiente.

1. en el Administrador de IIS, seleccione el sitio de IIS que está utilizando para la caché de Dispatcher y, en la parte derecha de la ventana, haga clic en Configuración avanzada.
1. Seleccione el valor de la propiedad Grupo de aplicaciones y cópielo en el portapapeles.
1. Vuelva al cuadro de diálogo abierto. En el cuadro Escriba los nombres de los objetos que se deben seleccionar, escriba `IIS AppPool\` y pegue el contenido del portapapeles. El valor debe tener el aspecto siguiente:

   `IIS AppPool\DefaultAppPool`

1. Haga clic en el botón Comprobar nombres. Cuando Windows resuelva la cuenta de usuario, haga clic en Aceptar.
1. En el cuadro de diálogo Permisos de la carpeta del distribuidor, seleccione la cuenta que acaba de agregar, habilite todos los permisos de la cuenta **excepto Control** total y haga clic en Aceptar. Haga clic en Aceptar para cerrar el cuadro de diálogo Propiedades de la carpeta.

### Registro del tipo de MIME JSON: IIS 8.5 y 10 {#registering-the-json-mime-type-iis-and}

Utilice el siguiente procedimiento para registrar el tipo MIME JSON, cuando desee que Dispatcher permita llamadas JSON.

1. En el Administrador de IIS, seleccione el sitio web y, mediante la vista de características, haga doble clic en Tipos de MIME.
1. Si la extensión JSON no está en la lista, en el panel Acciones haga clic en Agregar, introduzca los siguientes valores de propiedad y, a continuación, haga clic en Aceptar:

   * Extensión de nombre de archivo: `.json`
   * Tipo MIME: `application/json`

### Eliminación del segmento oculto bin - IIS 8.5 y 10 {#removing-the-bin-hidden-segment-iis-and}

Utilice el procedimiento siguiente para eliminar el segmento `bin` oculto. Los sitios Web que no son nuevos pueden contener este segmento oculto.

1. En el Administrador de IIS, seleccione el sitio web y, mediante la vista de características, haga doble clic en Filtrado de solicitudes.
1. Seleccione el `bin` segmento, haga clic en Eliminar y, en el cuadro de diálogo de confirmación, haga clic en Sí.

### Registro de mensajes IIS en un archivo - IIS 8.5 y 10 {#logging-iis-messages-to-a-file-iis-and}

Utilice el siguiente procedimiento para escribir los mensajes de registro de Dispatcher en un archivo de registro en lugar de en el registro de eventos de Windows. Debe configurar Dispatcher para que utilice el archivo de registro y proporcionar a IIS acceso de escritura al archivo.

1. Utilice el Explorador de Windows para crear una carpeta con el nombre `dispatcher` debajo de la carpeta de registros de la instalación de IIS. La ruta de esta carpeta para una instalación típica es `C:\inetpub\logs\dispatcher`.

1. Haga clic con el botón secundario en la carpeta del despachante y haga clic en Propiedades.
1. En la ficha Seguridad, haga clic en Editar y, a continuación, en el cuadro de diálogo Permisos, haga clic en Agregar. Se abre un cuadro de diálogo para seleccionar cuentas de usuario. Haga clic en el botón Ubicaciones, seleccione el nombre del equipo y, a continuación, haga clic en Aceptar.

   Mantenga este cuadro de diálogo abierto mientras completa el paso siguiente.

1. en el Administrador de IIS, seleccione el sitio de IIS que está utilizando para la caché de Dispatcher y, en la parte derecha de la ventana, haga clic en Configuración avanzada.
1. Seleccione el valor de la propiedad Grupo de aplicaciones y cópielo en el portapapeles.
1. Vuelva al cuadro de diálogo abierto. En el cuadro Escriba los nombres de los objetos que se deben seleccionar, escriba `IIS AppPool\` y pegue el contenido del portapapeles. El valor debe tener el aspecto siguiente:

   `IIS AppPool\DefaultAppPool`

1. Haga clic en el botón Comprobar nombres. Cuando Windows resuelva la cuenta de usuario, haga clic en Aceptar.
1. En el cuadro de diálogo Permisos de la carpeta del despachante, seleccione la cuenta que acaba de agregar, habilite todos los permisos de la cuenta **excepto Control total,** y haga clic en Aceptar. Haga clic en Aceptar para cerrar el cuadro de diálogo Propiedades de la carpeta.
1. Utilice un editor de texto para abrir el `disp_iis.ini` archivo.
1. Agregue una línea de texto similar al siguiente ejemplo para configurar la ubicación del archivo de registro y, a continuación, guarde el archivo:

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### Próximos pasos {#next-steps}

Antes de empezar a usar Dispatcher, debe saber:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configure AEM](page-invalidate.md) para que funcione con Dispatcher.

## Apache Web Server {#apache-web-server}

>[!CAUTION]
>
>Las instrucciones para la instalación tanto en **Windows** como en **Unix** se describen aquí. Tenga cuidado al realizar los pasos.

### Instalación de Apache Web Server {#installing-apache-web-server}

Para obtener información sobre cómo instalar un servidor web Apache, lea el manual de instalación, ya sea [en línea](https://httpd.apache.org/) o en la distribución.

>[!CAUTION]
>
>Si va a crear un archivo binario Apache compilando los archivos de origen, asegúrese de activar el soporte **de módulos** dinámicos. Esto se puede hacer con cualquiera de las opciones **—habilitar y compartir** . Incluya como mínimo el `mod_so` módulo.
>
>Puede encontrar más información en el manual de instalación de Apache Web Server.

Consulte también las Sugerencias [de](https://httpd.apache.org/docs/2.4/misc/security_tips.html) seguridad y los informes [de seguridad del servidor HTTP Apache](https://httpd.apache.org/security_report.html).

### Servidor Web Apache - Agregar el módulo Dispatcher {#apache-web-server-add-the-dispatcher-module}

El despachante viene como:

* **Windows**: una biblioteca de vínculos dinámicos (DLL)
* **Unix**: un objeto compartido dinámico (DSO)

Los archivos del archivo de instalación contienen los siguientes archivos, según si ha seleccionado Windows o Unix:

| Archivo | Descripción |
|--- |--- |
| disp_apache&lt;x.y&gt;.dll | Windows:Archivo de biblioteca de vínculos dinámicos de Dispatcher. |
| dispatcher-apache&lt;x.y&gt;-&lt;rel-nr&gt;.so | Unix: El archivo de biblioteca de objetos compartidos Dispatcher. |
| mod_dispatcher.so | Unix: Un vínculo de ejemplo. |
| http.conf.disp&lt;x&gt; | Un archivo de configuración de ejemplo para el servidor Apache. |
| dispatcher.any | Un archivo de configuración de ejemplo para Dispatcher. |
| README | Archivo Léame que contiene instrucciones de instalación e información de última hora. **Nota**: Verifique este archivo antes de iniciar la instalación. |
| CAMBIOS | Archivo de cambios que enumera los problemas corregidos en las versiones actual y pasada. |

Siga estos pasos para agregar Dispatcher a su servidor Web Apache:

1. Coloque el archivo Dispatcher en el directorio del módulo Apache correspondiente:

   * **Windows**: Colocar `disp_apache<x.y>.dll``<APACHE_ROOT>/modules`
   * **Unix**: Busque el `<APACHE_ROOT>/libexec` directorio o el `<APACHE_ROOT>/modules`directorio según la instalación.\
      Copie `dispatcher-apache<options>.so` en este directorio.\
      Para simplificar el mantenimiento a largo plazo también puede crear un vínculo simbólico denominado `mod_dispatcher.so` al despachante:\
      `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Copie el archivo dispatcher.any en el `<APACHE_ROOT>/conf` directorio.

   **** Nota: Puede colocar este archivo en una ubicación diferente, siempre y cuando la propiedad DispatcherLog del módulo Dispatcher esté configurada en consecuencia. (Consulte a continuación las entradas de configuración específicas del despachante).

### Servidor web Apache - Configurar propiedades de SELinux {#apache-web-server-configure-selinux-properties}

Si está ejecutando Dispatcher en RedHat Linux Kernel 2.6 con SELinux habilitado, puede que encuentre mensajes de error como este en el archivo de registro del despachante.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

Esto se debe probablemente a una seguridad SELinux habilitada. A continuación, debe realizar las siguientes tareas:

* Configure el contexto SELinux del archivo del módulo dispatcher.
* Habilite los módulos y secuencias de comandos HTTPD para realizar conexiones de red.
* Configure el contexto SELinux de docroot, donde se almacenan los archivos en caché.

Introduzca los siguientes comandos en una ventana de terminal, reemplazando `[path to the dispatcher.so file]` por la ruta al módulo Dispatcher que instaló en Apache Web Server y *`path to the docroot`* por la ruta de acceso donde se encuentra docroot (p. ej. `/opt/cq/cache`):

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_content_t "[path to the docroot](/.*)?"
```

### Apache Web Server - Configurar Apache Web Server para Dispatcher {#apache-web-server-configure-apache-web-server-for-dispatcher}

Es necesario configurar el servidor web Apache, usando `httpd.conf`. En el kit de instalación de Dispatcher encontrará un archivo de configuración de ejemplo llamado `httpd.conf.disp<x>`.

Estos pasos son obligatorios:

1. Ir a `<APACHE_ROOT>/conf`.
1. Abra `httpd.conf`para editar.
1. Deben agregarse las siguientes entradas de configuración, en el orden indicado:

   * **LoadModule** para cargar el módulo al iniciar.
   * Entradas de configuración específicas del despachante, incluidas **DispatcherConfig, DispatcherLog** y **DispatcherLogLevel**.
   * **SetHandler** para activar Dispatcher. **LoadModule**.
   * **ModMimeUsePathInfo** para configurar el comportamiento de **mod_mime**.

1. (Opcional) Se recomienda cambiar el propietario del directorio htdocs:

   * El servidor apache se inicia como raíz, aunque los procesos secundarios se inician como daemon (por motivos de seguridad). DocumentRoot (`<APACHE_ROOT>/htdocs`) debe pertenecer al demonio de usuario:

      ```xml
      cd <APACHE_ROOT>  
      chown -R daemon:daemon htdocs
      ```

**LoadModule**

En la tabla siguiente se enumeran los ejemplos que se pueden utilizar; las entradas exactas están según el servidor web Apache específico:

|  |  |
|--- |--- |
| Windows | `... LoadModule dispatcher_module modules\disp_apache.dll ...` |
| Unix (suponiendo vínculo simbólico) | `... LoadModule dispatcher_module libexec/mod_dispatcher.so ...` |

>[!NOTE]
>
>El primer parámetro de cada sentencia debe escribirse exactamente como en los ejemplos anteriores.
>
>Consulte los archivos de configuración de ejemplo proporcionados y la documentación de Apache Web Server para obtener información detallada sobre este comando.

**Entradas de configuración específicas del despachante**

Las entradas de configuración específicas del despachante se colocan después de la entrada LoadModule. En la tabla siguiente se muestra un ejemplo de configuración aplicable tanto a Unix como a Windows:

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

Parámetros de configuración individuales:

| Parámetro | Descripción |
|--- |--- |
| DispatcherConfig | Ubicación y nombre del archivo de configuración de Dispatcher. <br/>Cuando esta propiedad se encuentra en la configuración del servidor principal, todos los hosts virtuales heredan el valor de la propiedad. Sin embargo, los hosts virtuales pueden incluir una propiedad DispatcherConfig para anular la configuración del servidor principal. |
| DispatcherLog | Ubicación y nombre del archivo de registro. |
| DispatcherLogLevel | Nivel de registro del archivo de registro: <br/>0 - Errores <br/>1 - Advertencias <br/>2 - Información <br/>3 - <br/>**Nota** de depuración: Se recomienda establecer el nivel de registro en 3 durante la instalación y la prueba y, a continuación, en 0 cuando se ejecute en un entorno de producción. |
| DispatcherNoServerHeader | *Este parámetro está en desuso y ya no tiene ningún efecto.*<br/><br/> Define el encabezado del servidor que se va a utilizar: <br/><ul><li>undefined o 0: el encabezado del servidor HTTP contiene la versión de AEM. </li><li>1 - se utiliza el encabezado del servidor Apache.</li></ul> |
| DispatcherDeclineRoot | Define si se rechazan las solicitudes a la raíz "/": <br/>**0** - aceptar solicitudes a / <br/>**1** - solicitudes a / no son manejadas por el despachante; use mod_alias para la asignación correcta. |
| DispatcherUseProcessedURL | Define si se usarán direcciones URL preprocesadas para todo el procesamiento posterior de Dispatcher: <br/>**0** : utilice la dirección URL original que se pasa al servidor web. <br/>**1** : el despachante utiliza la dirección URL ya procesada por los controladores que preceden al despachante (por ejemplo: `mod_rewrite`) en lugar de la dirección URL original que se pasa al servidor web.  Por ejemplo, la dirección URL original o procesada coincide con los filtros de Dispatcher. La dirección URL también se utiliza como base para la estructura de archivos de caché.   Consulte la documentación del sitio Web de Apache para obtener información sobre mod_rewrite; por ejemplo, Apache 2.4. Al utilizar mod_rewrite, es aconsejable utilizar el indicador 'pass-through | PT' (pasar al siguiente controlador) para forzar al motor de reescritura a establecer el campo uri de la estructura request_rec interna en el valor del campo de nombre de archivo. |
| DispatcherPassError | Define cómo se admiten los códigos de error para la gestión de ErrorDocument: <br/>**0** : Dispatcher detecta todas las respuestas de error al cliente. <br/>**1** - Dispatcher no genera una respuesta de error al cliente (donde el código de estado es mayor o igual que 400), pero pasa el código de estado a Apache, lo cual, por ejemplo, permite que una directiva ErrorDocument procese dicho código de estado. <br/>**Intervalo** de código: especifique un rango de códigos de error para los que la respuesta se pasa a Apache. Se pasan otros códigos de error al cliente. Por ejemplo, la siguiente configuración envía al cliente las respuestas del error 412 y todos los demás errores se pasan a Apache: DispatcherPassError 400-411,413-417 |
| DispatcherKeepAliveTimeout | Especifica el tiempo de espera de mantenimiento, en segundos. A partir de la versión 4.2.0 de Dispatcher, el valor predeterminado es 60. Un valor de 0 deshabilita el mantenimiento vivo. |
| DispatcherNoCanonURL | Si se establece este parámetro en Activado, se pasará la dirección URL sin procesar al servidor en lugar del canonicalizado y se anulará la configuración de DispatcherUseProcessedURL. El valor predeterminado es Desactivado. <br/>**Nota**: Las reglas de filtro de la configuración de Dispatcher siempre se evaluarán con respecto a la dirección URL saneada, no con respecto a la dirección URL sin procesar. |




>[!NOTE]
>
>Las entradas de ruta son relativas al directorio raíz del servidor Web Apache.

>[!NOTE]
>
>La configuración predeterminada para el encabezado del servidor es: `  
ServerTokens Full` `  
DispatcherNoServerHeader 0`\
Muestra la versión de AEM (con fines estadísticos). Si desea desactivar que dicha información esté disponible en el encabezado, puede establecer: `  
ServerTokens Prod`\
Consulte la Documentación de [Apache sobre la Directiva ServerTokens (por ejemplo, para Apache 2.4)](https://httpd.apache.org/docs/2.4/mod/core.html) para obtener más información.

**SetHandler**

Después de estas entradas, debe agregar una **instrucción SetHandler** al contexto de la configuración ( `<Directory>`, `<Location>`) para que Dispatcher pueda gestionar las solicitudes entrantes. El ejemplo siguiente configura Dispatcher para que gestione las solicitudes del sitio web completo:

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

En el ejemplo siguiente se configura Dispatcher para que gestione las solicitudes de un dominio virtual:

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
El parámetro de la instrucción **SetHandler** debe escribirse *exactamente como en los ejemplos* anteriores, ya que es el nombre del controlador definido en el módulo.
Consulte los archivos de configuración de ejemplo proporcionados y la documentación de Apache Web Server para obtener información detallada sobre este comando.

**ModMimeUsePathInfo**

Después de la **instrucción SetHandler** , también debe agregar la definición **ModMimeUsePathInfo** .

>[!NOTE]
El `ModMimeUsePathInfo` parámetro solo debe utilizarse y configurarse si utiliza Dispatcher versión 4.0.9 o superior.
(Tenga en cuenta que Dispatcher versión 4.0.9 se publicó en 2011. Si utiliza una versión anterior, sería adecuado actualizar a una versión reciente de Dispatcher).

El **parámetro ModMimeUsePathInfo** debe establecerse `On` para todas las configuraciones de Apache:

`ModMimeUsePathInfo On`

El módulo mod_mime (consulte, por ejemplo, [Apache Module mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)) se utiliza para asignar metadatos de contenido al contenido seleccionado para una respuesta HTTP. La configuración predeterminada significa que cuando mod_mime determina el tipo de contenido, solo se tendrá en cuenta la parte de la dirección URL que se asigna a un archivo o directorio.

Cuando `On`, el `ModMimeUsePathInfo` parámetro especifica que `mod_mime` es para determinar el tipo de contenido en función de la dirección URL *completa* ; esto significa que los recursos virtuales tendrán la información de metadatos aplicada en función de su extensión.

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

### Habilitar compatibilidad para HTTPS (Unix y Linux) {#enable-support-for-https-unix-and-linux}

Dispatcher utiliza OpenSSL para implementar la comunicación segura a través de HTTP. A partir de Dispatcher versión **4.2.0**, se admiten OpenSSL 1.0.0 y OpenSSL 1.0.1. Dispatcher utiliza OpenSSL 1.0.0 de forma predeterminada. Para utilizar OpenSSL 1.0.1, utilice el siguiente procedimiento para crear vínculos simbólicos de modo que Dispatcher utilice las bibliotecas OpenSSL instaladas.

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
Si utiliza una versión personalizada de Apache, asegúrese de que Apache y Dispatcher estén compilados con la misma versión de [OpenSSL](https://www.openssl.org/source/).

### Próximos pasos {#next-steps-1}

Antes de empezar a usar Dispatcher, debe:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configure AEM](page-invalidate.md) para que funcione con Dispatcher.

## Sun Java System Web Server / iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
Las instrucciones para los entornos Windows y Unix se tratan aquí.
Tenga cuidado al seleccionar qué ejecutar.

### Sun Java System Web Server / iPlanet - Instalación del servidor Web {#sun-java-system-web-server-iplanet-installing-your-web-server}

Para obtener información completa sobre cómo instalar estos servidores Web, consulte la documentación correspondiente:

* Sun Java System Web Server
* iPlanet Web Server

### Sun Java System Web Server / iPlanet - Agregar el módulo Dispatcher {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

El despachante viene como:

* **Windows**: una biblioteca de vínculos dinámicos (DLL)
* **Unix**: un objeto compartido dinámico (DSO)

Los archivos del archivo de instalación contienen los siguientes archivos, según si ha seleccionado Windows o Unix:

| Archivo | Descripción |
|---|---|
| `disp_ns.dll` | Windows:Archivo de biblioteca de vínculos dinámicos de Dispatcher. |
| `dispatcher.so` | Unix: El archivo de biblioteca de objetos compartidos Dispatcher. |
| `dispatcher.so` | Unix: Un vínculo de ejemplo. |
| `obj.conf.disp` | Un archivo de configuración de ejemplo para el servidor web iPlanet/Sun Java System. |
| `dispatcher.any` | Un archivo de configuración de ejemplo para Dispatcher. |
| README | Archivo Léame que contiene instrucciones de instalación e información de última hora. Nota: Verifique este archivo antes de iniciar la instalación. |
| CAMBIOS | Archivo de cambios que enumera los problemas corregidos en las versiones actual y pasada. |

Siga estos pasos para agregar Dispatcher a su servidor web:

1. Coloque el archivo Dispatcher en el directorio `plugin` del servidor web:

### Sun Java System Web Server / iPlanet - Configurar para Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

Es necesario configurar el servidor web mediante `obj.conf`. En el kit de instalación de Dispatcher encontrará un archivo de configuración de ejemplo llamado `obj.conf.disp`.

1. Ir a `<WEBSERVER_ROOT>/config`.
1. Abra `obj.conf`para editar.
1. Copie la línea que empieza:\
   `Service fn="dispService"`\
   desde `obj.conf.disp` a la sección de inicialización de `obj.conf`.

1. Guarde los cambios.
1. Abrir `magnus.conf` para edición.
1. Copie las dos líneas que comienzan:\
   `Init funcs="dispService, dispInit"`\
   y\
   `Init fn="dispInit"`\
   desde `obj.conf.disp` a la sección de inicialización de `magnus.conf`.

1. Guarde los cambios.

>[!NOTE]
Las siguientes configuraciones deben estar todas en una línea y `$(SERVER_ROOT)` y `$(PRODUCT_SUBDIR)` deben reemplazarse por los valores respectivos.

**Init**

En la tabla siguiente se enumeran los ejemplos que se pueden utilizar; las entradas exactas se ajustan a su servidor web específico:

**Windows y Unix**

```
...  
Init funcs="dispService,dispInit" fn="load-modules" shlib="$(SERVER\_ROOT)/plugins/dispatcher.so"  
Init fn="dispInit" config="$(PRODUCT\_SUBDIR)/dispatcher.any" loglevel="1" logfile="$(PRODUCT\_SUBDIR)/logs/dispatcher.log"  
keepalivetimeout="60"  
...
```

donde:

| Parámetro | Descripción |
|--- |--- |
| config | Ubicación y nombre del archivo de configuración `dispatcher.any.` |
| archivo de registro | Ubicación y nombre del archivo de registro. |
| loglevel | <br/> Nivel de registro para cuando se escriben mensajes en el archivo de registro: Errores **** 0<br/> **** 1<br/> Advertencias **** 2<br/> Infos **** 3<br/> Depuración **** Nota: Se recomienda establecer el nivel de registro en 3 durante la instalación y la prueba y en 0 cuando se ejecuta en un entorno de producción. |
| keepalivetimeout | Especifica el tiempo de espera de mantenimiento, en segundos. A partir de la versión 4.2.0 de Dispatcher, el valor predeterminado es 60. Un valor de 0 deshabilita el mantenimiento vivo. |

Según sus necesidades, puede definir Dispatcher como un servicio para los objetos. Para configurar Dispatcher para todo el sitio web, modifique el objeto predeterminado:


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

### Próximos pasos {#next-steps-2}

Antes de empezar a usar Dispatcher, debe:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configure AEM](page-invalidate.md) para que funcione con Dispatcher.
