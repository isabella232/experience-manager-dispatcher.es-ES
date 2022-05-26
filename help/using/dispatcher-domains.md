---
title: 'Utilizar Dispatcher con varios dominios '
seo-title: Using Dispatcher with Multiple Domains
description: Aprenda a utilizar Dispatcher para procesar solicitudes de páginas en varios dominios web.
seo-description: Learn how to use Dispatcher to process page requests in multiple web domains.
uuid: 7342a1c2-fe61-49be-a240-b487d53c7ec1
contentOwner: User
cq-exporttemplate: /etc/contentsync/templates/geometrixx/page/rewrite
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 40d91d66-c99b-422d-8e61-c0ced23272ef
exl-id: 1470b636-7e60-48cc-8c31-899f8785dafa
source-git-commit: 9d168ab7139e46b0c768fc3bab37245459eca002
workflow-type: ht
source-wordcount: '2965'
ht-degree: 100%

---

# Utilizar Dispatcher con varios dominios {#using-dispatcher-with-multiple-domains}

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher incrustado en la documentación de AEM o CQ.

Utilice Dispatcher para procesar solicitudes de página en varios dominios web a la vez que admitan las siguientes condiciones:

* El contenido web de ambos dominios se almacena en un único repositorio de AEM.
* Los archivos de la caché de Dispatcher se pueden invalidar por separado para cada dominio.

Por ejemplo, una empresa publica sitios web para dos de sus marcas: la marca A y la marca B. El contenido de las páginas del sitio web se crea en AEM y se almacena en el mismo espacio de trabajo del repositorio:

```
/
| - content  
   | - sitea  
       | - content nodes  
   | - siteb  
       | - content nodes
```

Las páginas para `BrandA.com` se almacenan debajo de `/content/sitea`. Las solicitudes del cliente para la URL `https://BrandA.com/en.html` se devuelven como la página representada para el nodo `/content/sitea/en`. Del mismo modo, las páginas para `BrandB.com` se almacenan debajo de `/content/siteb`.

Al utilizar Dispatcher para almacenar en caché el contenido, se deben realizar asociaciones entre la dirección URL de la página en la solicitud HTTP del cliente, la ruta del archivo correspondiente en la caché y la ruta del archivo correspondiente en el repositorio.

## Solicitudes de cliente

Cuando los clientes envían solicitudes HTTP al servidor web, la dirección URL de la página solicitada debe resolverse al contenido de la caché de Dispatcher y, finalmente, al contenido del repositorio.

![](assets/chlimage_1-8.png)

1. El sistema de nombres de dominio detecta la dirección IP del servidor web que está registrado para el nombre de dominio en la solicitud HTTP.
1. La solicitud HTTP se envía al servidor web.
1. La solicitud HTTP se pasa a Dispatcher.
1. Dispatcher determina si los archivos en caché son válidos. Si lo son, los archivos en caché se proporcionan al cliente.
1. Si los archivos en caché no son válidos, Dispatcher solicita las páginas recién procesadas de la instancia de publicación de AEM.

## Invalidación de caché

Cuando los agentes de replicación de vaciado de Dispatcher solicitan que Dispatcher invalide los archivos en caché, la ruta del contenido en el repositorio debe resolverse en el contenido de la caché.

![](assets/chlimage_1-9.png)

1. Se activará una página en la instancia de autor de AEM y el contenido se duplicará en la instancia de publicación.
1. El agente de vaciado de Dispatcher llama a Dispatcher para invalidar la caché del contenido replicado.
1. Dispatcher toca uno o más archivos .stat para invalidar los archivos en caché.

Para utilizar Dispatcher con varios dominios, debe configurar AEM, Dispatcher y el servidor web. Las soluciones descritas en esta página son generales y se aplican a la mayoría de los entornos. Debido a la complejidad de algunas topologías de AEM, su solución puede requerir más configuraciones personalizadas para resolver problemas específicos. Es probable que necesite adaptar los ejemplos para satisfacer sus políticas de administración e infraestructura de TI.

## Asignar URL {#url-mapping}

Para permitir que las direcciones URL de dominio y las rutas de contenido se resuelvan en archivos en caché, en algún momento del proceso se debe traducir una ruta de archivo o una dirección URL de página. Se proporcionan descripciones de las siguientes estrategias comunes, donde las traducciones de ruta o URL se producen en diferentes puntos del proceso:

