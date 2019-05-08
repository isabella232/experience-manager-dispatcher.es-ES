---
title: 'Uso de Dispatcher con varios dominios '
seo-title: 'Uso de Dispatcher con varios dominios '
description: Aprenda a utilizar Dispatcher para procesar solicitudes de página en varios dominios Web.
seo-description: Aprenda a utilizar Dispatcher para procesar solicitudes de página en varios dominios Web.
uuid: 7342 a 1 c 2-fe 61-49 be-a 240-b 487 d 53 c 7 ec 1
contentOwner: Usuario
cq-exporttemplate: /etc/contentsync/templates/geometrixx/page/rewrite
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referencia
discoiquuid: 40 d 91 d 66-c 99 b -422 d -8 e 61-c 0 ated 23272 ef
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Uso de Dispatcher con varios dominios {#using-dispatcher-with-multiple-domains}

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher incrustada en la documentación de AEM o CQ.

Utilice Dispatcher para procesar solicitudes de página en varios dominios Web al mismo tiempo que admite las condiciones siguientes:

* El contenido web para ambos dominios se almacena en un solo repositorio de AEM.
* Los archivos de la caché de Dispatcher se pueden invalidar por separado para cada dominio.

Por ejemplo, una empresa publica sitios web para dos de sus marcas: Marca A y Marca B. El contenido de las páginas del sitio web se crea en AEM y se almacena en el mismo espacio de trabajo del repositorio:

```
/
| - content  
   | - sitea  
       | - content nodes  
   | - siteb  
       | - content nodes
```

Las páginas de se `BrandA.com` almacenan a continuación `/content/sitea`. Se devuelven las solicitudes del cliente de la dirección URL `https://BrandA.com/en.html` a la página representada para el `/content/sitea/en` nodo. De manera similar, las páginas de `BrandB.com` se almacenan a continuación `/content/siteb`.

Cuando se utiliza Dispatcher para almacenar contenido en caché, las asociaciones deben realizarse entre la dirección URL de la página en la solicitud HTTP del cliente, la ruta del archivo correspondiente en la caché y la ruta del archivo correspondiente en el repositorio.

## Solicitudes del cliente

Cuando los clientes envían solicitudes HTTP al servidor Web, la dirección URL de la página solicitada debe resolverse en el contenido de la caché de Dispatcher y, finalmente, en el contenido del repositorio.

![](assets/chlimage_1-8.png)

1. El sistema de nombre de dominio detecta la dirección IP del servidor Web que está registrado para el nombre de dominio en la solicitud HTTP.
1. La solicitud HTTP se envía al servidor web.
1. La solicitud HTTP se transfiere a Dispatcher.
1. Dispatcher determina si los archivos en caché son válidos. Si es válido, los archivos en caché se sirven al cliente.
1. Si los archivos en caché no son válidos, Dispatcher solicita las páginas recientemente procesadas desde la instancia de publicación de AEM.

## Invalidación de caché

Cuando los agentes de replicación Despush de Dispatcher solicitan que Dispatcher invalida los archivos almacenados en caché, la ruta del contenido del repositorio debe resolver el contenido en la caché.

![](assets/chlimage_1-9.png)

1. Una página se activa en la instancia de autor de AEM y el contenido se replica en la instancia de publicación.
1. El agente Despachante de despachante llama Dispatcher para invalidar la caché del contenido replicado.
1. Dispatcher toca uno o varios archivos. stat para invalidar los archivos en caché.

Para utilizar Dispatcher con varios dominios, debe configurar AEM, Dispatcher y el servidor web. Las soluciones descritas en esta página son generales y se aplican a la mayoría de entornos. Debido a la complejidad de algunas topologías de AEM, su solución puede necesitar configuraciones personalizadas adicionales para resolver problemas concretos. Es probable que necesite adaptar los ejemplos para satisfacer sus políticas de administración y infraestructura de TI existentes.

## Asignación de URL {#url-mapping}

Para habilitar las direcciones URL de dominio y las rutas de contenido para resolver los archivos almacenados en caché, en algún momento del proceso debe traducirse una ruta de archivo o una dirección URL de página. Se proporcionan descripciones de las siguientes estrategias comunes, donde las traducciones de ruta o URL se producen en diferentes puntos del proceso:

* (Recomendado) La instancia de publicación de AEM utiliza asignación Sling para la resolución de recursos para implementar las reglas internas de reescritura de URL. Las URL de dominio se traducen en rutas de repositorio de contenido. (Consulte [AEM Reescribe URL entrantes](dispatcher-domains.md#main-pars-title-2).)
* El servidor Web utiliza reglas internas de reescritura de URL que traducen direcciones URL de dominio a las rutas de caché. (Consulte [El Servidor Web reescribe las URL entrantes](dispatcher-domains.md#main-pars-title-1).)

Generalmente es deseable utilizar direcciones URL cortas para páginas Web. Normalmente, las direcciones URL de página reflejan la estructura de las carpetas de repositorio que contienen el contenido web. Sin embargo, las direcciones URL no revelan los nodos del repositorio superiores, como `/content`. El cliente no necesariamente tiene en cuenta la estructura del repositorio de AEM.

## Requisitos generales {#general-requirements}

Su entorno debe implementar las configuraciones siguientes para admitir Dispatcher que funcione con varios dominios:

* El contenido de cada dominio reside en ramificaciones independientes del repositorio (consulte el siguiente entorno de ejemplo).
* El agente de replicación Dispatcher Flush se configura en la instancia de publicación de AEM. (Consulte [Invalidating Dispatcher Cache desde una instancia de publicación](page-invalidate.md)).
* El sistema de nombres de dominio resuelve los nombres de dominio en la dirección IP del servidor Web.
* La caché Dispatcher refleja la estructura de directorio del repositorio de contenido de AEM. Las rutas de archivo situadas debajo de la raíz del documento del servidor Web son las mismas que las rutas de los archivos del repositorio.

## Entorno para los ejemplos proporcionados {#environment-for-the-provided-examples}

Las soluciones de ejemplo que se proporcionan se aplican a un entorno con las características siguientes:

* Las instancias de autor y publicación de AEM se implementan en sistemas Linux.
* Apache HTTPD es el servidor web, implementado en un sistema Linux.
* El repositorio de contenido de AEM y la raíz del documento del servidor web utilizan las siguientes estructuras de archivos (la raíz del documento del servidor web Apache es/`usr/lib/apache/httpd-2.4.3/htdocs)`:

   **Repositorio**

```
  | - /content  
    | - sitea  
  |    | - content nodes 
    | - siteb  
       | - conent nodes
```

**Raíz del documento del servidor Web**

```
  | - /usr  
    | - lib  
      | - apache  
        | - httpd-2.4.3  
          | - htdocs  
            | - content  
              | - sitea  
                 | - content nodes 
              | - siteb  
                 | - content nodes
```

## AEM vuelve a escribir las URL entrantes {#aem-rewrites-incoming-urls}

La asignación de Sling para la resolución de recursos permite asociar URL entrantes con las rutas de contenido de AEM. Cree asignaciones en la instancia de publicación de AEM para que las solicitudes de procesamiento de Dispatcher resuelvan el contenido correcto del repositorio.

Las solicitudes Dispatcher para el procesamiento de página identifican la página utilizando la URL que pasa del servidor web. Cuando la URL incluye un nombre de dominio, las asignaciones Sling resuelven la URL del contenido. El siguiente gráfico ilustra una asignación de `branda.com/en.html` la URL al `/content/sitea/en` nodo.

![](assets/chlimage_1-10.png)

La caché de Dispatcher refleja la estructura del nodo del repositorio. Por lo tanto, cuando se producen activaciones de página, las solicitudes resultantes para invadir la página almacenada en caché no requieren ninguna dirección URL ni traducción de ruta.

![](assets/chlimage_1-11.png)

## Definición de los hosts virtuales en el servidor Web {#define-virtual-hosts-on-the-web-server}

Defina los hosts virtuales en el servidor Web para asignar una raíz de documento diferente a cada dominio Web:

* El servidor web debe definir un dominio virtual para cada uno de los dominios web.
* Para cada dominio, configure la raíz del documento para que coincida con la carpeta del repositorio que contiene el contenido web del dominio.
* Cada dominio virtual también debe incluir configuraciones relacionadas con Dispatcher, tal como se describe en la [página Instalación de Dispatcher](dispatcher-install.md) .

El siguiente archivo de ejemplo `httpd.conf` configura dos dominios virtuales para un servidor web Apache:

* Los nombres de servidor (que coinciden con los nombres de dominio) son branda.com (línea 16) y brandb.com (línea 30).
* La raíz del documento de cada dominio virtual es el directorio de la caché Dispatcher que contiene las páginas del sitio. (líneas 17 y 31)

Con esta configuración, el servidor Web realiza las siguientes acciones cuando recibe una solicitud de `https://branda.com/en/products.html`:

* Asocia la dirección URL con el host virtual `ServerName` que tiene `branda.com.`

* Envía la URL a Dispatcher.

### httpd. conf {#httpd-conf}

```xml
# load the Dispatcher module
LoadModule dispatcher_module modules/mod_dispatcher.so
# configure the Dispatcher module
<IfModule disp_apache2.c>
 DispatcherConfig conf/dispatcher.any
 DispatcherLog    logs/dispatcher.log  
 DispatcherLogLevel 3
 DispatcherNoServerHeader 0 
 DispatcherDeclineRoot 0
 DispatcherUseProcessedURL 0
 DispatcherPassError 0
</IfModule>

# Define virtual host for brandA.com
<VirtualHost *:80>
  ServerName branda.com
  DocumentRoot /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea
   <Directory /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea>
     <IfModule disp_apache2.c>
       SetHandler dispatcher-handler
       ModMimeUsePathInfo On
     </IfModule>
     Options FollowSymLinks
     AllowOverride None
   </Directory>
</VirtualHost>

# define virtual host for brandB.com
<VirtualHost *:80>
  ServerName brandB.com
  DocumentRoot /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb
   <Directory /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb>
     <IfModule disp_apache2.c>
       SetHandler dispatcher-handler
       ModMimeUsePathInfo On
     </IfModule>
     Options FollowSymLinks
     AllowOverride None
   </Directory>
</VirtualHost>

# document root for web server
DocumentRoot "/usr/lib/apache/httpd-2.4.3/htdocs"
```

Tenga en cuenta que los anfitriones virtuales heredan el [valor](dispatcher-install.md#main-pars-67-table-7) de propiedad dispatcherconfig que se configura en la sección del servidor principal. Los anfitriones virtuales pueden incluir su propia propiedad dispatcherconfig para anular la configuración del servidor principal.

### Configurar Dispatcher para gestionar varios dominios {#configure-dispatcher-to-handle-multiple-domains}

Para admitir direcciones URL que incluyan nombres de dominio y sus hosts virtuales correspondientes, defina las siguientes granjas Dispatcher:

* Configure una granja Dispatcher para cada host virtual. Estas granjas procesan solicitudes desde el servidor Web para cada dominio, comprueban si hay archivos almacenados en caché y solicitan páginas de los procesadores.
* Configure una granja Dispatcher que se utilice para invalidar el contenido de la caché, independientemente del dominio al que pertenezca el contenido. Esta granja gestiona solicitudes de invalidación de archivos desde agentes de replicación Despush Dispatcher.

### Creación de granjas Dispatcher para hosts virtuales

Las granjas para los anfitriones virtuales deben tener las configuraciones siguientes para que las URL de las solicitudes HTTP del cliente se resuelvan a los archivos correctos en la caché Dispatcher:

* `/virtualhosts` La propiedad se establece en el nombre de dominio. Esta propiedad permite a Dispatcher asociar la granja con el dominio.
* La `/filter` propiedad permite acceder a la ruta de la URL de la solicitud truncada después de la parte del nombre de dominio. Por ejemplo, para `https://branda.com/en.html` la URL, la ruta se interpreta como `/en.html`, por lo que el filtro debe permitir el acceso a esta ruta.

* La `/docroot` propiedad se establece en la ruta del directorio raíz del contenido del sitio del dominio en la caché Dispatcher. Esta ruta se utiliza como prefijo para la dirección URL concatenada de la solicitud original. Por ejemplo, el punto de `/usr/lib/apache/httpd-2.4.3/htdocs/sitea` la solicitud de `https://branda.com/en.html` resolver en `/usr/lib/apache/httpd-2.4.3/htdocs/sitea/en.html` el archivo.

Además, la instancia de publicación de AEM debe designarse como la representación para el host virtual. Configure otras propiedades de granja según sea necesario. El siguiente código es una configuración de granja abreviada para el dominio branda.com:

```xml
/farm_sitea  {     
    ...
    /virtualhosts { "branda.com" }
    /renders {
      /rend01  { /hostname "127.0.0.1"  /port "4503" }
    }
    /filter {
      /0001 { /type "deny"  /glob "*" }
      /0023 { /type "allow" /glob "*/en*" }  
      ...
     }
    /cache {
      /docroot "/usr/lib/apache/httpd-2.4.3/htdocs/content/sitea"
      ...
   }
   ...
}
```

### Creación de una granja Dispatcher para la invalidación de caché

Se requiere una granja Dispatcher para gestionar solicitudes para inutilizar archivos en caché. Esta granja debe tener acceso a archivos. stat en los directorios docroot de cada host virtual.

Las siguientes configuraciones de propiedad permiten a Dispatcher resolver archivos en el repositorio de contenido de AEM desde archivos en la caché:

* `/docroot` La propiedad se establece en el punto predeterminado del servidor web. Normalmente, es el directorio donde se crea la `/content` carpeta. Un valor de ejemplo para Apache en Linux es `/usr/lib/apache/httpd-2.4.3/htdocs`.
* `/filter` La propiedad permite acceder a los archivos situados debajo del `/content` directorio.

`/statfileslevel`La propiedad debe ser lo suficientemente alta para que los archivos. stat se creen en el directorio raíz de cada host virtual. Esta propiedad permite invalidar por separado la caché de cada dominio. Para la configuración de ejemplo, se ha `/statfileslevel``2` creado un valor de archivos. stat en `*docroot*/content/sitea` el directorio y `*docroot*/content/siteb` en el directorio.

Además, la instancia de publicación debe designarse como la representación para el host virtual. Configure otras propiedades de granja según sea necesario. El siguiente código es una configuración abreviada para la granja que se utiliza para invalidar la caché:

```xml
/farm_flush {  
    ...
    /virtualhosts   { "invalidation_only" }
    /renders  {
      /rend01  { /hostname "127.0.0.1" /port "4503" }
    }
    /filter   {
      /0001 { /type "deny"  /glob "*" }
      /0023 { /type "allow" /glob "*/content*" } 
      ...
      }
    /cache  {
       /docroot "/usr/lib/apache/httpd-2.4.3/htdocs"
       /statfileslevel "2"
       ...
   }
   ...
}
```

Al iniciar el servidor Web, el registro Dispatcher (en modo de depuración) indica la inicialización de todas las granjas:

```shell
Dispatcher initializing (build 4.1.2)
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_sitea].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_siteb].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_flush].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs
[Fri Nov 02 16:27:18 2012] [I] [24974(140006182991616)] Dispatcher initialized (build 4.1.2)
```

### Configuración de asignaciones de Sling para la resolución de recursos {#configure-sling-mapping-for-resource-resolution}

Utilice la asignación Sling para la resolución de recursos para que las URL basadas en dominios resuelvan el contenido de la instancia de publicación de AEM. La asignación de recursos traduce las URL entrantes desde Dispatcher (originalmente desde solicitudes HTTP del cliente) a nodos de contenido.

Para obtener más información sobre la asignación de recursos Sling, consulte [Asignaciones de resolución](https://sling.apache.org/site/mappings-for-resource-resolution.html) de recursos en la documentación Sling.

Normalmente, las asignaciones son necesarias para los recursos siguientes, aunque pueden requerirse asignaciones adicionales:

* Nodo raíz de la página de contenido (abajo `/content`)
* Nodo de diseño que utilizan las páginas (abajo `/etc/designs`)
* La `/libs` carpeta

Después de crear la asignación para la página de contenido, para descubrir asignaciones adicionales necesarias utilice un explorador Web para abrir una página en el servidor Web. En el archivo error. log de la instancia de publicación, busque mensajes sobre recursos que no se encuentren. El siguiente mensaje de ejemplo indica que se requiere `/etc/clientlibs` una asignación:

```shell
01.11.2012 15:59:24.601 *INFO* [10.36.34.243 [1351799964599] GET /etc/clientlibs/foundation/jquery.js HTTP/1.1] org.apache.sling.engine.impl.SlingRequestProcessorImpl service: Resource /content/sitea/etc/clientlibs/foundation/jquery.js not found
```

>[!NOTE]
>
>El transacción linkchecker del regrabador Apache Sling predeterminado modifica automáticamente los hipervínculos de la página para evitar los vínculos dañados. Sin embargo, la reescritura de vínculos se realiza únicamente cuando el destino del vínculo es un archivo HTML o HTM. Para actualizar los vínculos a otros tipos de archivos, cree un componente transformer y agréguelo a un canal de reescritura HTML.

### Nodos de asignación de recursos de ejemplo

En la tabla siguiente se enumeran los nodos que implementan la asignación de recursos para el dominio branda.com. Se crean nodos similares para `brandb.com` el dominio, como `/etc/map/http/brandb.com`. En todos los casos, es necesario asignar asignaciones cuando las referencias del HTML de la página no se resuelven correctamente en el contexto de Sling.

| Ruta de nodo | Tipo | Propiedad |
|--- |--- |--- |
| `/etc/map/http/branda.com` | sling: Asignación | Nombre: sling: Tipo de internalredirect: Valor de cadena: /content/sitea |
| `/etc/map/http/branda.com/libs` | sling: Asignación | Nombre: sling: Tipo de internalredirect <br/>: Valor de cadena <br/>: /libs |
| `/etc/map/http/branda.com/etc` | sling: Asignación |
| `/etc/map/http/branda.com/etc/designs` | sling: Asignación | Nombre: sling: Internalredirect <br/>vtype: Cadena <br/>vvalue: /etc/designs |
| `/etc/map/http/branda.com/etc/clientlibs` | sling: Asignación | Nombre: sling: Internalredirect <br/>vtype: Cadena <br/>vvalue: /etc/clientlibs |

## Configuración del agente de replicación Despush de Dispatcher {#configuring-the-dispatcher-flush-replication-agent}

El agente de replicación Despush Despush en la instancia de publicación de AEM debe enviar solicitudes de invalidación a la granja Dispatcher correcta. Para dirigir una granja, utilice la propiedad URI del agente de replicación Despush Despush (en la ficha Transporte). Incluya el valor de `/virtualhost` la propiedad para la granja Dispatcher configurada para invalidar la caché:

`https://*webserver_name*:*port*/*virtual_host*/dispatcher/invalidate.cache`

Por ejemplo, para utilizar `farm_flush` la granja del ejemplo anterior, la URI es `https://localhost:80/invalidation_only/dispatcher/invalidate.cache`.

![](assets/chlimage_1-12.png)

## El servidor Web reescribe las URL entrantes {#the-web-server-rewrites-incoming-urls}

Utilice la función de reescritura de URL interna de su servidor web para traducir URL basadas en dominio a rutas de archivos en la caché de Dispatcher. Por ejemplo, las solicitudes de cliente de `https://brandA.com/en.html` la página se traducen al `content/sitea/en.html`archivo en la raíz del documento del servidor Web.

![](assets/chlimage_1-13.png)

La caché de Dispatcher refleja la estructura del nodo del repositorio. Por lo tanto, cuando se producen activaciones de página, las solicitudes resultantes para invalidar la página almacenada en caché no requieren ninguna dirección URL ni traducción de ruta.

![](assets/chlimage_1-14.png)

## Definir los hosts virtuales y volver a escribir las reglas en el servidor Web {#define-virtual-hosts-and-rewrite-rules-on-the-web-server}

Configure los siguientes aspectos en el servidor Web:

* Defina un host virtual para cada uno de sus dominios Web.
* Para cada dominio, configure la raíz del documento para que coincida con la carpeta del repositorio que contiene el contenido web del dominio.
* Para cada dominio virtual, cree una regla de cambio de nombre de URL que traduzca la dirección URL entrante a la ruta del archivo almacenado en caché.
* Cada dominio virtual también debe incluir configuraciones relacionadas con Dispatcher, tal como se describe en la [página Instalación de Dispatcher](dispatcher-install.md) .
* El módulo Dispatcher debe configurarse para utilizar la dirección URL que el servidor Web ha reescrito. (Consulte `DispatcherUseProcessedURL` la propiedad en [Instalación de Dispatcher](dispatcher-install.md)).

El siguiente ejemplo de archivo httpd. conf configura dos hosts virtuales para un servidor Web Apache:

* Los nombres de servidor (que coinciden con los nombres de dominio) son `brandA.com` (línea 16) y `brandB.com` (línea 32).

* La raíz del documento de cada dominio virtual es el directorio de la caché Dispatcher que contiene las páginas del sitio. (líneas 20 y 33)
* La regla de reescritura de URL para cada dominio virtual es una expresión regular que prefiera la ruta de la página solicitada con la ruta a las páginas en la caché. (líneas 19 y 35)
* La `DispatherUseProcessedURL` propiedad se establece en `1`. (línea 10)

Por ejemplo, el servidor Web realiza las siguientes acciones cuando recibe una solicitud con `https://brandA.com/en/products.html` la dirección URL:

* Asocia la dirección URL con el host virtual `ServerName` que tiene `brandA.com.`
* Vuelve a escribir la dirección URL para que `/content/sitea/en/products.html.`
* Envía la URL a Dispatcher.

### httpd. conf {#httpd-conf-1}

```xml
# load the Dispatcher module
LoadModule dispatcher_module modules/mod_dispatcher.so
# configure the Dispatcher module
<IfModule disp_apache2.c>
 DispatcherConfig conf/dispatcher.any
 DispatcherLog    logs/dispatcher.log  
 DispatcherLogLevel 3
 DispatcherNoServerHeader 0 
 DispatcherDeclineRoot 0
 DispatcherUseProcessedURL 1
 DispatcherPassError 0
</IfModule>

# Define virtual host for brandA.com
<VirtualHost *:80>
  ServerName branda.com
  DocumentRoot /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea
  RewriteEngine  on
  RewriteRule    ^/(.*)\.html$  /content/sitea/$1.html [PT]
   <Directory /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea>
     <IfModule disp_apache2.c>
       SetHandler dispatcher-handler
       ModMimeUsePathInfo On
     </IfModule>
     Options FollowSymLinks
     AllowOverride None
   </Directory>
</VirtualHost>

# define virtual host for brandB.com
<VirtualHost *:80>
  ServerName brandB.com
  DocumentRoot /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb
  RewriteEngine  on
  RewriteRule    ^/(.*)\.html$  /content/siteb/$1.html [PT]
   <Directory /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb>
     <IfModule disp_apache2.c>
       SetHandler dispatcher-handler
       ModMimeUsePathInfo On
     </IfModule>
     Options FollowSymLinks
     AllowOverride None
   </Directory>
</VirtualHost>

# document root for web server
DocumentRoot "/usr/lib/apache/httpd-2.4.3/htdocs"
```

### Configurar una granja Dispatcher {#configure-a-dispatcher-farm}

Cuando el servidor web vuelva a escribir las URL, Dispatcher requiere una sola granja definida según [Configurar Dispatcher](dispatcher-configuration.md). Se requieren las configuraciones siguientes para admitir los hosts virtuales del servidor Web y las reglas de cambio de nombre de URL:

* La `/virtualhosts` propiedad debe incluir los valores de servername para todas las definiciones de virtualhost.
* `/statfileslevel` La propiedad debe ser lo suficientemente alta como para crear archivos. stat en directorios que contengan los archivos de contenido para cada dominio.

El siguiente archivo de configuración de ejemplo se basa en `dispatcher.any` el archivo de ejemplo que se instala con Dispatcher. Se requieren los siguientes cambios para admitir las configuraciones de servidor web del `httpd.conf` archivo anterior:

* La `/virtualhosts` propiedad hace que Dispatcher gestione solicitudes para los dominios `brandA.com` y `brandB.com` los dominios. (línea 12)
* `/statfileslevel` La propiedad se establece en 2, de modo que los archivos de stat se crean en cada directorio que contenga el contenido web del dominio (línea 41): `/statfileslevel "2"`

Como de costumbre, la raíz del documento de la caché es la misma que la raíz del documento del servidor web (línea 40): `/usr/lib/apache/httpd-2.4.3/htdocs`

### `dispatcher.any` {#dispatcher-any}

```xml
/name "testDispatcher"
/farms
  {
  /dispfarm0
    {  
    /clientheaders
      {
      "*"
      }      
    /virtualhosts
      {
      "brandA.com" "brandB.com"
      }
    /renders
      {
      /rend01    {  /hostname "127.0.0.1"   /port "4503"  }
      }
    /filter
      {
      /0001 { /type "deny"  /glob "*" }
      /0023 { /type "allow" /glob "*/content*" }  # disable this rule to allow mapped content only
      /0041 { /type "allow" /glob "* *.css *"   }  # enable css
      /0042 { /type "allow" /glob "* *.gif *"   }  # enable gifs
      /0043 { /type "allow" /glob "* *.ico *"   }  # enable icos
      /0044 { /type "allow" /glob "* *.js *"    }  # enable javascript
      /0045 { /type "allow" /glob "* *.png *"   }  # enable png
      /0046 { /type "allow" /glob "* *.swf *"   }  # enable flash
      /0061 { /type "allow" /glob "POST /content/[.]*.form.html" }  # allow POSTs to form selectors under content
      /0062 { /type "allow" /glob "* /libs/cq/personalization/*"  }  # enable personalization
      /0081 { /type "deny"  /glob "GET *.infinity.json*" }
      /0082 { /type "deny"  /glob "GET *.tidy.json*"     }
      /0083 { /type "deny"  /glob "GET *.sysview.xml*"   }
      /0084 { /type "deny"  /glob "GET *.docview.json*"  }
      /0085 { /type "deny"  /glob "GET *.docview.xml*"  }      
      /0086 { /type "deny"  /glob "GET *.*[0-9].json*" }
      /0090 { /type "deny"  /glob "* *.query.json*" }
      }
    /cache
      {
      /docroot "/usr/lib/apache/httpd-2.4.3/htdocs"
      /statfileslevel "2"
      /allowAuthorized "0"
      /rules
        {
        /0000  { /glob "*"     /type "allow"  }
        }
      /invalidate
        {
        /0000  {   /glob "*" /type "deny"  }
        /0001 {  /glob "*.html" /type "allow"  }
        }
      /allowedClients
        {
        }     
      }
    /statistics
      {
      /categories
        {
        /html  { /glob "*.html" }
        /others  {  /glob "*"  }
        }
      }
    }
  }
```

>[!NOTE]
>
>Como se define una sola granja Dispatcher, el agente de replicación Despush Despush en la instancia de publicación de AEM no requiere configuraciones especiales.

## Reescritura de vínculos a archivos no HTML {#rewriting-links-to-non-html-files}

Para reescribir referencias a archivos que tienen extensiones distintas de.html o.htm, cree un componente transformer Sling rewriter y añádalo al canal de reescritura predeterminado.

Vuelva a escribir referencias cuando las rutas de recursos no se resuelven correctamente en el contexto del servidor web. Por ejemplo, es necesario un transprimido cuando los componentes que generan imágenes crean vínculos como /content/sitea/en/products.navimage.png. El componente superior de la [sección Cómo crear un sitio Web de Internet con funciones](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/the-basics.html) completas crea estos vínculos.

El regrabador [Sling](https://sling.apache.org/documentation/bundles/output-rewriting-pipelines-org-apache-sling-rewriter.html) es un módulo que procesa los resultados de Sling. Las implementaciones de la canalización SAX del regrabador consisten en un generador, uno o más transformadores y un serializador:

* **Generator:** Analiza el flujo de salida Sling (documento HTML) y genera eventos SAX cuando detecta tipos de elementos específicos.
* **Transprimido:** Escucha eventos SAX y, por lo tanto, modifica el destino del evento (un elemento HTML). La canalización de regrabadores contiene cero o más transformadores. Los transformadores se ejecutan en secuencia y pasan los eventos SAX al siguiente transdominio en la secuencia.
* **Serializador:** Serializa el resultado, incluidas las modificaciones de cada transdominio.

![](assets/chlimage_1-15.png)

### Pipeline de reescritura predeterminada de AEM {#the-aem-default-rewriter-pipeline}

AEM utiliza un regrabador predeterminado de Pipeline que procesa documentos de tipo text/html:

* El generador analiza documentos HTML y genera eventos SAX cuando encuentra a, img, area, form, base, link, script y body elementos. El alias del generador es `htmlparser`.
* La canalización incluye los siguientes transformadores: `linkchecker``mobile``mobiledebug``contentsync`. `linkchecker` La transprimencia externaliza las rutas hacia archivos HTML o HTM referenciados para evitar los vínculos dañados.
* El serializador escribe la salida HTML. El alias de serializador es htmlwriter.

El `/libs/cq/config/rewriter/default` nodo define la canalización.

### Creación de un transdominio {#creating-a-transformer}

Realice las tareas siguientes para crear un componente transformer y utilizarlo en un canal:

1. Implemente la `org.apache.sling.rewriter.TransformerFactory` interfaz. Esta clase crea instancias de la clase transformer. Especifique los valores de `transformer.type` la propiedad (el alias transformer) y configure la clase como componente de servicio osgi.
1. Implemente la `org.apache.sling.rewriter.Transformer` interfaz. Para minimizar el trabajo, puede ampliar `org.apache.cocoon.xml.sax.AbstractSAXPipe` la clase. Anule el método startelement para personalizar el comportamiento de reescritura. Este método se llama para cada evento SAX que se pase al transprimido.
1. Empaquete e implemente las clases.
1. Agregue un nodo de configuración a la aplicación AEM para agregar el transaccionar al canal.

>[!TIP]
>En su lugar, puede configurar transformerfactory a que el transacro se inserte en todos los reredactores definidos. Por lo tanto, no es necesario configurar un canal:
>
>* Defina la `pipeline.mode` propiedad como `global`.
>* Establezca `service.ranking` la propiedad en un entero positivo.
>* No incluya `pipeline.type` una propiedad.


>[!NOTE]
>
>Utilice el archetype [multimódulo](https://helpx.adobe.com/experience-manager/aem-previous-versions.html) del complemento de Maven de Content Package para crear su proyecto Maven. Los POMS crean e instalan automáticamente un paquete de contenido.

Los siguientes ejemplos implementan un transformer que reescribe referencias a archivos de imagen.

* La clase myrewritertransformerfactory instala objetos myrewritertransformer. La propiedad pipeline. type establece el alias transformer como mytransformer. Para incluir el alias en una canalización, el nodo de configuración del canal incluye este alias en la lista de transformadores.
* La clase myrewritertransformer anula el método startelement de la clase abstractsaxtransformer. El método startelement vuelve a escribir el valor de los atributos src para elementos img.

Los ejemplos no son sólidos y no deben utilizarse en un entorno de producción.

### Ejemplo de implementación de transformerfactory {#example-transformerfactory-implementation}

```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.rewriter.Transformer;
import org.apache.sling.rewriter.TransformerFactory;

@Component
@Service
public class MyRewriterTransformerFactory implements TransformerFactory {
    /* Define the alias */
    @Property(value="mytransformer")
    static final String PIPELINE_TYPE ="pipeline.type";
 
    public Transformer createTransformer() {
        
        return new MyRewriterTransformer ();
    }
}
```

### Implementación de transformer de ejemplo {#example-transformer-implementation}

```java
package com.adobe.example;

import java.io.IOException;

import org.apache.cocoon.xml.sax.AbstractSAXPipe;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.rewriter.ProcessingComponentConfiguration;
import org.apache.sling.rewriter.ProcessingContext;
import org.apache.sling.rewriter.Transformer;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.xml.sax.Attributes;
import org.xml.sax.SAXException;
import org.xml.sax.helpers.AttributesImpl;

import javax.servlet.http.HttpServletRequest;

public class MyRewriterTransformer extends AbstractSAXPipe implements Transformer {

 private static final Logger log = LoggerFactory.getLogger(MyRewriterTransformer.class);
 private SlingHttpServletRequest httpRequest; 
 /* The element and attribute to act on  */
 private static final String ATT_NAME = new String("src");
 private static final String EL_NAME = new String("img");

 public MyRewriterTransformer () {
 }
 public void dispose() {
 }
 public void init(ProcessingContext context, ProcessingComponentConfiguration config) throws IOException {
  this.httpRequest = context.getRequest();
  log.debug("Transforming request {}.", httpRequest.getRequestURI());
 }
 @Override
 public void startElement (String nsUri, String localname, String qname, Attributes atts) throws SAXException {
  /* copy the element attributes */
  AttributesImpl linkAtts = new AttributesImpl(atts); 
  /* Only interested in EL_NAME elements */
  if(EL_NAME.equalsIgnoreCase(localname)){

   /* iterate through the attributes of the element and act only on ATT_NAME attributes */
   for (int i=0; i < linkAtts.getLength(); i++) {
    if (ATT_NAME.equalsIgnoreCase(linkAtts.getLocalName(i))) {
     String path_in_link = linkAtts.getValue(i);

     /* use the resource resolver of the http request to reverse-resolve the path  */
     String mappedPath = httpRequest.getResourceResolver().map(httpRequest, path_in_link);

     log.info("Tranformed {} to {}.", path_in_link,mappedPath);

     /* update the attribute value */
     linkAtts.setValue(i,mappedPath);
    }
   }

  }
        /* return updated attributes to super and continue with the transformer chain */
 super.startElement(nsUri, localname, qname, linkAtts);
 }
}
```

### Adición del transaccionar a una canalización de redactor {#adding-the-transformer-to-a-rewriter-pipeline}

Cree un nodo JCR que defina un canal que utilice su transaccional. La siguiente definición de nodo crea un canal que procesa archivos text/html. Se utiliza el generador y el analizador predeterminado de AEM para HTML.

>[!NOTE]
>
>Si establece la propiedad Transformer `pipeline.mode` en `global`, no es necesario configurar un canal. El `global` modo inserta el transacro en todos los canales.

### Nodo de configuración de regrabación: representación XML {#rewriter-configuration-node-xml-representation}

```xml
<?xml version="1.0" encoding="UTF-8"?>
<jcr:root xmlns:jcr="https://www.jcp.org/jcr/1.0" xmlns:nt="https://www.jcp.org/jcr/nt/1.0"
    jcr:primaryType="nt:unstructured"
    contentTypes="[text/html]"
    enabled="{Boolean}true"
    generatorType="htmlparser"
    order="5"
    serializerType="htmlwriter"
    transformerTypes="[mytransformer]">
</jcr:root>
```

El siguiente gráfico muestra la representación CRXDE Lite del nodo:

![](assets/chlimage_1-16.png)
