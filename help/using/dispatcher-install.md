---
title: Instalación de Dispatcher
seo-title: Instalación de AEM Dispatcher
description: Aprenda a instalar el módulo Dispatcher en el servidor de información de Internet de Microsoft, Apache Web Server y Sun Java Web Server-iplanet.
seo-description: Aprenda a instalar el módulo AEM Dispatcher en el servidor de información de Internet de Microsoft, Apache Web Server y Sun Java Web Server-iplanet.
uuid: 2384 b 907-1042-4707-b 02 f-fba 2125618 cf cf
contentOwner: Usuario
converted: verdadero
topic-tags: dispatcher
content-type: referencia
discoiquuid: f 00 ad 751-6 b 95-4365-8500-e 1 e 0108 d 9536
translation-type: tm+mt
source-git-commit: 6d3ff696780ce55c077a1d14d01efeaebcb8db28

---


# Installing Dispatcher {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher incrustada en la documentación de una versión anterior de AEM.

Use the [Dispatcher Release Notes](release-notes.md) page to obtain the latest Dispatcher installation file for your operating system and web server. Los números de versiones de Dispatcher son independientes de los números de versiones de Adobe Experience Manager y son compatibles con las versiones de Adobe Experience Manager 6. x, 5. x y Adobe CQ 5. x.

Se utiliza la siguiente convención de nombre de archivo:

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

For example, the `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` file contains Dispatcher version 4.3.1 for an Apache 2.4 web server that runs on Linux i686 and is packaged using the **tar** format.

La siguiente tabla muestra el identificador del servidor Web que se utiliza en los nombres de archivos de cada servidor Web:

| Servidor web | Kit de instalación |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt;other parameters&gt; |
| Servidor de información de Internet de Microsoft 7.5, 8, 8.5 | dispatcher-**iis**-&lt;other parameters&gt; |
| Sun Java Web Server iplanet | dispatcher-**ns**-&lt;other parameters&gt; |

>[!NOTE]
>
>Debe instalar la versión más reciente de Dispatcher que esté disponible para su plataforma. Cada año, debe actualizar la instancia de Dispatcher para utilizar la última versión para aprovechar las mejoras del producto.

Cada archivo contiene los archivos siguientes:

* módulos Dispatcher
* Un archivo de configuración de ejemplo
* El archivo README que contiene instrucciones de instalación y información de último momento
* El archivo CHANGE que enumera los problemas corregidos en las versiones actuales y anteriores

>[!NOTE]
>
>Consulte el archivo README de cualquier nota de la plataforma o cambios de último momento antes de iniciar la instalación.

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

Para obtener información sobre cómo instalar este servidor Web, consulte los siguientes recursos:

