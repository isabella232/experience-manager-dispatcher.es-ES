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
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Instalación de Dispatcher {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher incrustada en la documentación de una versión anterior de AEM.

Utilice [la página Notas](release-notes.md) de revisión de Dispatcher para obtener el archivo de instalación de Distribuidor más reciente para el sistema operativo y el servidor web. Los números de versiones de Dispatcher son independientes de los números de versiones de Adobe Experience Manager y son compatibles con las versiones de Adobe Experience Manager 6. x, 5. x y Adobe CQ 5. x.

Se utiliza la siguiente convención de nombre de archivo:

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

Por ejemplo, `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` el archivo contiene la versión 4.3.1 de Dispatcher para un servidor web Apache 2.4 que se ejecuta en Linux i 686 y se empaqueta con el formato **tar** .

La siguiente tabla muestra el identificador del servidor Web que se utiliza en los nombres de archivos de cada servidor Web:

| Servidor web | Kit de instalación |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt; otros parámetros &gt; |
| Apache 2.2 | dispatcher-apache **2.2**-&lt; otros parámetros &gt; |
| Servidor de información de Internet de Microsoft 7.5, 8, 8.5 | dispatcher-**iis**-&lt; otros parámetros &gt; |
| Sun Java Web Server iplanet | dispatcher-**ns**-&lt; otros parámetros &gt; |

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

## Servidor de información de Internet de Microsoft {#microsoft-internet-information-server}

Para obtener información sobre cómo instalar este servidor Web, consulte los siguientes recursos:

* Propia documentación de Microsoft en el Servidor de información de Internet
* [&quot; Sitio oficial de Microsoft IIS &quot;](https://www.iis.net/)

### Componentes IIS necesarios {#required-iis-components}

Las versiones 8.5 y 10 de IIS requieren que se instalen los componentes IIS siguientes:

* Extensiones ISAPI

Además, debe agregar la función Servidor Web (IIS). Utilice el Administrador de servidor para agregar la función y los componentes.

## Microsoft IIS - Instalación del módulo Dispatcher {#microsoft-iis-installing-the-dispatcher-module}

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

1. Utilice el Explorador de Windows para crear `<IIS_INSTALLDIR>/Scripts` el directorio, por ejemplo `C:\inetpub\Scripts`.

1. Extraiga los siguientes archivos del paquete Dispatcher en este directorio Scripts:

   * `disp_iis.dll`
   * `disp_iis.ini`
   * Uno de los siguientes archivos depende de si Dispatcher funciona con una instancia de autor o instancia de publicación de AEM:
      * Instancia de autor: `author_dispatcher.any`
      * Publicar instancia: `dispatcher.any`

## Microsoft IIS - Configurar el archivo Dispatcher INI {#microsoft-iis-configure-the-dispatcher-ini-file}

Edite `disp_iis.ini` el archivo para configurar la instalación de Dispatcher. El formato básico del `.ini` archivo es el siguiente:

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
| configpath | Ubicación del `dispatcher.any` sistema de archivos local (ruta absoluta). |
| logfile | Ubicación del `dispatcher.log` archivo. Si no se establece, entonces los mensajes de registro se dirigen al registro de eventos de Windows. |
| nivel de inicio de sesión | Define el nivel de registro utilizado para enviar mensajes al registro de eventos. Se pueden especificar los siguientes valores: Nivel de registro para el archivo de registro: <br/>0 - solo mensajes de error. <br/>1: errores y advertencias. <br/>2: errores, advertencias e mensajes informativos <br/>3: errores, advertencias, mensajes informativos y debug. <br/>**Nota**: Se recomienda establecer el nivel de registro en 3 durante la instalación y la prueba, y luego en 0 al ejecutarse en un entorno de producción. |
| replaceauthorization | Especifica cómo se gestionan los encabezados de autorización en la solicitud HTTP. Los valores siguientes son válidos:<br/>0 - Los encabezados de autorización no se modifican. <br/>1 - Reemplaza cualquier encabezado llamado «Autorización» que no sea «Básico» con su `Basic <IIS:LOGON\_USER>` equivalente.<br/> |
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

### Configuración de Microsoft IIS {#configuring-microsoft-iis}

Configure IIS para integrar el módulo ISAPI Dispatcher. En IIS Utilice la asignación de aplicaciones comodín.

### Configuración de acceso anónimo - IIS 8.5 y 10 {#configuring-anonymous-access-iis-and}

El agente de replicación Flush predeterminado en la instancia de autor se configura para que no envíe credenciales de seguridad con solicitudes vaciadas. Por lo tanto, el sitio web que utilice la caché Dispatcher debe permitir el acceso anónimo.

Si el sitio web utiliza un método de autenticación, el agente de replicación Vaciar debe configurarse en consecuencia.

1. Abra el administrador IIS y seleccione el sitio web que está utilizando como caché de Disptcher.
1. Con el modo de vista de funciones, en la sección IIS haga doble clic en Autenticación.
1. Si la autenticación anónima no está habilitada, seleccione Autenticación anónima y en el área Acciones haga clic en Habilitar.

### Integración del módulo ISAPI Dispatcher - IIS 8.5 y 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Siga el procedimiento siguiente para agregar el módulo ISAPI Dispatcher a IIS.

1. Abra el Administrador IIS.
1. Seleccione el sitio web que está usando como caché de Dispatcher.
1. Con el modo Vista de funciones, en la sección IIS, haga doble clic en Controlador de asignaciones.
1. En el panel Acciones de la página Asignaciones de controlador, haga clic en Agregar mapa de secuencias de comandos comodín, agregue los valores de propiedad siguientes y haga clic en Aceptar:

   * Ruta de solicitud: *
   * Ejecutable: La ruta absoluta del archivo disp_ iis. dll, por ejemplo `C:\inetpub\Scripts\disp_iis.dll`.
   * Nombre: Un nombre descriptivo para la asignación del controlador, por ejemplo `Dispatcher`.

1. En el cuadro de diálogo que aparece, para agregar la biblioteca disp_ iis. dll a la lista ISAPI y CGI Restricciones, haga clic en Sí.

   Para IIS 7.0 y 7.5, la configuración se ha completado. Continúe con los pasos restantes si está configurando IIS 8.0.

1. (IIS 8.0) En la lista de asignaciones de controlador, seleccione la que acaba de crear y, en el área Acciones, haga clic en Editar.
1. (IIS 8.0) En el cuadro de diálogo Editar mapa de secuencias de comandos, haga clic en el botón Restricciones de solicitud.
1. (IIS 8.0) Para asegurarse de que el controlador se utiliza para archivos y carpetas que aún no se encuentran en caché, anule la selección de Invocar controlador solo si la solicitud está asignada a y, a continuación, haga clic en Aceptar.
1. (IIS 8.0) En el cuadro de diálogo Editar mapa de secuencias de comandos, haga clic en Aceptar.

### Configuración del acceso a la caché - IIS 8.5 y 10 {#configuring-access-to-the-cache-iis-and}

Proporcione al usuario del grupo de aplicaciones predeterminado acceso de escritura a la carpeta que se utiliza como caché de Dispatcher.

1. Haga clic con el botón derecho en la carpeta raíz del sitio web que utiliza como caché de Dispatcher y haga clic en Propiedades, como `C:\inetpub\wwwroot`.
1. En la ficha Seguridad, haga clic en Editar y, a continuación, en el cuadro de diálogo Permisos, haga clic en Agregar. Se abre un cuadro de diálogo para seleccionar cuentas de usuario. Haga clic en el botón Ubicaciones, seleccione el nombre del equipo y, a continuación, haga clic en Aceptar.

   Mantenga este cuadro de diálogo abierto mientras completa el siguiente paso.

1. En el administrador de IIS, seleccione el sitio IIS que está utilizando para la caché de Dispatcher y, a la derecha de la ventana, haga clic en Configuración avanzada.
1. Seleccione el valor de la propiedad App Pool y cópielo en el portapapeles.
1. Vuelva al cuadro de diálogo abierto. En el cuadro Introducir nombres de objetos para seleccionar, escriba `IIS AppPool\` y pegue el contenido del portapapeles. El valor debería ser similar al siguiente ejemplo:

   `IIS AppPool\DefaultAppPool`

1. Haga clic en el botón de verificación Nombres. Cuando Windows resuelva la cuenta de usuario, haga clic en Aceptar.
1. En el cuadro de diálogo Permisos para la carpeta Dispatcher, seleccione la cuenta que acaba de agregar, habilite todos los permisos de la cuenta **excepto Control completo** y haga clic en Aceptar. Haga clic en Aceptar para cerrar el cuadro de diálogo Propiedades de carpeta.

### Registro del tipo MIME de JSON - IIS 8.5 y 10 {#registering-the-json-mime-type-iis-and}

Utilice el procedimiento siguiente para registrar el tipo MIME MSON cuando desee que Dispatcher permita llamadas JSON.

1. En el administrador de IIS, seleccione su sitio web y utilice la vista de funciones, haga doble clic en Tipos Mime.
1. Si la extensión JSON no se encuentra en la lista, en el panel Acciones, haga clic en Agregar, introduzca los siguientes valores de propiedad y, a continuación, haga clic en Aceptar:

   * Extensión de nombre de archivo: `.json`
   * Tipo MIME: `application/json`

### Eliminación del segmento oculto de la bandeja - IIS 8.5 y 10 {#removing-the-bin-hidden-segment-iis-and}

Siga el procedimiento siguiente para eliminar el segmento `bin` oculto. Los sitios Web que no son nuevos pueden contener este segmento oculto.

1. En el administrador de IIS, seleccione su sitio web y utilice la vista de funciones, haga doble clic en Filtrar filtros.
1. Seleccione `bin` el segmento, haga clic en Quitar y, en el cuadro de diálogo de confirmación, haga clic en Sí.

### Registro de mensajes IIS a un archivo - IIS 8.5 y 10 {#logging-iis-messages-to-a-file-iis-and}

Siga el procedimiento siguiente para escribir mensajes de registro Dispatcher en un archivo de registro en lugar del registro de eventos de Windows. Debe configurar Dispatcher para que utilice el archivo de registro y proporcionar IIS con acceso de escritura al archivo.

1. Utilice el Explorador de Windows para crear una carpeta llamada `dispatcher` por debajo de la carpeta de registros de la instalación de IIS. La ruta de esta carpeta para una instalación típica es `C:\inetpub\logs\dispatcher`.

1. Haga clic con el botón derecho en la carpeta Dispatcher y haga clic en Propiedades.
1. En la ficha Seguridad, haga clic en Editar y, a continuación, en el cuadro de diálogo Permisos, haga clic en Agregar. Se abre un cuadro de diálogo para seleccionar cuentas de usuario. Haga clic en el botón Ubicaciones, seleccione el nombre del equipo y, a continuación, haga clic en Aceptar.

   Mantenga este cuadro de diálogo abierto mientras completa el siguiente paso.

1. En el administrador de IIS, seleccione el sitio IIS que está utilizando para la caché de Dispatcher y, a la derecha de la ventana, haga clic en Configuración avanzada.
1. Seleccione el valor de la propiedad App Pool y cópielo en el portapapeles.
1. Vuelva al cuadro de diálogo abierto. En el cuadro Introducir nombres de objetos para seleccionar, escriba `IIS AppPool\` y pegue el contenido del portapapeles. El valor debería ser similar al siguiente ejemplo:

   `IIS AppPool\DefaultAppPool`

1. Haga clic en el botón de verificación Nombres. Cuando Windows resuelva la cuenta de usuario, haga clic en Aceptar.
1. En el cuadro de diálogo Permisos para la carpeta Dispatcher, seleccione la cuenta que acaba de agregar, habilite todos los permisos de la cuenta** excepto Control completo,** y haga clic en Aceptar. Haga clic en Aceptar para cerrar el cuadro de diálogo Propiedades de carpeta.
1. Utilice un editor de texto para abrir el `disp_iis.ini` archivo.
1. Agregue una línea de texto similar al siguiente ejemplo para configurar la ubicación del archivo de registro y, a continuación, guarde el archivo:

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### Pasos siguientes {#next-steps}

Antes de empezar a utilizar Dispatcher, debe saber:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configura AEM](page-invalidate.md) para que funcione con Dispatcher.

## Servidor Web Apache {#apache-web-server}

>[!CAUTION]
>
>Aquí se tratan las instrucciones para la instalación en **Windows** y **Unix** . Tenga cuidado al realizar los pasos.

### Instalación del servidor Web Apache {#installing-apache-web-server}

Para obtener información sobre cómo instalar un servidor web Apache, lea el manual de instalación, ya sea [en línea](https://httpd.apache.org/) o en la distribución.

>[!CAUTION]
>
>Si está creando un archivo binario Apache compilando los archivos de origen, asegúrese de activar la compatibilidad con módulos **dinámicos**. Esto se puede hacer utilizando cualquiera de las **—opciones compartidas** . Como mínimo, incluya el `mod_so` módulo.
>
>Encontrará más información en el manual de instalación de Apache Web Server.

Consulte también las Sugerencias Seguridad y [los informes](https://httpd.apache.org/docs/2.2/misc/security_tips.html) [de seguridad de Apache HTTP Server](https://httpd.apache.org/security_report.html).

### Servidor Web Apache: Agregar el módulo Dispatcher {#apache-web-server-add-the-dispatcher-module}

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
      Copiar `dispatcher-apache<options>.so` en este directorio.\
      Para simplificar el mantenimiento a largo plazo, también puede crear un vínculo simbólico denominado `mod_dispatcher.so` Dispatcher:\
      `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Copie el archivo dispatcher. any en `<APACHE_ROOT>/conf` el directorio.

   **Nota:** Puede colocar este archivo en una ubicación diferente, siempre y cuando la propiedad dispatcherlog del módulo Dispatcher esté configurada en consecuencia. (Consulte Entradas de configuración específicas de Dispatcher a continuación.)

### Servidor Web Apache: configurar propiedades de selinux {#apache-web-server-configure-selinux-properties}

Si ejecuta Dispatcher en redhat Linux Kernel 2.6 con selinux habilitado, puede aparecer en mensajes de error como este en el archivo de registro de Dispatcher.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

Esto probablemente se deba a una seguridad de selinux habilitada. A continuación, debe realizar las tareas siguientes:

* Configure el contexto selinux del archivo del módulo Dispatcher.
* Activar secuencias de comandos y módulos HTTPD para realizar conexiones de red.
* Configure el contexto selinux del docroot, donde se almacenan los archivos en caché.

Introduzca los comandos siguientes en una ventana de terminal, sustituya `[path to the dispatcher.so file]` por la ruta del módulo Dispatcher que instaló en Apache Web Server y *`path to the docroot`* con la ruta donde se ubica el punto (p. ej. `/opt/cq/cache`,):

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_content_t "[path to the docroot](/.*)?"
```

### Servidor web Apache: Configurar Apache Web Server for Dispatcher {#apache-web-server-configure-apache-web-server-for-dispatcher}

El servidor Web Apache debe configurarse, utilizar `httpd.conf`. En el kit de instalación de Dispatcher encontrará un archivo de configuración de ejemplo denominado `httpd.conf.disp<x>`.

Estos pasos son obligatorios:

1. Ir a `<APACHE_ROOT>/conf`.
1. Abrir `httpd.conf`para editar.
1. Se deben agregar las entradas de configuración siguientes, en el orden enumerado:

   * **Loadmodule** para cargar el módulo al principio.
   * Entradas de configuración específicas del despachante, incluidas **dispatcherconfig, dispatcherlog** y **dispatcherloglevel**.
   * **Sethandler** para activar Dispatcher. **Loadmodule**.
   * **Modmimeusepathinfo** para configurar el comportamiento de **mod_ mime**.

1. (Opcional) Se recomienda cambiar el propietario del directorio htdocs:

   * El servidor apache comienza como raíz, aunque los procesos secundarios comienzan como daemon (por cuestiones de seguridad). Documentroot (`<APACHE_ROOT>/htdocs`) debe pertenecer al daemon del usuario:

      ```xml
      cd <APACHE_ROOT>  
      chown -R daemon:daemon htdocs
      ```

**Loadmodule**

En la tabla siguiente se muestran ejemplos que pueden utilizarse; las entradas exactas dependen del servidor Web Apache específico:

|  |
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
| Dispatcherloglevel | Nivel de registro para el archivo de registro: <br/>0 - Errores <br/>1 - Advertencias <br/>2 - Infos <br/>3 - <br/>**Nota de depuración**: Se recomienda establecer el nivel de registro en 3 durante la instalación y la prueba, y luego en 0 al ejecutarse en un entorno de producción. |
| Dispatchernoserverheader | *Este parámetro está obsoleto y ya no tiene ningún efecto.*<br/><br/> Define el encabezado del servidor que se utilizará: <br/><ul><li>undefined o 0: el encabezado del servidor HTTP contiene la versión de AEM. </li><li>1: se utiliza el encabezado del servidor Apache.</li></ul> |
| Dispatcherdeclineroot | Define si se rechazarán las solicitudes a la raíz &quot;/&quot;: <br/>**0** - aceptar solicitudes a/ <br/>**1** - las solicitudes a/no son gestionadas por el despachante; utilice mod_ alias para la asignación correcta. |
| Dispatcheruseprocessedurl | Define si se usarán direcciones URL preprocesadas para todo el procesamiento realizado por Dispatcher: <br/>**0** - utilice la dirección URL original que se pasa al servidor Web. <br/>**1** : el despachante utiliza la dirección URL ya procesada por los controladores que preceden al despachante (es decir `mod_rewrite`,) en lugar de la URL original pasada al servidor web. Por ejemplo, la URL original o la URL procesada se coinciden con los filtros Dispatcher. La dirección URL también se utiliza como base para la estructura de archivos caché. Consulte la documentación del sitio Web Apache para obtener información sobre mod_ rewrite; por ejemplo, Apache 2.2. Al utilizar mod_ rewrite, se recomienda utilizar el parámetro&#39;passthrough &#39; | PT &#39; (pasar al siguiente controlador) para obligar al motor de reescritura a definir el campo uri de la estructura internal request_ rec al valor del campo de nombre de archivo. |
| Dispatcherpasserror | Define cómo admitir los códigos de error para la gestión de errordocument: <br/>**0** - Dispatcher oculta todas las respuestas de error al cliente. <br/>**1** - Dispatcher no genera una respuesta de error al cliente (donde el código de estado es mayor o igual que 400), sino que pasa el código de estado a Apache, que por ejemplo permite que una directiva errordocument procese dicho código de estado. <br/>**Rango** de código: especifique una serie de códigos de error para los que la respuesta se pasa a Apache. Se pasan otros códigos de error al cliente. Por ejemplo, la siguiente configuración pasa las respuestas para el error 412 al cliente y se pasan a Apache todos los demás errores: Dispatcherpasserror 400-411.413-417 |
| Dispatcherkeepalivetimeout | Especifica el tiempo de espera de retención, en segundos. Desde la versión 4.2.0 de Dispatcher, el valor predeterminado para guardar es 60. Un valor de 0 deshabilita la supervivencia. |
| Dispatchernocanonurl | Si se define este parámetro como Activado, pasará la dirección URL sin procesar al servidor en lugar de la canalización canonicalizada y se omitirá la configuración de dispatcheruseprocessedurl. El valor predeterminado es Desactivado. <br/>**Nota**: Las reglas de filtro de la configuración Dispatcher se evaluarán siempre comparándolas con la URL sanitista que no sea la URL sin procesar. |




>[!NOTE]
>
>Las entradas de ruta son relativas al directorio raíz del servidor Web Apache.

>[!NOTE]
>
>La configuración predeterminada para el encabezado del servidor es: `  
ServerTokens Full``  
DispatcherNoServerHeader 0`\
Muestra la versión de AEM (con fines estadísticos). Si desea deshabilitar dicha información en el encabezado que puede configurar: `  
ServerTokens Prod`\
Consulte [la documentación de Apache sobre la directiva servertokens (por ejemplo, Apache 2.2)](https://httpd.apache.org/docs/2.2/mod/core.html) para obtener más información.

**Sethandler**

Después de estas entradas, debe agregar una **instrucción sethandler** al contexto de la configuración ( `<Directory>`, `<Location>`) para que Dispatcher gestione las solicitudes entrantes. El siguiente ejemplo configura Dispatcher para gestionar solicitudes para el sitio web completo:

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
El parámetro de la instrucción **sethandler** debe escribirse *exactamente como en los ejemplos anteriores*, ya que es el nombre del controlador definido en el módulo.
Consulte los archivos de configuración de ejemplo proporcionados y la documentación de Apache Web Server para obtener detalles completos sobre este comando.

**Modmimeusepathinfo**

Después de **la** sentencia sethandler, también debe agregar la definición **modmimeusepathinfo** .

>[!NOTE]
El `ModMimeUsePathInfo` parámetro solo debe utilizarse y configurarse si utiliza la versión 4.0.9 de Dispatcher o superior.
(Tenga en cuenta que la versión 4.0.9 de Dispatcher se publicó en 2011. Si utiliza una versión anterior, la actualización a una versión reciente de Dispatcher sería adecuada).

El parámetro **modmimeusepathinfo** debe configurarse `On` para todas las configuraciones de Apache:

`ModMimeUsePathInfo On`

El módulo mod_ mime (consulte por ejemplo [, Apache Module mod_ mime](https://httpd.apache.org/docs/2.2/mod/mod_mime.html)) se utiliza para asignar metadatos de contenido al contenido seleccionado para una respuesta HTTP. La configuración predeterminada significa que cuando mod_ mime determina el tipo de contenido, solo se tomará en cuenta la parte de la URL que se asigna a un archivo o directorio.

Cuando `On`, el `ModMimeUsePathInfo` parámetro especifica que `mod_mime` es para determinar el tipo de contenido basado en la URL *completa* ; Esto significa que los recursos virtuales tendrán la metainformación aplicada según su extensión.

El siguiente ejemplo activa **modmimeusepathinfo**:

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

Dispatcher utiliza openssl para implementar la comunicación segura a través de HTTP. Desde la versión **4.2.0 de Dispatcher**, se admiten openssl 1.0.0 y openssl 1.0.1. Dispatcher utiliza openssl 1.0.0 de forma predeterminada. Para utilizar openssl 1.0.1, utilice el procedimiento siguiente para crear vínculos simbólicos de modo que Dispatcher utilice las bibliotecas openssl instaladas.

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
Si utiliza una versión personalizada de Apache, asegúrese de que Apache y Dispatcher se compilan con la misma versión [de openssl](https://www.openssl.org/source/).

### Pasos siguientes {#next-steps-1}

Antes de empezar a utilizar Dispatcher, debe:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configura AEM](page-invalidate.md) para que funcione con Dispatcher.

## Sun Java System Web Server/iplanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
Aquí se describen las instrucciones para entornos Windows y Unix.
Tenga cuidado al seleccionar la ejecución.

### Sun Java System Web Server/iplanet - Instalación del servidor Web {#sun-java-system-web-server-iplanet-installing-your-web-server}

Para obtener información completa sobre cómo instalar estos servidores Web, consulte la documentación correspondiente:

* Sun Java System Web Server
* Iplanet Web Server

### Sun Java System Web Server/iplanet - Agregar el módulo Dispatcher {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

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

1. Coloque el archivo Dispatcher en `plugin` el directorio del servidor web:

### Sun Java System Web Server/iplanet - Configurar para Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

El servidor web debe configurarse, utilizar `obj.conf`. En el kit de instalación de Dispatcher encontrará un archivo de configuración de ejemplo denominado `obj.conf.disp`.

1. Ir a `<WEBSERVER_ROOT>/config`.
1. Abrir `obj.conf`para editar.
1. Copie la línea que se inicia:\
   `Service fn="dispService"`\
   desde `obj.conf.disp` la sección de inicialización de `obj.conf`.

1. Guarde los cambios.
1. Abrir `magnus.conf` para editar.
1. Copie las dos líneas que se inician:\
   `Init funcs="dispService, dispInit"`\
   y\
   `Init fn="dispInit"`\
   desde `obj.conf.disp` la sección de inicialización de `magnus.conf`.

1. Guarde los cambios.

>[!NOTE]
Las siguientes configuraciones deben estar en una línea y en la `$(SERVER_ROOT)` y `$(PRODUCT_SUBDIR)` deben reemplazarse con los valores respectivos.

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
| config | Ubicación y nombre del archivo de configuración `dispatcher.any.` |
| logfile | Ubicación y nombre del archivo de registro. |
| nivel de inicio de sesión | Nivel de registro para cuando se escriben mensajes en el archivo de registro: <br/>**0** Errores <br/>**1** Advertencias <br/>**2** Infos <br/>**3** Debug <br/>**Note:** Se recomienda establecer el nivel de registro en 3 durante la instalación y la prueba y en 0 cuando se ejecute en un entorno de producción. |
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

### Pasos siguientes {#next-steps-2}

Antes de empezar a utilizar Dispatcher, debe:

* [Configurar](dispatcher-configuration.md) Dispatcher
* [Configura AEM](page-invalidate.md) para que funcione con Dispatcher.