* (Recomendado) La instancia de publicación de AEM utiliza la asignación de Sling para la resolución de recursos a fin de implementar reglas de reescritura de URL internas. Las direcciones URL del dominio se traducen en rutas del repositorio de contenido. Consulte [AEM reescribe las URL entrantes](#aem-rewrites-incoming-urls).
* El servidor web utiliza reglas de reescritura de URL internas que traducen las URL de dominio a rutas de caché. Consulte [El servidor web reescribe las URL entrantes](#the-web-server-rewrites-incoming-urls).

En general, es conveniente utilizar direcciones URL cortas para las páginas web. Normalmente, las direcciones URL de la página reflejan la estructura de las carpetas del repositorio que contienen el contenido web. Sin embargo, no muestran los nodos de repositorio superiores, como `/content`. El cliente no es necesariamente consciente de la estructura del repositorio de AEM.

## Requisitos generales {#general-requirements}

Su entorno debe implementar las siguientes configuraciones para apoyar el trabajo de Dispatcher con múltiples dominios:

* El contenido de cada dominio reside en ramas independientes del repositorio (consulte el siguiente entorno de ejemplo).
* El agente de replicación de vaciado de Dispatcher está configurado en la instancia de publicación AEM. (Consulte [Invalidación de la caché de Dispatcher desde una instancia de publicación](page-invalidate.md)).
* El sistema de nombres de dominio resuelve los nombres de dominio en la dirección IP del servidor web.
* La caché de Dispatcher refleja la estructura del directorio del repositorio de contenido de AEM. Las rutas de archivo debajo de la raíz del documento del servidor web son las mismas que las de los archivos del repositorio.

## Entorno para los ejemplos dados {#environment-for-the-provided-examples}

Las soluciones de ejemplo que se proporcionan se aplican a un entorno con las siguientes características:

* Las instancias de autor y de publicación de AEM se implementan en sistemas Linux.
* Apache HTTPD es el servidor web, implementado en un sistema Linux.
* El repositorio de contenido de AEM y la raíz del documento del servidor web utilizan las siguientes estructuras de archivos (la raíz del documento del servidor web Apache es /`usr/lib/apache/httpd-2.4.3/htdocs)`:

   **Repositorio**

```
  | - /content  
    | - sitea  
  |    | - content nodes 
    | - siteb  
       | - conent nodes
```

**Raíz del documento del servidor web**

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

## AEM reescribe las direcciones URL entrantes {#aem-rewrites-incoming-urls}

La asignación de Sling para la resolución de recursos permite asociar las direcciones URL entrantes con rutas de contenido de AEM. Cree asignaciones en la instancia de publicación de AEM para que las solicitudes de Dispatcher se resuelvan en el contenido correcto del repositorio.

Las solicitudes de Dispatcher para la representación de páginas identifican la página con la dirección URL que se pasa desde el servidor web. Cuando la dirección URL incluye un nombre de dominio, las asignaciones de Sling resuelven la dirección URL del contenido. El siguiente gráfico ilustra una asignación de la `branda.com/en.html` dirección URL al nodo `/content/sitea/en`.

![](assets/chlimage_1-10.png)

La caché de Dispatcher refleja la estructura de nodos del repositorio. Por lo tanto, cuando se producen activaciones de página, las solicitudes resultantes para invalidar la página en caché no requieren traducciones de dirección URL o de ruta.

![](assets/chlimage_1-11.png)

## Definir hosts virtuales en el servidor web {#define-virtual-hosts-on-the-web-server}

Defina hosts virtuales en el servidor web para que se pueda asignar una raíz de documento diferente a cada dominio web:

* El servidor web debe definir un dominio virtual para cada uno de los dominios web.
* Para cada dominio, configure la raíz del documento para que coincida con la carpeta del repositorio que contiene el contenido web del dominio.
* Cada dominio virtual también debe incluir configuraciones relacionadas con Dispatcher, como se describe en la página [Instalar Dispatcher](dispatcher-install.md).

El siguiente archivo `httpd.conf` de ejemplo configura dos dominios virtuales para un servidor web Apache:

* Los nombres de servidor (que coinciden con los de dominio) son branda.com (línea 16) y brandb.com (línea 30).
* La raíz del documento de cada dominio virtual es el directorio de la caché de Dispatcher que contiene las páginas del sitio. (líneas 17 y 31)

Con esta configuración, el servidor web realizará las siguientes acciones cuando reciba una solicitud de `https://branda.com/en/products.html`:

* Asocia la dirección URL con el host virtual que tiene un `ServerName` de `branda.com.`

* Reenvía la URL a Dispatcher.

### httpd.conf {#httpd-conf}

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

Tenga en cuenta que los hosts virtuales heredan el valor de propiedad [DispatcherConfig](dispatcher-install.md#main-pars-67-table-7) configurado en la sección del servidor principal. Los hosts virtuales pueden incluir su propia propiedad DispatcherConfig para anular la configuración del servidor principal.

### Configurar Dispatcher para administrar varios dominios {#configure-dispatcher-to-handle-multiple-domains}

Para admitir direcciones URL que incluyan nombres de dominio y sus hosts virtuales correspondientes, defina las siguientes granjas de Dispatcher:

* Configure una granja de Dispatcher para cada host virtual. Estas granjas procesan solicitudes del servidor web para cada dominio, buscan archivos en caché y solicitan páginas de los procesamientos.
* Configure una granja de Dispatcher que se utilice para invalidar contenido en la caché, independientemente del dominio al que pertenezca. Esta granja administra las solicitudes de invalidación de archivos de los agentes de replicación de vaciado de Dispatcher.

### Crear granjas de Dispatcher para hosts virtuales

Los granjas para hosts virtuales deben tener las siguientes configuraciones para que las direcciones URL de las solicitudes HTTP del cliente se resuelvan en los archivos correctos en la caché de Dispatcher:

* La propiedad `/virtualhosts` se establece en el nombre de dominio. Esta propiedad permite a Dispatcher asociar la granja con el dominio.
* La propiedad `/filter` permite acceder a la ruta de la URL de solicitud truncada después de la parte del nombre de dominio. Por ejemplo, para la dirección URL `https://branda.com/en.html`, la ruta se interpreta como `/en.html`, por lo que el filtro debe permitir el acceso a esta ruta.

* La propiedad `/docroot` se establece en la ruta del directorio raíz del contenido del sitio del dominio en la caché de Dispatcher. Esta ruta se utiliza como prefijo de la URL concatenada de la solicitud original. Por ejemplo, el docroot de `/usr/lib/apache/httpd-2.4.3/htdocs/sitea` hace que la solicitud de `https://branda.com/en.html` se resuelva en el archivo `/usr/lib/apache/httpd-2.4.3/htdocs/sitea/en.html`.

Además, la instancia de publicación de AEM debe designarse como el procesamiento para el host virtual. Configure otras propiedades de granja según sea necesario. El siguiente código es una configuración de granja abreviada para el dominio branda.com:

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

### Creación de una granja de Dispatcher para la invalidación de la caché

Se requiere una granja de Dispatcher para administrar solicitudes de invalidación de archivos en caché. Esta granja debe poder acceder a los archivos .stat en los directorios docroot de cada host virtual.

Las siguientes configuraciones de propiedad permiten a Dispatcher resolver archivos en el repositorio de contenido de AEM de archivos en la caché:

* La propiedad `/docroot` se establece en el docroot predeterminado del servidor web. Normalmente, este es el directorio donde se crea la carpeta `/content`. Un valor de ejemplo para Apache en Linux es `/usr/lib/apache/httpd-2.4.3/htdocs`.
* La propiedad `/filter` permite acceder a los archivos que se encuentran debajo del directorio `/content`.

La propiedad `/statfileslevel`debe ser lo suficientemente alta como para que los archivos .stat se creen en el directorio raíz de cada host virtual. Esta propiedad permite invalidar por separado la caché de cada dominio. Para la configuración de ejemplo, un valor `/statfileslevel` de `2` crea archivos .stat en el directorio `*docroot*/content/sitea` y en el `*docroot*/content/siteb`.

Además, la instancia de publicación debe designarse como el procesamiento para el host virtual. Configure otras propiedades de granja según sea necesario. El siguiente código es una configuración abreviada para la granja que se utiliza para invalidar la caché:

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

Cuando se inicia el servidor web, el registro de Dispatcher (en modo de depuración) indica la inicialización de todas las granjas:

```shell
Dispatcher initializing (build 4.1.2)
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_sitea].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_siteb].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_flush].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs
[Fri Nov 02 16:27:18 2012] [I] [24974(140006182991616)] Dispatcher initialized (build 4.1.2)
```

### Configurar la asignación de Sling para la resolución de recursos {#configure-sling-mapping-for-resource-resolution}

Utilice la asignación de Sling para la resolución de recursos de modo que las URL basadas en dominio se resuelvan en el contenido de la instancia de publicación de AEM. La asignación de recursos traduce las direcciones URL entrantes de Dispatcher (originalmente desde solicitudes HTTP del cliente) a nodos de contenido.

Para obtener más información sobre la asignación de recursos de Sling, consulte [Asignaciones para la resolución de recursos](https://sling.apache.org/site/mappings-for-resource-resolution.html) en la documentación de Sling.

Normalmente, se requieren asignaciones para los siguientes recursos, aunque pueden ser necesarias otras asignaciones adicionales:

* El nodo raíz de la página de contenido (debajo de `/content`)
* El nodo de diseño que utilizan las páginas (debajo de `/etc/designs`)
* La carpeta `/libs`

Después de crear la asignación para la página de contenido, para descubrir asignaciones adicionales necesarias, utilice un explorador web para abrir una página en el servidor web. En el archivo error.log de la instancia de publicación, busque mensajes sobre los recursos que no se encuentran. El siguiente mensaje de ejemplo indica que se requiere una asignación para `/etc/clientlibs`:

```shell
01.11.2012 15:59:24.601 *INFO* [10.36.34.243 [1351799964599] GET /etc/clientlibs/foundation/jquery.js HTTP/1.1] org.apache.sling.engine.impl.SlingRequestProcessorImpl service: Resource /content/sitea/etc/clientlibs/foundation/jquery.js not found
```

>[!NOTE]
>
>El transformador linkchecker de la reescritura por defecto de Apache Sling modifica automáticamente los vínculos de la página para evitar enlaces rotos. Sin embargo, la reescritura de vínculos solo se realiza cuando su destino es un archivo HTML o HTM. Para actualizar vínculos a otros tipos de archivo, cree un componente transformador y agréguelo a una canalización de reescritura HTML.

### Nodos de asignación de recursos de ejemplo

En la siguiente tabla se enumeran los nodos que implementan la asignación de recursos para el dominio branda.com. Se crean nodos similares para el dominio `brandb.com`, como `/etc/map/http/brandb.com`. En todos los casos, las asignaciones son necesarias cuando las referencias en la página HTML no se resuelven correctamente en el contexto de Sling.

| Ruta del nodo | Tipo | Propiedad |
|--- |--- |--- |
| `/etc/map/http/branda.com` | sling:Mapping | Nombre: sling:internalRedirect Type: Valor de la cadena: /content/sitea |
| `/etc/map/http/branda.com/libs` | sling:Mapping | Nombre: sling:internalRedirect <br/>Tipo: Cadena <br/>Valor: /libs |
| `/etc/map/http/branda.com/etc` | sling:Mapping |  |
| `/etc/map/http/branda.com/etc/designs` | sling:Mapping | Nombre: sling:internalRedirect <br/>VType: Cadena <br/>VValor: /etc/designs |
| `/etc/map/http/branda.com/etc/clientlibs` | sling:Mapping | Nombre: sling:internalRedirect <br/>VType: Cadena <br/>VValor: /etc/clientlibs |

## Configurar el agente de replicación de vaciado de Dispatcher {#configuring-the-dispatcher-flush-replication-agent}

El agente de replicación de vaciado de Dispatcher en la instancia de publicación de AEM debe enviar solicitudes de invalidación a la granja de Dispatcher correcta. Para dirigirse a una granja, utilice la propiedad URI del agente de replicación de vaciado de Dispatcher (en la pestaña Transport). Incluya el valor de la propiedad `/virtualhost` para la granja de Dispatcher configurada para invalidar la caché:

`https://*webserver_name*:*port*/*virtual_host*/dispatcher/invalidate.cache`

Por ejemplo, para utilizar la granja `farm_flush` del ejemplo anterior, el URI es `https://localhost:80/invalidation_only/dispatcher/invalidate.cache`.

![](assets/chlimage_1-12.png)

## El servidor web reescribe las direcciones URL entrantes {#the-web-server-rewrites-incoming-urls}

Utilice la función de reescritura de URL interna del servidor web para traducir URL basadas en dominios a rutas de archivo en la caché de Dispatcher. Por ejemplo, las solicitudes de cliente para la página `https://brandA.com/en.html` se traducen al archivo `content/sitea/en.html` en la raíz del documento del servidor web.

![](assets/chlimage_1-13.png)

La caché de Dispatcher refleja la estructura de nodos del repositorio. Por lo tanto, cuando se producen activaciones de página, las solicitudes resultantes para invalidar la página en caché no requieren traducciones de dirección URL o de ruta.

![](assets/chlimage_1-14.png)

## Definir hosts virtuales y reescribir reglas en el servidor web {#define-virtual-hosts-and-rewrite-rules-on-the-web-server}

Configure los siguientes aspectos en el servidor web:

* Defina un host virtual para cada uno de sus dominios web.
* Para cada dominio, configure la raíz del documento para que coincida con la carpeta del repositorio que contiene el contenido web del dominio.
* Para cada dominio virtual, cree una regla de cambio de nombre de URL que traduzca la URL entrante a la ruta del archivo en caché.
* Cada dominio virtual también debe incluir configuraciones relacionadas con Dispatcher, como se describe en la página [Instalar Dispatcher](dispatcher-install.md).
* El módulo de Dispatcher debe estar configurado para utilizar la URL que haya reescrito el servidor web. (Consulte la `DispatcherUseProcessedURL` propiedad en [Instalar Dispatcher](dispatcher-install.md)).

El siguiente ejemplo de archivo httpd.conf configura dos hosts virtuales para un servidor web Apache:

* Los nombres de servidor (que coinciden con los nombres de dominio) son `brandA.com` (línea 16) y `brandB.com` (línea 32).

* La raíz del documento de cada dominio virtual es el directorio de la caché de Dispatcher que contiene las páginas del sitio. (líneas 20 y 33)
* La regla de reescritura de URL para cada dominio virtual es una expresión regular que prefija la ruta de la página solicitada con la ruta de las páginas en la caché. (líneas 19 y 35)
* La propiedad `DispatherUseProcessedURL` se establece en `1`. (línea 10)

Por ejemplo, el servidor web realiza las siguientes acciones cuando recibe una solicitud con la dirección URL `https://brandA.com/en/products.html`:

* Asocia la dirección URL con el host virtual que tiene un `ServerName` de `brandA.com.`
* Reescribe la dirección URL para que sea `/content/sitea/en/products.html.`
* Reenvía la URL a Dispatcher.

### httpd.conf {#httpd-conf-1}

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

### Configurar una granja de Dispatcher {#configure-a-dispatcher-farm}

Cuando el servidor web vuelve a escribir las direcciones URL, Dispatcher requiere una única granja definida de acuerdo con la [Configuración de Dispatcher](dispatcher-configuration.md). Las siguientes configuraciones son necesarias para admitir los hosts virtuales del servidor web y las reglas de cambio de nombre de URL:

* La propiedad `/virtualhosts` debe incluir los valores ServerName para todas las definiciones de VirtualHost.
* La propiedad `/statfileslevel` debe ser lo suficientemente alta como para crear archivos .stat en los directorios que contienen los archivos de contenido de cada dominio.

El siguiente archivo de configuración de ejemplo se basa en el archivo de ejemplo `dispatcher.any` que se instala con Dispatcher. Se requieren los siguientes cambios para admitir las configuraciones de servidor web del archivo `httpd.conf` anterior:

* La propiedad `/virtualhosts` hace que Dispatcher administre solicitudes para los dominios `brandA.com` y `brandB.com`. (línea 12)
* La propiedad `/statfileslevel` se establece en 2, de modo que los archivos .stat se crean en cada directorio que contiene el contenido web del dominio (línea 41): `/statfileslevel "2"`

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
>Dado que se define una única granja de Dispatcher, el agente de replicación de vaciado de Dispatcher de la instancia de publicación de AEM no requiere configuraciones especiales.

## Reescribir vínculos en archivos que no son HTML {#rewriting-links-to-non-html-files}

Para reescribir referencias a archivos que tengan extensiones distintas de .html o .htm, cree un componente transformador de reescritura de Sling y agréguelo a la canalización de reescritura predeterminada.

Reescriba referencias cuando las rutas de recursos no se resuelven correctamente en el contexto del servidor web. Por ejemplo, se necesita un transformador cuando los componentes que generan imágenes crean vínculos como /content/sitea/en/products.navimage.png. El componente de navegación superior del [Cómo crear un sitio web de Internet con todas las funciones](https://helpx.adobe.com/es/experience-manager/6-5/sites/developing/using/the-basics.html) crea estos vínculos.

El [reescritor Sling](https://sling.apache.org/documentation/bundles/output-rewriting-pipelines-org-apache-sling-rewriter.html) es un módulo que postprocesa la salida de Sling. Las implementaciones de canalización SAX del reescritor consisten en un generador, uno o más transformadores y un serializador:

* **Generador:** analiza el flujo de salida de Sling (documento HTML) y genera eventos SAX cuando encuentra tipos de elementos específicos.
* **Transformador:** escucha eventos SAX y, en consecuencia, modifica el objetivo del evento (un elemento HTML). Una canalización de reescritura contiene cero o más transformadores. Los transformadores se ejecutan en secuencia, pasando los eventos SAX al siguiente transformador de la secuencia.
* **Serializador:** serializa la salida, incluidas las modificaciones de cada transformador.

![](assets/chlimage_1-15.png)

### La canalización de reescritura por defecto de AEM {#the-aem-default-rewriter-pipeline}

AEM utiliza una reescritura de canalización por defecto que procesa documentos de tipo text/html:

* El generador analiza los documentos HTML y genera eventos SAX cuando encuentra elementos de cuerpo, base, vínculo, script y área. El alias del generador es `htmlparser`.
* La canalización incluye los siguientes transformadores: `linkchecker`, `mobile`, `mobiledebug`, `contentsync`. El transformador `linkchecker` externaliza las rutas a archivos HTML o HTM a los que se hace referencia para evitar que se rompan los vínculos.
* El serializador escribe la salida HTML. El alias del serializador es htmlwriter.

El nodo `/libs/cq/config/rewriter/default` define la canalización.

### Crear un transformador {#creating-a-transformer}

Realice las siguientes tareas para crear un componente transformador y utilizarlo en una canalización:

1. Implemente la interfaz `org.apache.sling.rewriter.TransformerFactory`. Esta clase crea instancias de su clase de transformador. Especifique valores para la propiedad `transformer.type` (el alias del transformador) y configure la clase como un componente de servicio OSGi.
1. Implemente la interfaz `org.apache.sling.rewriter.Transformer`. Para minimizar el trabajo, puede ampliar la clase `org.apache.cocoon.xml.sax.AbstractSAXPipe`. Anule el método startElement para personalizar el comportamiento de reescritura. Se llama a este método para cada evento SAX que se pasa al transformador.
1. Agrupe e implemente las clases.
1. Agregue un nodo de configuración a la aplicación de AEM para agregar el transformador a la canalización.

>[!TIP]
>En su lugar, puede configurar TransformerFactory para que el transformador se inserte en cada reescritura que se defina. Por lo tanto, no es necesario configurar una canalización:
>
>* Establezca la propiedad `pipeline.mode` en `global`.
>* Establezca la propiedad `service.ranking` en un número entero positivo.
>* No incluya una propiedad `pipeline.type`.


>[!NOTE]
>
>Utilice el arquetipo [multimodule](https://helpx.adobe.com/es/experience-manager/aem-previous-versions.html) del complemento Maven del paquete de contenido para crear su proyecto Maven. Los POM crean e instalan automáticamente un paquete de contenido.

Los siguientes ejemplos implementan un transformador que reescribe referencias a archivos de imagen.

* La clase MyRewriterTransformerFactory crea una instancia de los objetos MyRewriterTransformer. La propiedad pipeline.type establece el alias del transformador en mytransformer. Para incluir el alias en una canalización, el nodo de configuración de la canalización incluye este alias en la lista de transformadores.
* La clase MyRewriterTransformer anula el método startElement de la clase AbstractSAXTransformer. El método startElement reescribe el valor de los atributos src para los elementos img.

Los ejemplos no son sólidos y no deben utilizarse en un entorno de producción.

### Ejemplo de implementación de TransformerFactory {#example-transformerfactory-implementation}

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

### Ejemplo de implementación del transformador {#example-transformer-implementation}

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

### Añadir el transformador a una canalización de reescritura {#adding-the-transformer-to-a-rewriter-pipeline}

Cree un nodo JCR que defina una canalización que utilice su transformador. La siguiente definición de nodo creará una canalización que procese archivos de texto/html. Se utilizan el generador de AEM y el analizador predeterminados para HTML.

>[!NOTE]
>
>Si establece la propiedad Transformer `pipeline.mode` en `global`, no será necesario configurar una canalización. El modo `global` inserta el transformador en todas las canalizaciones.

### Nodo de configuración de reescritura: representación XML {#rewriter-configuration-node-xml-representation}

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

El siguiente gráfico muestra la representación del CRXDE Lite del nodo:

![](assets/chlimage_1-16.png)