* Propia documentación de Microsoft en el Servidor de información de Internet
* [&quot; Sitio oficial de Microsoft IIS &quot;](https://www.iis.net/)

### Required IIS Components {#required-iis-components}

Las versiones 8.5 y 10 de IIS requieren que se instalen los componentes IIS siguientes:

* Extensiones ISAPI

Además, debe agregar la función Servidor Web (IIS). Utilice el Administrador de servidor para agregar la función y los componentes.

## Microsoft IIS - Installing the Dispatcher module {#microsoft-iis-installing-the-dispatcher-module}

El archivo requerido para el sistema de información de Internet de Microsoft es el siguiente:

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

El archivo ZIP contiene los archivos siguientes:

| Archivo | Descripción |
|--- |--- |
| `disp_iis.dll` | El archivo de biblioteca de vínculos dinámicos Dispatcher. |
| `disp_iis.ini` | Archivo de configuración para IIS. Este ejemplo se puede actualizar con sus requisitos. **Nota**: El archivo ini debe tener el mismo nombre-raíz que el dll. |
| `dispatcher.any` | Un archivo de configuración de ejemplo para Dispatcher. |
| `author_dispatcher.any` | Un archivo de configuración de ejemplo para Dispatcher que funciona con la instancia de autor. |
| README | Archivo Léame que contiene instrucciones de instalación y información de último momento. **Nota**: Verifique este archivo antes de iniciar la instalación. |
| CHANGES | Se ha corregido un archivo que enumera los problemas corregidos en las versiones actuales y pasadas. |

Siga el procedimiento siguiente para copiar los archivos Dispatcher en la ubicación correcta.

1. Use Windows Explorer to create the `<IIS_INSTALLDIR>/Scripts` directory, for example, `C:\inetpub\Scripts`.

1. Extraiga los siguientes archivos del paquete Dispatcher en este directorio Scripts:

   * `disp_iis.dll`
   * `disp_iis.ini`
   * Uno de los siguientes archivos depende de si Dispatcher funciona con una instancia de autor o instancia de publicación de AEM:
      * Author instance: `author_dispatcher.any`
      * Publish instance: `dispatcher.any`

## Microsoft IIS - Configure the Dispatcher INI File {#microsoft-iis-configure-the-dispatcher-ini-file}

Edit the `disp_iis.ini` file to configure the Dispatcher installation. The basic format of the `.ini` file is as follows:

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
| configpath | The location of `dispatcher.any` within the local file system (absolute path). |
| logfile | The location of the `dispatcher.log` file. Si no se establece, entonces los mensajes de registro se dirigen al registro de eventos de Windows. |
| nivel de inicio de sesión | Define el nivel de registro utilizado para enviar mensajes al registro de eventos. The following values may be specified:Log level for the log file: <br/>0 - error messages only. <br/>1: errores y advertencias. <br/>2: errores, advertencias e mensajes informativos <br/>3: errores, advertencias, mensajes informativos y debug. <br/>**Nota**: Se recomienda establecer el nivel de registro en 3 durante la instalación y la prueba, y luego en 0 al ejecutarse en un entorno de producción. |
| replaceauthorization | Especifica cómo se gestionan los encabezados de autorización en la solicitud HTTP. The following values are valid:<br/>0 - Authorization headers are not modified. <br/>1 - Reemplaza cualquier encabezado llamado «Autorización» que no sea «Básico» con su `Basic <IIS:LOGON\_USER>` equivalente.<br/> |
| servervariables | Define cómo se procesan las variables de servidor.<br/>0 - Las variables de servidor IIS no se envían ni a Dispatcher ni a AEM. <br/>1: todas las variables de servidor IIS (como `LOGON\_USER, QUERY\_STRING, ...`) se envían al despachante, junto con los encabezados de solicitud (y también a la instancia de AEM si no se almacenan en caché). <br/>Las variables de servidor incluyen `AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` y muchas otras. Consulte la documentación de IIS para ver la lista completa de variables, con detalles. |
| enable_ chunked_ transfer | Define si se habilita (1) o deshabilita (0) la transferencia fragmentada para la respuesta del cliente. El valor predeterminado es 0. |

Una configuración de ejemplo:

```xml
[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0
```

### Configuring Microsoft IIS {#configuring-microsoft-iis}

Configure IIS para integrar el módulo ISAPI Dispatcher. En IIS Utilice la asignación de aplicaciones comodín.

### Configuring Anonymous Access - IIS 8.5 and 10 {#configuring-anonymous-access-iis-and}

El agente de replicación Flush predeterminado en la instancia de autor se configura para que no envíe credenciales de seguridad con solicitudes vaciadas. Por lo tanto, el sitio web que utilice la caché Dispatcher debe permitir el acceso anónimo.

Si el sitio web utiliza un método de autenticación, el agente de replicación Vaciar debe configurarse en consecuencia.

1. Abra el administrador IIS y seleccione el sitio web que está utilizando como caché de Disptcher.
1. Con el modo de vista de funciones, en la sección IIS haga doble clic en Autenticación.
1. Si la autenticación anónima no está habilitada, seleccione Autenticación anónima y en el área Acciones haga clic en Habilitar.

### Integrating the Dispatcher ISAPI Module - IIS 8.5 and 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Siga el procedimiento siguiente para agregar el módulo ISAPI Dispatcher a IIS.

1. Abra el Administrador IIS.
1. Seleccione el sitio web que está usando como caché de Dispatcher.
1. Con el modo Vista de funciones, en la sección IIS, haga doble clic en Controlador de asignaciones.
1. En el panel Acciones de la página Asignaciones de controlador, haga clic en Agregar mapa de secuencias de comandos comodín, agregue los valores de propiedad siguientes y haga clic en Aceptar:

   * Ruta de solicitud: *
   * Executable: The absolute path of the disp_iis.dll file, for example `C:\inetpub\Scripts\disp_iis.dll`.
   * Name: A descriptive name for the handler mapping, for example `Dispatcher`.

1. En el cuadro de diálogo que aparece, para agregar la biblioteca disp_ iis. dll a la lista ISAPI y CGI Restricciones, haga clic en Sí.

   Para IIS 7.0 y 7.5, la configuración se ha completado. Continúe con los pasos restantes si está configurando IIS 8.0.

1. (IIS 8.0) En la lista de asignaciones de controlador, seleccione la que acaba de crear y, en el área Acciones, haga clic en Editar.
1. (IIS 8.0) En el cuadro de diálogo Editar mapa de secuencias de comandos, haga clic en el botón Restricciones de solicitud.
1. (IIS 8.0) Para asegurarse de que el controlador se utiliza para archivos y carpetas que aún no se encuentran en caché, anule la selección de Invocar controlador solo si la solicitud está asignada a y, a continuación, haga clic en Aceptar.
1. (IIS 8.0) En el cuadro de diálogo Editar mapa de secuencias de comandos, haga clic en Aceptar.

### Configuring Access to the Cache - IIS 8.5 and 10 {#configuring-access-to-the-cache-iis-and}

Proporcione al usuario del grupo de aplicaciones predeterminado acceso de escritura a la carpeta que se utiliza como caché de Dispatcher.

1. Right-click the root folder of the website that you are using as the Dispatcher cache and click Properties, such as `C:\inetpub\wwwroot`.
1. En la ficha Seguridad, haga clic en Editar y, a continuación, en el cuadro de diálogo Permisos, haga clic en Agregar. Se abre un cuadro de diálogo para seleccionar cuentas de usuario. Haga clic en el botón Ubicaciones, seleccione el nombre del equipo y, a continuación, haga clic en Aceptar.

   Mantenga este cuadro de diálogo abierto mientras completa el siguiente paso.

1. En el administrador de IIS, seleccione el sitio IIS que está utilizando para la caché de Dispatcher y, a la derecha de la ventana, haga clic en Configuración avanzada.
1. Seleccione el valor de la propiedad App Pool y cópielo en el portapapeles.
1. Vuelva al cuadro de diálogo abierto. In the Enter The Object Names To Select box, type `IIS AppPool\` and then paste the contents of your clipboard. El valor debería ser similar al siguiente ejemplo:

   `IIS AppPool\DefaultAppPool`

1. Haga clic en el botón de verificación Nombres. Cuando Windows resuelva la cuenta de usuario, haga clic en Aceptar.
1. In the Permissions dialog box for the dispatcher folder, select the account that you just added, enable all of the permissions for the account  **except for Full Control** and click OK. Haga clic en Aceptar para cerrar el cuadro de diálogo Propiedades de carpeta.

### Registering the JSON Mime Type - IIS 8.5 and 10 {#registering-the-json-mime-type-iis-and}

Utilice el procedimiento siguiente para registrar el tipo MIME MSON cuando desee que Dispatcher permita llamadas JSON.

1. En el administrador de IIS, seleccione su sitio web y utilice la vista de funciones, haga doble clic en Tipos Mime.
1. Si la extensión JSON no se encuentra en la lista, en el panel Acciones, haga clic en Agregar, introduzca los siguientes valores de propiedad y, a continuación, haga clic en Aceptar:

   * File Name Extension: `.json`
   * MIME Type: `application/json`

### Removing the bin Hidden Segment - IIS 8.5 and 10 {#removing-the-bin-hidden-segment-iis-and}

Use the following procedure to remove the `bin` hidden segment. Los sitios Web que no son nuevos pueden contener este segmento oculto.

1. En el administrador de IIS, seleccione su sitio web y utilice la vista de funciones, haga doble clic en Filtrar filtros.
1. Select the `bin` segment, click Remove, and in the confirmation dialog box click Yes.

### Logging IIS Messages to a File - IIS 8.5 and 10 {#logging-iis-messages-to-a-file-iis-and}

Siga el procedimiento siguiente para escribir mensajes de registro Dispatcher en un archivo de registro en lugar del registro de eventos de Windows. Debe configurar Dispatcher para que utilice el archivo de registro y proporcionar IIS con acceso de escritura al archivo.

1. Use Windows Explorer to create a folder named `dispatcher` below the logs folder of the IIS installation. The path of this folder for a typical installation is `C:\inetpub\logs\dispatcher`.

1. Haga clic con el botón derecho en la carpeta Dispatcher y haga clic en Propiedades.
1. En la ficha Seguridad, haga clic en Editar y, a continuación, en el cuadro de diálogo Permisos, haga clic en Agregar. Se abre un cuadro de diálogo para seleccionar cuentas de usuario. Haga clic en el botón Ubicaciones, seleccione el nombre del equipo y, a continuación, haga clic en Aceptar.

   Mantenga este cuadro de diálogo abierto mientras completa el siguiente paso.

1. En el administrador de IIS, seleccione el sitio IIS que está utilizando para la caché de Dispatcher y, a la derecha de la ventana, haga clic en Configuración avanzada.
1. Seleccione el valor de la propiedad App Pool y cópielo en el portapapeles.
1. Vuelva al cuadro de diálogo abierto. In the Enter The Object Names To Select box, type `IIS AppPool\` and then paste the contents of your clipboard. El valor debería ser similar al siguiente ejemplo:

   `IIS AppPool\DefaultAppPool`

1. Haga clic en el botón de verificación Nombres. Cuando Windows resuelva la cuenta de usuario, haga clic en Aceptar.
1. En el cuadro de diálogo Permisos para la carpeta Dispatcher, seleccione la cuenta que acaba de agregar, habilite todos los permisos de la cuenta** excepto Control completo,** y haga clic en Aceptar. Haga clic en Aceptar para cerrar el cuadro de diálogo Propiedades de carpeta.
1. Use a text editor to open the `disp_iis.ini` file.
1. Agregue una línea de texto similar al siguiente ejemplo para configurar la ubicación del archivo de registro y, a continuación, guarde el archivo:

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### Next Steps {#next-steps}

Antes de empezar a utilizar Dispatcher, debe saber:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configura AEM](page-invalidate.md) para que funcione con Dispatcher.

## Apache Web Server {#apache-web-server}

>[!CAUTION]
>
>Instructions for installation under both **Windows** and **Unix** are covered here. Tenga cuidado al realizar los pasos.

### Installing Apache Web Server {#installing-apache-web-server}

For Information about how to install an Apache Web Server read the installation manual - either [online](https://httpd.apache.org/) or in the distribution.

>[!CAUTION]
>
>If you are creating an Apache binary by compiling the source files, make sure that you turn on **dynamic modules support**. This can be done by using any of the **--enable-shared** options. At a minimum include the `mod_so` module.
>
>Encontrará más información en el manual de instalación de Apache Web Server.

Also see the Apache HTTP Server [Security Tips](https://httpd.apache.org/docs/2.4/misc/security_tips.html) and [Security Reports](https://httpd.apache.org/security_report.html).

### Apache Web Server - Add the Dispatcher Module {#apache-web-server-add-the-dispatcher-module}

Dispatcher viene como:

* **Windows**: una biblioteca de vínculos dinámicos (DLL)
* **Unix**: un objeto compartido dinámico (DSO)

Los archivos de archivo de instalación contienen los archivos siguientes, según si ha seleccionado Windows o Unix:

| Archivo | Descripción |
|--- |--- |
| disp_ apache &lt; x. y &gt;. dll | Windows: El archivo de biblioteca de vínculos dinámicos Dispatcher. |
| dispatcher-apache &lt; x. y &gt;-&lt; rel-nr &gt;. so | Unix: El archivo de biblioteca de objetos compartidos Dispatcher. |
| mod_ dispatcher. so | Unix: Un vínculo de ejemplo. |
| http. conf. disp &lt; x &gt; | Un archivo de configuración de ejemplo para el servidor Apache. |
| dispatcher. any | Un archivo de configuración de ejemplo para Dispatcher. |
| README | Archivo Léame que contiene instrucciones de instalación y información de último momento. **Nota**: Verifique este archivo antes de iniciar la instalación. |
| CHANGES | Se ha corregido un archivo que enumera los problemas corregidos en las versiones actuales y anteriores. |

Siga los pasos siguientes para agregar Dispatcher al servidor web Apache:

1. Coloque el archivo Dispatcher en el directorio apropiado del módulo Apache:

   * **Windows**: Colocar `disp_apache<x.y>.dll``<APACHE_ROOT>/modules`
   * **Unix**: Localice el `<APACHE_ROOT>/libexec``<APACHE_ROOT>/modules`directorio o según su instalación.\
      Copy `dispatcher-apache<options>.so` into this directory.\
      To simplify long-term maintenance you can also create a symbolic link named `mod_dispatcher.so` to the Dispatcher:\
      `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Copy the dispatcher.any file to the `<APACHE_ROOT>/conf` directory.

   **Nota:** Puede colocar este archivo en una ubicación diferente, siempre y cuando la propiedad dispatcherlog del módulo Dispatcher esté configurada en consecuencia. (Consulte Entradas de configuración específicas de Dispatcher a continuación.)

### Apache Web Server - Configure SELinux Properties {#apache-web-server-configure-selinux-properties}

Si ejecuta Dispatcher en redhat Linux Kernel 2.6 con selinux habilitado, puede aparecer en mensajes de error como este en el archivo de registro de Dispatcher.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

Esto probablemente se deba a una seguridad de selinux habilitada. A continuación, debe realizar las tareas siguientes:

* Configure el contexto selinux del archivo del módulo Dispatcher.
* Activar secuencias de comandos y módulos HTTPD para realizar conexiones de red.
* Configure el contexto selinux del docroot, donde se almacenan los archivos en caché.

Enter the following commands in a terminal window, replacing `[path to the dispatcher.so file]` with the path to the Dispatcher module that you installed to Apache Web Server, and *`path to the docroot`* with the path where the docroot is located (e.g. `/opt/cq/cache`):

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_content_t "[path to the docroot](/.*)?"
```

### Apache Web Server - Configure Apache Web Server for Dispatcher {#apache-web-server-configure-apache-web-server-for-dispatcher}

The Apache Web Server needs to be configured, using `httpd.conf`. In the Dispatcher installation kit you will find an example configuration file named `httpd.conf.disp<x>`.

Estos pasos son obligatorios:

1. Ir a `<APACHE_ROOT>/conf`.
1. Open `httpd.conf`for editing.
1. Se deben agregar las entradas de configuración siguientes, en el orden enumerado:

   * **Loadmodule** para cargar el módulo al principio.
   * Dispatcher-specific configuration entries, including **DispatcherConfig, DispatcherLog** and **DispatcherLogLevel**.
   * **Sethandler** para activar Dispatcher. **Loadmodule**.
   * **Modmimeusepathinfo** para configurar el comportamiento de **mod_ mime**.

1. (Opcional) Se recomienda cambiar el propietario del directorio htdocs:

   * El servidor apache comienza como raíz, aunque los procesos secundarios comienzan como daemon (por cuestiones de seguridad). The DocumentRoot (`<APACHE_ROOT>/htdocs`) must belong to the user daemon:

      ```xml
      cd <APACHE_ROOT>  
      chown -R daemon:daemon htdocs
      ```

**Loadmodule**

En la tabla siguiente se muestran ejemplos que pueden utilizarse; las entradas exactas dependen del servidor Web Apache específico:

|  |  |
|--- |--- |
| Windows | `... LoadModule dispatcher_module modules\disp_apache.dll ...` |
| Unix (suponiendo un vínculo simbólico) | `... LoadModule dispatcher_module libexec/mod_dispatcher.so ...` |

>[!NOTE]
>
>El primer parámetro de cada instrucción debe escribirse exactamente como en los ejemplos anteriores.
>
>Consulte los archivos de configuración de ejemplo proporcionados y la documentación de Apache Web Server para obtener detalles completos sobre este comando.

**Entradas de configuración específicas del despachante**

Las entradas de configuración específicas del despachante se ubican después de la entrada loadmodule. La siguiente tabla muestra una configuración de ejemplo aplicable tanto a Unix como a Windows:

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
| Dispatcherconfig | Ubicación y nombre del archivo de configuración Dispatcher. <br/>Cuando esta propiedad se encuentra en la configuración del servidor principal, todos los hosts virtuales heredan el valor de la propiedad. Sin embargo, los anfitriones virtuales pueden incluir una propiedad dispatcherconfig para anular la configuración del servidor principal. |
| Dispatcherlog | Ubicación y nombre del archivo de registro. |
| Dispatcherloglevel | Log level for the log file: <br/>0 - Errors <br/>1 - Warnings <br/>2 - Infos <br/>3 - Debug <br/>**Note**: It is recommended to set the log level to 3 during installation and testing, then to 0 when running in a production environment. |
| Dispatchernoserverheader | *Este parámetro está obsoleto y ya no tiene ningún efecto.*<br/><br/> Define el encabezado del servidor que se utilizará: <br/><ul><li>undefined o 0: el encabezado del servidor HTTP contiene la versión de AEM. </li><li>1: se utiliza el encabezado del servidor Apache.</li></ul> |
| Dispatcherdeclineroot | Defines whether to decline requests to the root &quot;/&quot;: <br/>**0** - accept requests to / <br/>**1** - requests to / are not handled by the dispatcher; use mod_alias for the correct mapping. |
| Dispatcheruseprocessedurl | Defines whether to use pre-processed URLs for all further processing by Dispatcher: <br/>**0** - use the original URL passed to the web server. <br/>**1** : el despachante utiliza la dirección URL ya procesada por los controladores que preceden al despachante (es decir `mod_rewrite`,) en lugar de la URL original pasada al servidor web. Por ejemplo, la URL original o la URL procesada se coinciden con los filtros Dispatcher. La dirección URL también se utiliza como base para la estructura de archivos caché. Consulte la documentación del sitio Web Apache para obtener información sobre mod_ rewrite; por ejemplo, Apache 2.4. Al utilizar mod_ rewrite, se recomienda utilizar el parámetro&#39;passthrough &#39; | PT &#39; (pasar al siguiente controlador) para obligar al motor de reescritura a definir el campo uri de la estructura internal request_ rec al valor del campo de nombre de archivo. |
| Dispatcherpasserror | Defines how to support error codes for ErrorDocument handling: <br/>**0** - Dispatcher spools all error responses to the client. <br/>**1** - Dispatcher no genera una respuesta de error al cliente (donde el código de estado es mayor o igual que 400), sino que pasa el código de estado a Apache, que por ejemplo permite que una directiva errordocument procese dicho código de estado. <br/>**Rango** de código: especifique una serie de códigos de error para los que la respuesta se pasa a Apache. Se pasan otros códigos de error al cliente. Por ejemplo, la siguiente configuración pasa las respuestas para el error 412 al cliente y se pasan a Apache todos los demás errores: Dispatcherpasserror 400-411.413-417 |
| Dispatcherkeepalivetimeout | Especifica el tiempo de espera de retención, en segundos. Desde la versión 4.2.0 de Dispatcher, el valor predeterminado para guardar es 60. Un valor de 0 deshabilita la supervivencia. |
| Dispatchernocanonurl | Si se define este parámetro como Activado, pasará la dirección URL sin procesar al servidor en lugar de la canalización canonicalizada y se omitirá la configuración de dispatcheruseprocessedurl. El valor predeterminado es Desactivado. <br/>**Nota**: Las reglas de filtro de la configuración Dispatcher se evaluarán siempre comparándolas con la URL sanitista que no sea la URL sin procesar. |




>[!NOTE]
>
>Las entradas de ruta son relativas al directorio raíz del servidor Web Apache.

>[!NOTE]
>
>The default settings for the Server Header are: `  
ServerTokens Full` `  
DispatcherNoServerHeader 0`\
Muestra la versión de AEM (con fines estadísticos). If you want to disable such information being available in the header you can set: `  
ServerTokens Prod`\
See the [Apache Documentation about ServerTokens Directive (for example, for Apache 2.4)](https://httpd.apache.org/docs/2.4/mod/core.html) for more information.

**Sethandler**

After these entries you must add a **SetHandler** statement to the context of your configuration ( `<Directory>`, `<Location>`) for the Dispatcher to handle the incoming requests. El siguiente ejemplo configura Dispatcher para gestionar solicitudes para el sitio web completo:

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

El siguiente ejemplo configura Dispatcher para gestionar solicitudes de un dominio virtual:

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
The parameter of the **SetHandler** statement must be written *exactly as in the above examples*, as this is the name of the handler defined in the module.
Consulte los archivos de configuración de ejemplo proporcionados y la documentación de Apache Web Server para obtener detalles completos sobre este comando.

**Modmimeusepathinfo**

After the **SetHandler** statement you should also add the **ModMimeUsePathInfo** definition.

>[!NOTE]
The `ModMimeUsePathInfo` parameter should only be used and configured if you are using Dispatcher version 4.0.9, or higher.
(Tenga en cuenta que la versión 4.0.9 de Dispatcher se publicó en 2011. Si utiliza una versión anterior, la actualización a una versión reciente de Dispatcher sería adecuada).

The **ModMimeUsePathInfo** parameter should be set `On` for all Apache configurations:

`ModMimeUsePathInfo On`

The mod_mime module (see for example, [Apache Module mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)) is used to assign content metadata to the content selected for an HTTP response. La configuración predeterminada significa que cuando mod_ mime determina el tipo de contenido, solo se tomará en cuenta la parte de la URL que se asigna a un archivo o directorio.

When `On`, the `ModMimeUsePathInfo` parameter specifies that `mod_mime` is to determine the content type based on the *complete* URL; this means that virtual resources will have metainformation applied based on their extension.

The following example activates **ModMimeUsePathInfo**:

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

### Enable Support for HTTPS (Unix and Linux) {#enable-support-for-https-unix-and-linux}

Dispatcher utiliza openssl para implementar la comunicación segura a través de HTTP. Starting from Dispatcher version **4.2.0**, OpenSSL 1.0.0 and OpenSSL 1.0.1 are supported. Dispatcher utiliza openssl 1.0.0 de forma predeterminada. Para utilizar openssl 1.0.1, utilice el procedimiento siguiente para crear vínculos simbólicos de modo que Dispatcher utilice las bibliotecas openssl instaladas.

1. Abra un terminal y cambie el directorio actual al directorio donde están instaladas las bibliotecas openssl, por ejemplo:

   ```shell
   cd /usr/lib64
   ```

1. Para crear los vínculos simbólicos, introduzca los siguientes comandos:

   ```shell
   ln -s libssl.so libssl.so.1.0.1
   ln -s libcrypto.so libcrypto.so.1.0.1
   ```

>[!NOTE]
If you are using a customized version of Apache, make sure Apache and Dispatcher are compiled using the same version of [OpenSSL](https://www.openssl.org/source/).

### Next Steps {#next-steps-1}

Antes de empezar a utilizar Dispatcher, debe:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configura AEM](page-invalidate.md) para que funcione con Dispatcher.

## Sun Java System Web Server / iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
Aquí se describen las instrucciones para entornos Windows y Unix.
Tenga cuidado al seleccionar la ejecución.

### Sun Java System Web Server / iPlanet - Installing your Web Server {#sun-java-system-web-server-iplanet-installing-your-web-server}

Para obtener información completa sobre cómo instalar estos servidores Web, consulte la documentación correspondiente:

* Sun Java System Web Server
* Iplanet Web Server

### Sun Java System Web Server / iPlanet - Add the Dispatcher Module {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

Dispatcher viene como:

* **Windows**: una biblioteca de vínculos dinámicos (DLL)
* **Unix**: un objeto compartido dinámico (DSO)

Los archivos de archivo de instalación contienen los archivos siguientes, según si ha seleccionado Windows o Unix:

| Archivo | Descripción |
|---|---|
| `disp_ns.dll` | Windows: El archivo de biblioteca de vínculos dinámicos Dispatcher. |
| `dispatcher.so` | Unix: El archivo de biblioteca de objetos compartidos Dispatcher. |
| `dispatcher.so` | Unix: Un vínculo de ejemplo. |
| `obj.conf.disp` | Un archivo de configuración de ejemplo para el servidor web del sistema Java de iplanet/Sun Java. |
| `dispatcher.any` | Un archivo de configuración de ejemplo para Dispatcher. |
| README | Archivo Léame que contiene instrucciones de instalación y información de último momento. Nota: Verifique este archivo antes de iniciar la instalación. |
| CHANGES | Se ha corregido un archivo que enumera los problemas corregidos en las versiones actuales y anteriores. |

Siga estos pasos para agregar Dispatcher a su servidor Web:

1. Place the Dispatcher file in the web server&#39;s `plugin` directory:

### Sun Java System Web Server / iPlanet - Configure for the Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

The web server needs to be configured, using `obj.conf`. In the Dispatcher installation kit you will find an example configuration file named `obj.conf.disp`.

1. Ir a `<WEBSERVER_ROOT>/config`.
1. Open `obj.conf`for editing.
1. Copie la línea que se inicia:\
   `Service fn="dispService"`\
   from `obj.conf.disp` to the initialization section of `obj.conf`.

1. Guarde los cambios.
1. Open `magnus.conf` for editing.
1. Copie las dos líneas que se inician:\
   `Init funcs="dispService, dispInit"`\
   y\
   `Init fn="dispInit"`\
   from `obj.conf.disp` to the initialization section of `magnus.conf`.

1. Guarde los cambios.

>[!NOTE]
The following configurations should all be on one line and the `$(SERVER_ROOT)` and `$(PRODUCT_SUBDIR)` must be replaced by the respective values.

**Init**

En la tabla siguiente se muestran ejemplos que pueden utilizarse; las entradas exactas dependen del servidor Web específico:

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
| config | Location and name of the configuration file `dispatcher.any.` |
| logfile | Ubicación y nombre del archivo de registro. |
| nivel de inicio de sesión | Log level for when writing messages to the log file: <br/>**0** Errors <br/>**1** Warnings <br/>**2** Infos <br/>**3** Debug <br/>**Note:** It is recommended to set the log level to 3 during installation and testing and to 0 when running in a production environment. |
| keepalivetimeout | Especifica el tiempo de espera de retención, en segundos. Desde la versión 4.2.0 de Dispatcher, el valor predeterminado para guardar es 60. Un valor de 0 deshabilita la supervivencia. |

Según sus necesidades, puede definir Dispatcher como servicio para los objetos. Para configurar Dispatcher para todo el sitio web, modifique el objeto predeterminado:


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

### Next Steps {#next-steps-2}

Antes de empezar a utilizar Dispatcher, debe:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configura AEM](page-invalidate.md) para que funcione con Dispatcher.
