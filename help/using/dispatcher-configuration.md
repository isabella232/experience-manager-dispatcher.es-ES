---
title: Configurar Dispatcher
description: Aprenda a configurar Dispatcher. Obtenga información acerca de la compatibilidad con IPv4 e IPv6, archivos de configuración, variables de entorno, nombres de instancias, definición de granjas, identificación de hosts virtuales, etc.
exl-id: 91159de3-4ccb-43d3-899f-9806265ff132
source-git-commit: 51be516f90587ceda19180f13727c8372a794261
workflow-type: ht
source-wordcount: '0'
ht-degree: 100%

---

# Configurar Dispatcher {#configuring-dispatcher}

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher insertado en la documentación de una versión anterior de AEM.

Las siguientes secciones describen cómo configurar varios aspectos de Dispatcher.

## Compatibilidad con IPv4 e IPv6 {#support-for-ipv-and-ipv}

Todos los elementos de AEM y Dispatcher se pueden instalar en redes IPv4 e IPv6. Consulte [IPV4 e IPV6](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/introduction/technical-requirements.html?lang=es#ipv-and-ipv).

## Archivos de configuración de Dispatcher {#dispatcher-configuration-files}

De forma predeterminada, la configuración de Dispatcher se almacena en el archivo de texto `dispatcher.any`, aunque puede cambiar el nombre y la ubicación de este archivo durante la instalación.

El archivo de configuración contiene una serie de propiedades de un solo valor o de varios valores que controlan el comportamiento de Dispatcher:

* Los nombres de las propiedades llevan como prefijo una barra diagonal `/`.
* Las propiedades de varios valores encierran elementos secundarios utilizando llaves `{ }`.

Un ejemplo de configuración se estructura de la siguiente manera:

```xml
# name of the dispatcher
/name "internet-server"

# each farm configures a set off (loadbalanced) renders
/farms
 {
  # first farm entry (label is not important, just for your convenience)
   /website
     {  
     /clientheaders
       {
       # List of headers that are passed on
       }
     /virtualhosts
       {
       # List of URLs for this Web site
       }
     /sessionmanagement
       {
       # settings for user authentification
       }
     /renders
       {
       # List of AEM instances that render the documents
       }
     /filter
       {
       # List of filters
       }
     /vanity_urls
       {
       # List of vanity URLs
       }
     /cache
       {
       # Cache configuration
       /rules
         {
         # List of cachable documents
         }
       /invalidate
         {
         # List of auto-invalidated documents
         }
       }
     /statistics
       {
       /categories
         {
         # The document categories that are used for load balancing estimates
         }
       }
     /stickyConnectionsFor "/myFolder"
     /health_check
       {
       # Page gets contacted when an instance returns a 500
       }
     /retryDelay "1"
     /numberOfRetries "5"
     /unavailablePenalty "1"
     /failover "1"
     }
 }
```

Puede incluir otros archivos que contribuyan a la configuración:

* Si el archivo de configuración es grande, puede dividirlo en varios archivos más pequeños (que sean más fáciles de administrar) e incluirlos.
* Incluir archivos que se generan automáticamente.

Por ejemplo, para incluir el archivo myFarm.any en la configuración /farms, utilice el siguiente código:

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Utilice el asterisco (`*`) como comodín para especificar el rango de archivos que desea incluir.

Por ejemplo, si los archivos `farm_1.any` hasta `farm_5.any` contienen la configuración de granjas de uno a cinco, puede incluirlos de la siguiente manera:

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Utilizar variables de entorno {#using-environment-variables}

Puede utilizar variables de entorno en propiedades con valor en cadena en el archivo dispatcher.any en lugar de programar los valores. Para incluir el valor de una variable de entorno, utilice el formato `${variable_name}`.

Por ejemplo, si el archivo dispatcher.any se encuentra en el mismo directorio que el directorio de caché, se puede utilizar el siguiente valor para la propiedad [docroot](#specifying-the-cache-directory):

```xml
/docroot "${PWD}/cache"
```

Otro ejemplo: si crea una variable de entorno denominada `PUBLISH_IP` que almacena el nombre de host de la instancia de publicación de AEM, se puede utilizar la siguiente configuración de la propiedad [/renders](#defining-page-renderers-renders):

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Nombrar la instancia de Dispatcher {#naming-the-dispatcher-instance-name}

Utilice la propiedad `/name` para especificar un nombre único para identificar la instancia de Dispatcher. La propiedad `/name` es una propiedad de nivel superior de la estructura de configuración.

## Definir granjas {#defining-farms-farms}

La propiedad `/farms` define uno o más conjuntos de comportamientos de Dispatcher, donde cada conjunto se asocia con diferentes sitios web o direcciones URL. La propiedad `/farms` puede incluir una o varias granjas:

* Utilice una única granja de servidores cuando desee que Dispatcher administre todas las páginas o sitios web del mismo modo.
* Cree varias granjas cuando diferentes áreas del sitio web o sitios web diferentes requieran un comportamiento de Dispatcher diferente.

La propiedad `/farms` es una propiedad de nivel superior de la estructura de configuración. Para definir una granja, agregue una propiedad secundaria a la propiedad `/farms`. Utilice un nombre de propiedad que identifique de forma exclusiva la granja en la instancia de Dispatcher.

La propiedad `/farmname` tiene varios valores y contiene otras propiedades que definen el comportamiento de Dispatcher:

* Direcciones URL de las páginas a las que se aplica la granja.
* Una o más direcciones URL de servicio (normalmente de instancias de publicaciones de AEM) que se utilizan para procesar documentos.
* Las estadísticas que se utilizarán para equilibrar la carga de varios procesadores de documentos.
* Otros comportamientos, como qué archivos almacenar en caché y dónde.

El valor puede incluir cualquier carácter alfanumérico (a-z, 0-9). El siguiente ejemplo muestra la definición del esqueleto de dos granjas denominadas `/daycom` y `/docsdaycom`:

```xml
#name of dispatcher
/name "day sites"

#farms section defines a list of farms or sites
/farms
{
   /daycom
   {
       ...
   }
   /docdaycom
   {
      ...
   }
}
```

>[!NOTE]
>
>Si utiliza más de una granja de servidores de procesamiento, la lista se evaluará de abajo hacia arriba. Esto es especialmente relevante a la hora de definir [Hosts virtuales](#identifying-virtual-hosts-virtualhosts) para sus sitios web.

Cada propiedad de granja puede contener las siguientes propiedades secundarias:

| Nombre de la propiedad | Descripción |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | Página principal predeterminada (opcional) (solo IIS) |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | Encabezados de la solicitud HTTP del cliente para pasar.los |
| [/virtualhosts](#identifying-virtual-hosts-virtualhosts) | Los hosts virtuales de esta granja. |
| [/sessionmanagement](#enabling-secure-sessions-sessionmanagement) | Compatibilidad con la administración y la autenticación de sesiones. |
| [/renders](#defining-page-renderers-renders) | Servidores que ofrecen páginas procesadas (normalmente instancias de publicación de AEM). |
| [/filter](#configuring-access-to-content-filter) | Define las direcciones URL a las que Dispatcher habilita el acceso. |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | Configura el acceso a las URL de vanidad. |
| [/propagateSyndPost](#forwarding-syndication-requests-propagatesyndpost) | Compatibilidad con el reenvío de solicitudes de distribución. |
| [/cache](#configuring-the-dispatcher-cache-cache) | Configura el comportamiento del almacenamiento en caché. |
| [/statistics](#configuring-load-balancing-statistics) | Definir categorías estadísticas para cálculos de equilibrio de carga. |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-stickyconnectionsfor) | Carpeta que contiene documentos duraderos. |
| [/health_check](#specifying-a-health-check-page) | URL que se utiliza para determinar la disponibilidad del servidor. |
| [/retryDelay](#specifying-the-page-retry-delay) | Retraso antes de reintentar una conexión fallida. |
| [/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | Sanciones que afectan a las estadísticas de los cálculos del equilibrio de carga. |
| [/failover](#using-the-failover-mechanism) | Reenviar solicitudes a diferentes procesamientos cuando falle la solicitud original. |
| [/auth_checker](permissions-cache.md) | Para obtener información sobre el almacenamiento en caché que distingue entre permisos, consulte [Almacenamiento en caché de contenido seguro](permissions-cache.md). |

## Especificar una página predeterminada (solo IIS) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>El parámetro `/homepage` (solo IIS) ya no funciona. En su lugar, debe utilizar el [Módulo de reescritura de URL de IIS](https://docs.microsoft.com/es-es/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>Si utiliza Apache, debe utilizar el módulo `mod_rewrite`. Consulte la documentación del sitio web Apache para obtener información sobre `mod_rewrite` (por ejemplo, [Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). Al utilizar `mod_rewrite`, se recomienda utilizar el indicador **[&#39;passthrough|PT&#39; (pasar al siguiente controlador)](https://helpx.adobe.com/dispatcher/kb/DispatcherModReWrite.html)** para forzar al motor de reescritura a establecer el campo `uri` de la estructura interna `request_rec` en el valor del campo `filename`.

<!-- 

Comment Type: draft

<p>The optional /homepage parameter specifies the page that Dispatcher returns when a client requests an undeterminable page or file.</p> 
<p>Typically this situation occurs when a user specifies an URL for which neither IIS or AEM provides an automatic redirection target. For example, if the AEM render instance is shut down after the content is cached, the content redirect URL is unavailable.</p> 
<p>The following example configuration displays the <span class="code">index.html</span> page in such circumstances:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /homepage&nbsp;"/index.html" 
</codeblock>

 -->

<!-- 

Comment Type: draft

<p>The <span class="code">/homepage</span> section is located inside the <span class="code">/farms</span> section, for example:<br /> </p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  #name&nbsp;of&nbsp;dispatcher!!discoiqbr!!/name&nbsp;"day&nbsp;sites"!!discoiqbr!!!!discoiqbr!!#farms&nbsp;section&nbsp;defines&nbsp;a&nbsp;list&nbsp;of&nbsp;farms&nbsp;or&nbsp;sites!!discoiqbr!!/farms!!discoiqbr!!{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/daycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/homepage&nbsp;"/index.html"!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!&nbsp;&nbsp;&nbsp;/docdaycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!} 
</codeblock>

 -->

## Especificar los encabezados HTTP para pasarlos {#specifying-the-http-headers-to-pass-through-clientheaders}

La propiedad `/clientheaders` define una lista de encabezados HTTP que Dispatcher pasa de la solicitud HTTP del cliente al procesador (instancia de AEM).

De forma predeterminada, Dispatcher reenvía los encabezados HTTP estándar a la instancia de AEM. En algunos casos, puede que desee reenviar encabezados adicionales o eliminar encabezados específicos:

* Añada encabezados, como encabezados personalizados, que la instancia de AEM espera en la solicitud HTTP.
* Elimine los encabezados, como los de autenticación, que solo sean relevantes para el servidor web.

Si personaliza el conjunto de encabezados para pasarlos, debe especificar una lista exhaustiva de encabezados, incluidos los que normalmente se incluyen de forma predeterminada.

Por ejemplo, una instancia de Dispatcher que administra las solicitudes de activación de página para instancias de publicación requiere el encabezado `PATH` en la sección `/clientheaders`. El encabezado `PATH` permite la comunicación entre el agente de replicación y Dispatcher.

El siguiente código es un ejemplo de configuración para `/clientheaders`:

```shell
/clientheaders
  {
  "CSRF-Token"
  "X-Forwarded-Proto"
  "referer"
  "user-agent"
  "authorization"
  "from"
  "content-type"
  "content-length"
  "accept-charset"
  "accept-encoding"
  "accept-language"
  "accept"
  "host"
  "max-forwards"
  "proxy-authorization"
  "proxy-connection"
  "range"
  "cookie"
  "cq-action"
  "cq-handle"
  "handle"
  "action"
  "cqstats"
  "depth"
  "translate"
  "expires"
  "date"
  "dav"
  "ms-author-via"
  "if"
  "lock-token"
  "x-expected-entity-length"
  "destination"
  "PATH"
  }
```

## Identificar hosts virtuales {#identifying-virtual-hosts-virtualhosts}

La propiedad `/virtualhosts` define una lista de todas las combinaciones de nombre de host/URI que Dispatcher acepta para esta granja. Puede utilizar el asterisco (`*`) como comodín. Los valores de la propiedad / `virtualhosts` utilizan el siguiente formato:

```xml
[scheme]host[uri][*]
```

* `scheme`: (Opcional) O `https://` o bien `https://.`
* `host`: El nombre o la dirección IP del equipo host y el número de puerto si es necesario. (Consulte [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23))
* `uri`: (Opcional) La ruta a los recursos.

La siguiente configuración de ejemplo administra las solicitudes de los dominios .com y .ch de myCompany, así como todos los dominios de mySubDivision:

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

La siguiente configuración administra *todas las solicitudes*:

```xml
   /virtualhosts
    {
    "*"
    }
```

### Resolver el host virtual {#resolving-the-virtual-host}

Cuando Dispatcher recibe una solicitud HTTP o HTTPS, encuentra el valor del host virtual que mejor coincide con los encabezados `host,` `uri` y `scheme` de la solicitud. Dispatcher evalúa los valores en las propiedades `virtualhosts` en el siguiente orden:

* Dispatcher comienza en la granja más baja y va subiendo en el archivo dispatcher.any.
* Para cada granja, Dispatcher comienza con el valor superior de la propiedad `virtualhosts` y va bajando en la lista de valores.

Dispatcher encuentra el valor de host virtual que mejor se corresponde con él de la siguiente manera:

* Se utiliza el host virtual que se encuentra por primera vez y que coincide con los tres, el `host`, el `scheme` y el `uri` de la solicitud.
* Si ningún valor `virtualhosts` tiene `scheme` y `uri` partes que coincidan con `scheme` y `uri` de la solicitud, se utilizará el host virtual que se encuentre por primera vez y que coincida con el `host` de la solicitud.
* Si ningún valor `virtualhosts` tiene una parte de host que coincida con el host de la solicitud, se utiliza el host virtual superior de la granja superior.

Por lo tanto, debe colocar el host virtual predeterminado en la parte superior de la propiedad `virtualhosts` en la granja superior del archivo `dispatcher.any`.

### Ejemplo de resolución de host virtual {#example-virtual-host-resolution}

El siguiente ejemplo representa un fragmento de un archivo `dispatcher.any` que define dos granjas de Dispatcher y cada granja define una propiedad `virtualhosts`.

```xml
/farms
  {
  /myProducts
    {
    /virtualhosts
      {
      "www.mycompany.com"
      }
    /renders
      {
      /hostname "server1.myCompany.com"
      /port "80"
      }
    }
  /myCompany
    {
    /virtualhosts
      {
      "www.mycompany.com/products/*"
      }
    /renders
      {
      /hostname "server2.myCompany.com"
      /port "80"
      }
    }
  }
```

En este ejemplo, la siguiente tabla muestra los hosts virtuales que se resuelven para las solicitudes HTTP dadas:

| URL de solicitud | Host virtual resuelto |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## Habilitar sesiones seguras - /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized` **debe** configurarse como `"0"` en la `/cache` sección para habilitar esta funcionalidad. Tal y como se detalla en la sección [Almacenamiento en caché cuando se utiliza la autenticación](#caching-when-authentication-is-used), cuando configure `/allowAuthorized 0 `, las solicitudes que incluyen información de autenticación **no** se guardan en caché. Si se requiere un almacenamiento en caché que distinga los permisos, consulte la página [Almacenamiento en caché de contenido seguro](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/permissions-cache.html?lang=es).

Cree una sesión segura para acceder a la granja de procesamiento, de modo que los usuarios tengan que iniciar sesión para acceder a cualquier página de la granja. Tras iniciar sesión, los usuarios pueden acceder a las páginas de la granja. Consulte [Creación de un grupo de usuarios cerrado](https://experienceleague.adobe.com/docs/experience-manager-65/administering/security/cug.html?lang=es#creating-the-user-group-to-be-used) para obtener información sobre el uso de esta función con CUG. Además, consulte la [Lista de comprobación de seguridad](/help/using/security-checklist.md) de Dispatcher antes de empezar.

La propiedad `/sessionmanagement` es una subpropiedad de `/farms`.

>[!CAUTION]
>
>Si las secciones del sitio web utilizan diferentes requisitos de acceso, debe definir varias granjas.

**/sessionmanagement** tiene varios subparámetros:

**/directory** (obligatorio)

El directorio que almacena la información de la sesión. Si el directorio no existe, se crea.

>[!CAUTION]
>
> Al configurar el subparámetro de directorio **no** señale la carpeta raíz (`/directory "/"`) ya que puede causar problemas graves. Siempre debe especificar la ruta a la carpeta que almacena la información de la sesión. Por ejemplo:

```xml
/sessionmanagement
  {
  /directory "/usr/local/apache/.sessions"
  }
```

**/encode** (opcional)

Cómo se cifra la información de la sesión. Utilice `md5` para el cifrado con el algoritmo md5 o `hex` para la codificación hexadecimal. Si cifra los datos de sesión, los usuarios con acceso al sistema de archivos no podrán leer el contenido de la sesión. El valor predeterminado es `md5`.

**/header** (opcional)

El nombre del encabezado HTTP o la cookie que almacena la información de autorización. Si almacena la información en el encabezado http, utilice `HTTP:<header-name>`. Para almacenar la información en una cookie, utilice `Cookie:<header-name>`. Si no especifica ningún valor se utilizará `HTTP:authorization`.

**/timeout** (opcional)

El número de segundos hasta que se agota el tiempo de espera de la sesión una vez que se ha utilizado por última vez. Si no se utiliza el `"800"` especificado, la sesión se cerrará poco más de 13 minutos después de la última petición del usuario.

Un ejemplo de configuración sería de la siguiente manera:

```xml
/sessionmanagement
  {
  /directory "/usr/local/apache/.sessions"
  /encode "md5"
  /header "HTTP:authorization"
  /timeout "800"
  }
```

## Definir procesadores de página {#defining-page-renderers-renders}

La propiedad /renders define la URL a la que Dispatcher envía solicitudes para procesar un documento. La siguiente sección de ejemplo `/renders` identifica una instancia de AEM única para el procesamiento:

```xml
/renders
  {
    /myRenderer
      {
      # hostname or IP of the renderer
      /hostname "aem.myCompany.com"
      # port of the renderer
      /port "4503"
      # connection timeout in milliseconds, "0" (default) waits indefinitely
      /timeout "0"
      }
  }
```

La siguiente sección de ejemplo /renders identifica una instancia de AEM que se ejecuta en el mismo equipo que Dispatcher:

```xml
/renders
  {
    /myRenderer
     {
     /hostname "127.0.0.1"
     /port "4503"
     }
  }
```

La siguiente sección de ejemplo /renders distribuye las solicitudes de procesamiento de forma equitativa entre dos instancias de AEM:

```xml
/renders
  {
    /myFirstRenderer
      {
      /hostname "aem.myCompany.com"
      /port "4503"
      }
    /mySecondRenderer
      {
      /hostname "127.0.0.1"
      /port "4503"
      }
  }
```

### Opciones de procesamiento {#renders-options}

**/timeout**

Especifica el tiempo de espera de conexión que accede a la instancia de AEM en milisegundos. El valor predeterminado es `"0"`, lo que hace que Dispatcher espere indefinidamente.

**/receiveTimeout**

Especifica el tiempo en milisegundos que puede tardar una respuesta. El valor predeterminado es `"600000"`, lo que hace que Dispatcher espere 10 minutos. Una configuración de `"0"` elimina por completo el tiempo de espera.

Si se alcanza el tiempo de espera al analizar los encabezados de respuesta, se devuelve un estado HTTP 504 (puerta de enlace incorrecta). Si se alcanza el tiempo de espera mientras se lee el cuerpo de respuesta, Dispatcher devolverá la respuesta incompleta al cliente, pero eliminará cualquier archivo de caché que se haya escrito.

**/ipv4**

Especifica si Dispatcher utiliza la función `getaddrinfo` (para IPv6) o la función `gethostbyname` (para IPv4) para obtener la dirección IP del procesamiento. Un valor de 0 hace que se utilice `getaddrinfo`. Un valor de `1` hace que se utilice `gethostbyname`. El valor predeterminado es `0`.

La función `getaddrinfo` devuelve una lista de direcciones IP. Dispatcher repite la lista de direcciones hasta que establece una conexión TCP/IP. Por tanto, la propiedad `ipv4` es importante cuando el nombre de host de procesamiento está asociado con varias direcciones IP y el host, en respuesta a la función `getaddrinfo`, devuelve una lista de direcciones IP que siempre están en el mismo orden. En este caso, debe utilizar la función `gethostbyname` para que la dirección IP con la que se conecta Dispatcher se asigne al azar.

El Elastic Load Balancing de Amazon (ELB) es un servicio de este tipo que responde a getaddrinfo con una lista ordenada de direcciones IP.

**/secure**

Si la propiedad `/secure` tiene un valor de `"1"` Dispatcher utiliza HTTPS para comunicarse con la instancia de AEM. Para obtener más información, consulte también [Configuración de Dispatcher para usar SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

Con la versión de Dispatcher **4.1.6**, puede configurar la propiedad `/always-resolve` de la siguiente manera:

* Cuando se establece en `"1"`, resuelve el nombre de host en cada solicitud (Dispatcher nunca almacenará en caché ninguna dirección IP). Puede afectar ligeramente al rendimiento debido a la llamada adicional necesaria para obtener la información del host de cada solicitud.
* Si la propiedad no está establecida, la dirección IP se almacenará en caché de forma predeterminada.

Además, esta propiedad se puede utilizar en caso de que encuentre problemas de resolución de IP dinámica, como se muestra en el siguiente ejemplo:

```xml
/renders {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## Configurar el acceso al contenido {#configuring-access-to-content-filter}

Utilice la sección `/filter` para especificar las solicitudes HTTP que acepta Dispatcher. El resto de solicitudes se envían de nuevo al servidor web con un código de error 404 (página no encontrada). Si no existe ninguna sección `/filter`, se aceptan todas las solicitudes.

**Nota:** Las solicitudes del [archivo de estado](#naming-the-statfile) siempre se rechazan.

>[!CAUTION]
>
>Consulte la [Lista de comprobación de seguridad de Dispatcher](security-checklist.md) para obtener más información al restringir el acceso con Dispatcher. Además, lea la [Lista de comprobación de seguridad de AEM](https://experienceleague.adobe.com/docs/experience-manager-65/administering/security/security-checklist.html?lang=es#security) para obtener más información sobre la seguridad relacionada con su instalación de AEM.

La sección `/filter` consta de una serie de reglas que deniegan o permiten el acceso al contenido según los patrones de la parte de línea de solicitud de la solicitud HTTP. Debe utilizar una estrategia con lista de permitidos para la sección `/filter`:

* Primero, deniegue el acceso a todo.
* Permita el acceso al contenido según sea necesario.

>[!NOTE]
>
>Se recomienda purgar la caché siempre que haya algún cambio en las reglas de filtro.

### Definir un filtro {#defining-a-filter}

Cada elemento de la sección `/filter` incluye un tipo y un patrón que coinciden con un elemento específico o con toda la línea de solicitud. Cada filtro puede contener los siguientes elementos:

* **Tipo**: El `/type` indica si se permite o deniega el acceso a las solicitudes que coinciden con el patrón. El valor puede ser `allow` o `deny`.

* **Elemento de la línea de solicitud:** incluye `/method`, `/url`, `/query` o `/protocol` y un patrón para filtrar solicitudes según estas partes específicas de la parte de línea de solicitud de la solicitud HTTP. El método de filtrado consiste en filtrar los elementos de la línea de solicitud (en lugar de toda la línea de solicitud).

* **Elementos avanzados de la línea de solicitud:** a partir de Dispatcher 4.2.0, hay cuatro elementos de filtro nuevos disponibles. Estos elementos nuevos son `/path`, `/selectors`, `/extension` y `/suffix` respectivamente. Incluya uno o más de ellos para controlar aún más los patrones de URL.

>[!NOTE]
>
>Para obtener más información sobre qué parte de la línea de solicitud hace referencia cada uno de estos elementos, consulte la página de wiki [Sling URL Decomposition](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html).

* **Propiedad glob**: La propiedad `/glob` se utiliza para coincidir con toda la línea de solicitud de la solicitud HTTP.

>[!CAUTION]
>
>El filtrado con globs está obsoleto en Dispatcher. Como tal, debe evitar utilizar globs en las secciones `/filter`, ya que puede provocar problemas de seguridad. Por lo tanto, en lugar de:
>
>`/glob "* *.css *"`
>
>debe usar
>
>`/url "*.css"`

#### La parte de línea de solicitud de solicitudes HTTP {#the-request-line-part-of-http-requests}

HTTP/1.1 define la [línea de solicitud](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) de la siguiente manera:

`Method Request-URI HTTP-Version<CRLF>`

Los caracteres `<CRLF>` representan un retorno de carro seguido de una línea nueva. El siguiente ejemplo es la línea de solicitud que se recibe cuando un cliente solicita la página americana del sitio WKND:

`GET /content/wknd/us/en.html HTTP.1.1<CRLF>`

Los patrones deben tener en cuenta los caracteres de espacio de la línea de solicitud y los caracteres `<CRLF>`.

#### Comillas dobles versus comillas simples {#double-quotes-vs-single-quotes}

Al crear las reglas de filtro, utilice comillas dobles `"pattern"` para patrones simples. Si utiliza la versión de Dispatcher 4.2.0 o posterior y el patrón incluye una expresión regular, debe incluir el patrón regex `'(pattern1|pattern2)'` entre comillas simples.

#### Expresiones regulares {#regular-expressions}

En las versiones de Dispatcher posteriores a la 4.2.0, puede incluir expresiones POSIX regulares extendidas en los patrones de filtro.

#### Solucionar problemas de filtros {#troubleshooting-filters}

Si los filtros no se activan del modo esperado, habilite [Registro de rastros](#trace-logging) en Dispatcher para que pueda ver qué filtro está interceptando la solicitud.

#### Filtro de ejemplo: Denegar todo {#example-filter-deny-all}

Este filtro de ejemplo hace que Dispatcher rechace las solicitudes de todos los archivos. Debe denegar el acceso a todos los archivos y permitir el acceso a áreas específicas.

```xml
/0001  { /type "deny" /url "*"  }
```

Las solicitudes a un área denegada explícitamente dan como resultado la devolución de un código de error 404 (página no encontrada).

#### Filtro de ejemplo: Denegar el acceso a áreas específicas {#example-filter-deny-access-to-specific-areas}

Los filtros también permiten denegar el acceso a varios elementos, por ejemplo, a páginas ASP y a áreas confidenciales dentro de una instancia de publicación. El siguiente filtro deniega el acceso a páginas ASP:

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Filtro de ejemplo: Habilitar solicitudes POST {#example-filter-enable-post-requests}

El siguiente filtro de ejemplo permite enviar datos de formulario mediante el método POST:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Filtro de ejemplo: Permitir el acceso a la consola de flujo de trabajo {#example-filter-allow-access-to-the-workflow-console}

El siguiente ejemplo muestra un filtro utilizado para denegar el acceso externo a la consola de flujo de trabajo:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

Si la instancia de publicación utiliza un contexto de aplicación web (por ejemplo, publicar), también se puede añadir a la definición del filtro.

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

Si todavía necesita acceder a páginas individuales dentro del área restringida, puede permitir el acceso a ellas. Por ejemplo, para permitir el acceso a la pestaña Archivo dentro de la consola de flujo de trabajo, agregue la siguiente sección:

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>Cuando se aplican varios patrones de filtros a una solicitud, el último que se aplica es efectivo.

#### Filtro de ejemplo: Uso de expresiones regulares {#example-filter-using-regular-expressions}

Este filtro habilita las extensiones en directorios de contenido no públicos mediante una expresión regular, definida aquí entre comillas simples:

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Filtro de ejemplo: Filtrar elementos adicionales de una URL de solicitud {#example-filter-filter-additional-elements-of-a-request-url}

A continuación se muestra un ejemplo de regla que bloquea la captura de contenido desde la ruta `/content` y su subárbol, utilizando filtros para la ruta, selectores y extensiones:

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy|sysview|docview|query|jcr:content|_jcr_content|search|childrenlist|ext|assets|assetsearch|[0-9-]+)'
        /extension '(json|xml|html|feed))'
        }
```

### Ejemplo de sección /filter {#example-filter-section}

Al configurar Dispatcher, debe restringir el acceso externo en la medida de lo posible. El siguiente ejemplo ofrece un acceso mínimo a los visitantes externos:

* `/content`
* contenido variado, como diseños y bibliotecas de cliente; por ejemplo:

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

Después de crear los filtros, [pruebe el acceso a la página](#testing-dispatcher-security) para asegurarse de que la instancia de AEM sea segura.

La siguiente sección `/filter` del archivo `dispatcher.any` puede utilizarse como base en el archivo de configuración de [Dispatcher.](#dispatcher-configuration-files)

Este ejemplo se basa en el archivo de configuración por defecto que se ofrece con Dispatcher y está diseñado como ejemplo para utilizarlo en un entorno de producción. Los elementos con el prefijo `#` están desactivados (comentados), debe tener cuidado si decide activar alguno de estos elementos (eliminando el `#` en esa línea), ya que podría afectar a la seguridad.

Debe denegar el acceso a todo y permitirlo a elementos específicos (limitados):

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:32:37.986-0400

<p>We should mention the config files that are shipped with the dispatcher distribution and only give a few examples here. This aims to avoid confusion and reduce content maintenance.<br /> </p>

 -->

```xml
  /filter
      {
      # Deny everything first and then allow specific entries
      /0001  { /type "deny" /url "*"  }

      # Open consoles
#     /0011 { /type "allow" /url "/admin/*"  }  # allow servlet engine admin
#     /0012 { /type "allow" /url "/crx/*"    }  # allow content repository
#     /0013 { /type "allow" /url "/system/*" }  # allow OSGi console

      # Allow non-public content directories
#     /0021 { /type "allow" /url "/apps/*"   }  # allow apps access
#     /0022 { /type "allow" /url "/bin/*"    }
      /0023 { /type "allow" /url "/content*" }  # disable this rule to allow mapped content only

#     /0024 { /type "allow" /url "/libs/*"   }
#     /0025 { /type "deny"  /url "/libs/shindig/proxy*" } # if you enable /libs close access to proxy

#     /0026 { /type "allow" /url "/home/*"   }
#     /0027 { /type "allow" /url "/tmp/*"    }
#     /0028 { /type "allow" /url "/var/*"    }

      # Enable extensions in non-public content directories, using a regular expression
      /0041
        {
        /type "allow"
        /extension '(css|gif|ico|js|png|swf|jpe?g)'
        }

      # Enable features
      /0062 { /type "allow" /url "/libs/cq/personalization/*"  }  # enable personalization

      # Deny content grabbing, on all accessible pages, using regular expressions
      /0081
        {
        /type "deny"
        /selectors '((sys|doc)view|query|[0-9-]+)'
        /extension '(json|xml)'
        }
      # Deny content grabbing for /content and its subtree
      /0082
        {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }

#     /0087 { /type "allow" /method "GET" /extension 'json' "*.1.json" }  # allow one-level json requests
}
```

>[!NOTE]
>
>Cuando se utilice con Apache, diseñe los patrones de URL del filtro según la propiedad DispatcherUseProcessedURL del módulo Dispatcher. (Consulte [Servidor web Apache: Configurar el servidor web Apache para Dispatcher](dispatcher-install.md##apache-web-server-configure-apache-web-server-for-dispatcher)).

<!----
>[!NOTE]
>
>Filters `0030` and `0031` regarding Dynamic Media are applicable to AEM 6.0 and higher. -->

Tenga en cuenta las siguientes recomendaciones si decide ampliar el acceso:

* El acceso externo a `/admin` siempre debe estar *completamente* deshabilitado si utiliza la versión 5.4 de CQ o una versión anterior.

* Debe tener cuidado al permitir acceso a archivos en `/libs`. El acceso debe permitirse de forma individual.
* Deniegue el acceso a la configuración de replicación para que no se vea:

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Deniegue el acceso al proxy inverso de Google Gadgets:

   * `/libs/opensocial/proxy*`

Dependiendo de la instalación, puede haber recursos adicionales en `/libs`, `/apps` o en otra parte, que deben estar disponibles. Puede utilizar el archivo `access.log` como un método para determinar los recursos a los que se accede externamente.

>[!CAUTION]
>
>El acceso a consolas y directorios puede implicar un riesgo de seguridad para los entornos de producción. A menos que tenga justificaciones explícitas, deben permanecer desactivadas (comentadas).

>[!CAUTION]
>
>Si está [utilizando informes en un entorno de publicación](https://experienceleague.adobe.com/docs/experience-manager-65/administering/operations/reporting.html?lang=es#using-reports-in-a-publish-environment) debe configurar Dispatcher para que deniegue el acceso a `/etc/reports` a los visitantes externos.

### Restringir cadenas de consulta {#restricting-query-strings}

Desde la versión 4.1.5 de Dispatcher, utilice la sección `/filter` para restringir las cadenas de consulta. Se recomienda encarecidamente permitir explícitamente cadenas de consulta y excluir la asignación genérica a través de elementos de filtro `allow`.

Una sola entrada puede tener `glob` o alguna combinación de `method`, `url`, `query` y `version`, pero no ambas. El siguiente ejemplo permite la cadena de consulta `a=*` y deniega todas las demás de las direcciones URL que se dirigen al nodo `/etc`:

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>Si una regla contiene `/query`, solo coincidirá con las solicitudes que contienen una cadena de consulta y coincidirán con el patrón de consulta proporcionado.
>
>En el ejemplo anterior, si las solicitudes a `/etc` que no tienen una cadena de consulta deben ser permitidas también, se requerirían las siguientes reglas:

```xml
/filter {  
>/0001 { /type "deny" /method “*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type “deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Prueba de seguridad de Dispatcher {#testing-dispatcher-security}

Los filtros de Dispatcher deben bloquear el acceso a las siguientes páginas y secuencias de comandos en las instancias de publicación de AEM. Use un explorador web para intentar abrir las siguientes páginas como lo haría un visitante del sitio y verificar que devuelve un código 404. Si obtiene cualquier otro resultado, ajuste los filtros.

Tenga en cuenta que debería ver el procesamiento normal de la página para `/content/add_valid_page.html?debug=layout`.

* `/admin`
* `/system/console`
* `/dav/crx.default`
* `/crx`
* `/bin/crxde/logs`
* `/jcr:system/jcr:versionStorage.json`
* `/_jcr_system/_jcr_versionStorage.json`
* `/libs/wcm/core/content/siteadmin.html`
* `/libs/collab/core/content/admin.html`
* `/libs/cq/ui/content/dumplibs.html`
* `/var/linkchecker.html`
* `/etc/linkchecker.html`
* `/home/users/a/admin/profile.json`
* `/home/users/a/admin/profile.xml`
* `/libs/cq/core/content/login.json`
* `/content/../libs/foundation/components/text/text.jsp`
* `/content/.{.}/libs/foundation/components/text/text.jsp`
* `/apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata`
* `/libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet`
* `/content.pages.json`
* `/content.languages.json`
* `/content.blueprint.json`
* `/content.-1.json`
* `/content.10.json`
* `/content.infinity.json`
* `/content.tidy.json`
* `/content.tidy.-1.blubber.json`
* `/content/dam.tidy.-100.json`
* `/content/content/geometrixx.sitemap.txt `
* `/content/add_valid_page.query.json?statement=//*`
* `/content/add_valid_page.qu%65ry.js%6Fn?statement=//*`
* `/content/add_valid_page.query.json?statement=//*[@transportPassword]/(@transportPassword%20|%20@transportUri%20|%20@transportUser)`
* `/content/add_valid_path_to_a_page/_jcr_content.json`
* `/content/add_valid_path_to_a_page/jcr:content.json`
* `/content/add_valid_path_to_a_page/_jcr_content.feed`
* `/content/add_valid_path_to_a_page/jcr:content.feed`
* `/content/add_valid_path_to_a_page/pagename._jcr_content.feed`
* `/content/add_valid_path_to_a_page/pagename.jcr:content.feed`
* `/content/add_valid_path_to_a_page/pagename.docview.xml`
* `/content/add_valid_path_to_a_page/pagename.docview.json`
* `/content/add_valid_path_to_a_page/pagename.sysview.xml`
* `/etc.xml`
* `/content.feed.xml`
* `/content.rss.xml`
* `/content.feed.html`
* `/content/add_valid_page.html?debug=layout`
* `/projects`
* `/tagging`
* `/etc/replication.html`
* `/etc/cloudservices.html`
* `/welcome`

Ejecute el siguiente comando en un terminal o símbolo del sistema para determinar si el acceso de escritura anónimo está habilitado. No debería poder escribir datos en el nodo.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Ejecute el siguiente comando en un terminal o símbolo del sistema para intentar invalidar la caché de Dispatcher y para asegurarse de recibir una respuesta de código 403:

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Habilitar el acceso a las URL de vanidad {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The "com.adobe.granite.dispatcher.vanityurl.content" package needs to be made public before publishing this contnet.</p>
 -->

Configure Dispatcher para habilitar el acceso a las URL de vanidad configuradas para sus páginas de AEM.

Cuando se habilita el acceso a las URL de vanidad, Dispatcher llama periódicamente a un servicio que se ejecuta en la instancia de procesamiento para obtener una lista de URL de vanidad. Dispatcher almacena esta lista en un archivo local. Cuando se deniega una solicitud de una página debido a un filtro en la sección `/filter`, Dispatcher consulta la lista de URL de vanidad. Si la URL denegada está en la lista, Dispatcher permite el acceso a la URL de vanidad.

Para habilitar el acceso a las URL de vanidad, agregue una sección `/vanity_urls` a la sección `/farms`, similar al siguiente ejemplo:

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

La sección `/vanity_urls` contiene las siguientes propiedades:

* `/url`: Ruta al servicio de URL de vanidad que se ejecuta en la instancia de procesamiento. El valor de esta propiedad debe ser `"/libs/granite/dispatcher/content/vanityUrls.html"`.

* `/file`: Ruta al archivo local donde Dispatcher almacena la lista de URL de vanidad. Asegúrese de que Dispatcher tiene acceso de escritura a este archivo.
* `/delay`: (Segundos) El tiempo entre llamadas a la URL de vanidad.

>[!NOTE]
>
>Si su procesamiento es una instancia de AEM debe instalar el paquete [VanityURLS-Components desde la carpeta de distribución de software](https://experience.adobe.com/#/downloads/content/software-distribution/es/aem.html?package=/content/software-distribution/en/details.html/content/dam/aem/public/adobe/packages/granite/vanityurls-components) para habilitar el servicio de URL de vanidad. (Consulte [Distribución de software](https://experienceleague.adobe.com/docs/experience-manager-65/administering/contentmanagement/package-manager.html?lang=es#software-distribution) para obtener más información).

Utilice el siguiente procedimiento para habilitar el acceso a las URL de vanidad.

1. Si el servicio de procesamiento es una instancia de AEM, instale el paquete `com.adobe.granite.dispatcher.vanityurl.content` en la instancia de publicación (consulte la nota anterior).
1. Para cada URL de vanidad que haya configurado para una página de AEM o CQ, asegúrese de que la configuración [`/filter`](#configuring-access-to-content-filter) deniegue la URL. Si es necesario, agregue un filtro que deniegue la dirección URL.
1. Agregue la sección `/vanity_urls` debajo de `/farms`.
1. Reinicie el servidor web Apache.

## Reenviar solicitudes de distribución: /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

Las solicitudes de distribución generalmente están destinadas únicamente a Dispatcher, por lo que de forma predeterminada no se envían al procesador (por ejemplo, una instancia de AEM).

Si es necesario, establezca la propiedad `/propagateSyndPost` en `"1"` para reenviar las solicitudes de distribución a Dispatcher. Si se configura, debe asegurarse de que las solicitudes de POST no se denieguen en la sección de filtro.

## Configurar la caché de Dispatcher: /cache {#configuring-the-dispatcher-cache-cache}

La sección `/cache` controla el modo en que Dispatcher almacena en caché los documentos. Configure varias subpropiedades para implementar sus estrategias de almacenamiento en caché:

* `/docroot`
* `/statfile`
* `/serveStaleOnError`
* `/allowAuthorized`
* `/rules`
* `/statfileslevel`
* `/invalidate`
* `/invalidateHandler`
* `/allowedClients`
* `/ignoreUrlParams`
* `/headers`
* `/mode`
* `/gracePeriod`
* `/enableTTL`

Un ejemplo de una sección de caché puede tener el siguiente aspecto:

```xml
/cache
  {
  /docroot "/opt/dispatcher/cache"
  /statfile  "/tmp/dispatcher-website.stat"
  /allowAuthorized "0"

  /rules
    {
    # List of files that are cached
    }

  /invalidate
    {
    # List of files that are auto-invalidated
    }
  }
  
```

>[!NOTE]
>
>Para el almacenamiento en caché que distingue entre permisos, consulte [Almacenamiento en caché de contenido seguro](permissions-cache.md).

### Especificar el directorio de la caché {#specifying-the-cache-directory}

La propiedad `/docroot` identifica el directorio donde se almacenan los archivos en caché.

>[!NOTE]
>
>El valor debe tener exactamente la misma ruta que la raíz del documento del servidor web para que Dispatcher y el servidor web administren los mismos archivos.\
>El servidor web es responsable de entregar el código de estado correcto cuando se utiliza el archivo de caché de Dispatcher, por eso es importante que también lo encuentre.

Si utiliza varias granjas, cada granja debe utilizar una raíz de documento diferente.

### Nombrar el archivo de estado {#naming-the-statfile}

La propiedad `/statfile` identifica el archivo que se va a utilizar como archivo de estado. Dispatcher utiliza este archivo para registrar la hora de la última actualización del contenido. El archivo de estado puede ser cualquier archivo del servidor web.

El archivo de estado no tiene contenido. Cuando se actualiza el contenido, Dispatcher actualiza la marca de tiempo. El nombre predeterminado del archivo de estado es `.stat` y se almacena en la carpeta docroot. Dispatcher bloquea el acceso al archivo de estado.

>[!NOTE]
>
>Si `/statfileslevel` está configurado, Dispatcher ignora la propiedad `/statfile` y utiliza `.stat` como nombre.

### Servir documentos anticuados cuando se producen errores {#serving-stale-documents-when-errors-occur}

La propiedad `/serveStaleOnError` controla si Dispatcher devuelve documentos invalidados cuando el servidor de procesamiento devuelve un error. De forma predeterminada, cuando se toca un archivo de estado y se invalida el contenido almacenado en caché, Dispatcher eliminará el contenido almacenado en caché la próxima vez que se solicite.

Si `/serveStaleOnError` está establecido en `"1"`, Dispatcher no elimina el contenido invalidado de la caché a menos que el servidor de procesamiento devuelva una respuesta correcta. Una respuesta 5xx de AEM o el tiempo de espera de una conexión hace que Dispatcher presente el contenido obsoleto y responda con un estado HTTP de 111 (Error de revalidación).

### Almacenar en caché cuando se utiliza la autenticación {#caching-when-authentication-is-used}

La propiedad `/allowAuthorized` controla si las solicitudes que contienen cualquiera de la siguiente información de autenticación se almacenan en caché:

* El encabezado `authorization`
* Una cookie denominada `authorization`
* Una cookie denominada `login-token`

De forma predeterminada, las solicitudes que incluyen esta información de autenticación no se almacenan en caché porque la autenticación no se realiza cuando se devuelve un documento en caché al cliente. Esta configuración evita que Dispatcher presente documentos en caché a usuarios que no tienen los derechos necesarios.

Sin embargo, si sus requisitos permiten el almacenamiento en caché de documentos autenticados, establezca `/allowAuthorized` en uno:

`/allowAuthorized "1"`

>[!NOTE]
>
>Para habilitar la administración de sesiones (con la propiedad `/sessionmanagement`), la propiedad `/allowAuthorized` debe establecerse en `"0"`.

### Especificar los documentos a almacenar en caché {#specifying-the-documents-to-cache}

La propiedad `/rules` controla qué documentos se almacenan en caché según la ruta del documento. Independientemente de la propiedad `/rules`, Dispatcher nunca almacena en caché un documento en las siguientes circunstancias:

* Si el URI de la solicitud contiene el signo de interrogación (`?`).
   * Esto generalmente indica una página dinámica, como un resultado de búsqueda, que no necesita almacenarse en la caché.
* Si falta la extensión del archivo.
   * El servidor web necesita la extensión para determinar el tipo de documento (el tipo MIME).
* Si el encabezado de autenticación está establecido (esto se puede configurar).
* Si la instancia de AEM responde con los siguientes encabezados:

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>Dispatcher puede almacenar en caché los métodos GET o HEAD (para el encabezado HTTP). Para obtener información adicional sobre el almacenamiento en caché de encabezados de respuesta, consulte la sección [Almacenamiento en caché de encabezados de respuesta HTTP](#caching-http-response-headers).

Cada elemento de la propiedad `/rules` incluye un patrón [`glob`](#designing-patterns-for-glob-properties) y un tipo:

* El patrón `glob` se usa para hacer coincidir la ruta del documento.
* El tipo indica si se deben almacenar en caché los documentos que coinciden con el patrón `glob`. El valor puede permitirse (para almacenar el documento en caché) o denegarse (para procesar siempre el documento).

Si no tiene páginas dinámicas (además de las que ya excluyen las reglas anteriores), puede configurar Dispatcher para almacenar todo el caché. La sección de reglas para esto presenta el siguiente aspecto:

```xml
/rules
  {
    /0000  {  /glob "*"   /type "allow" }
  }
```

Para obtener más información sobre las propiedades de glob, consulte [Diseño de patrones para propiedades de glob](#designing-patterns-for-glob-properties).

Si hay secciones de la página que sean dinámicas (por ejemplo, una aplicación de noticias) o dentro de un grupo cerrado de usuarios, puede definir excepciones:

>[!NOTE]
>
>Los grupos cerrados de usuarios no deben almacenarse en caché, ya que los derechos de usuario no se comprueban en busca de páginas en caché.

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }
  }
```

**Compresión**

En los servidores web Apache puede comprimir los documentos en caché. La compresión permite a Apache devolver el documento en un formulario comprimido si así lo solicita el cliente. La compresión se realiza automáticamente habilitando el módulo Apache `mod_deflate`, por ejemplo:

```xml
AddOutputFilterByType DEFLATE text/plain
```

El módulo se instala de forma predeterminada con Apache 2.x.

<!-- 

Comment Type: draft

<note type="note"> 
 <p>Depending on the Apache web server version you can enable <span class="code">gzip</span> compression as follows:</p> 
 <ul> 
  <li>For Apache 1.3 you can enable the <span class="code">mod_gzip </span>module. The module must be downloaded and installed.</li> 
  <li>For Apache 2.x you can enable the <span class="code">mod_deflate</span> module. The module is installed by default with Apache 2.x.<br /> </li> 
 </ul> 
 <p> </p> 
</note>

 -->

<!-- 

Comment Type: draft

<p>The following rule caches all documents in compressed form; Apache can return either the uncompressed or the compressed form to the client:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /rules!!discoiqbr!!&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/rulelabel&nbsp;&nbsp;{&nbsp;&nbsp;/glob&nbsp;"*"&nbsp;/type&nbsp;"allow"&nbsp;&nbsp;/compress&nbsp;"gzip"&nbsp;}!!discoiqbr!!&nbsp;&nbsp;} 
</codeblock>

 -->

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-11-13T09:23:24.326-0500

<p>Hidden the <span class="code">mod_gzip</span> content as requested in CQDOC-11124.</p>

 -->

### Invalidar archivos por nivel de carpeta {#invalidating-files-by-folder-level}

Utilice la propiedad `/statfileslevel` para invalidar los archivos en caché según su ruta:

* Dispatcher crea `.stat` archivos en cada carpeta desde la carpeta docroot hasta el nivel especificado. La carpeta docroot es el nivel 0.
* Los archivos se invalidan tocando el archivo `.stat`. La fecha de la última modificación del archivo `.stat` se compara con la fecha de la última modificación de un documento almacenado en caché. El documento se recupera si el archivo `.stat` es más reciente.

* Cuando se invalida un archivo ubicado en un nivel específico, se tocarán **todos** los archivos `.stat` desde docroot **hasta** el nivel del archivo invalidado o el `statsfilevel` configurado (el que sea menor).

   * Por ejemplo: si establece la propiedad `statfileslevel` en 6 y un archivo se invalida en el nivel 5, todos los archivos `.stat` de docroot a 5 se tocarán. Siguiendo con este ejemplo, si un archivo se invalida en el nivel 7, entonces se tocará cada archivo .`stat` desde el docroot al 6 (desde `/statfileslevel = "6"`).

Solo se ven afectados los recursos **de la ruta** al archivo invalidado. Consideremos el siguiente ejemplo: un sitio web utiliza la estructura `/content/myWebsite/xx/.`. Si establece `statfileslevel` como 3, se crea un archivo `.stat` de la siguiente manera:

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

Cuando se invalida un archivo en `/content/myWebsite/xx`, se modifica cada archivo `.stat` desde docroot hasta `/content/myWebsite/xx`. Este sería el caso solo para `/content/myWebsite/xx` y no por ejemplo `/content/myWebsite/yy` o `/content/anotherWebSite`.

>[!NOTE]
>
>La invalidación se puede evitar enviando un encabezado `CQ-Action-Scope:ResourceOnly` adicional. Esto se puede utilizar para vaciar recursos concretos sin invalidar otras partes de la caché. Consulte [esta página](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) e [Invalidación manual de la caché de Dispatcher](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/page-invalidate.html?lang=es#configuring) para obtener más información.

>[!NOTE]
>
>Si especifica un valor para la propiedad `/statfileslevel`, se ignora la propiedad `/statfile`.

### Invalidación automática de archivos en caché {#automatically-invalidating-cached-files}

La propiedad `/invalidate` define los documentos que se invalidan automáticamente cuando se actualiza el contenido.

Con la invalidación automática, Dispatcher no elimina los archivos en caché después de una actualización de contenido, sino que comprueba su validez la próxima vez que se soliciten. Los documentos de la caché que no se invalidan automáticamente permanecerán en la caché hasta que una actualización de contenido los elimine explícitamente.

La invalidación automática se suele utilizar para páginas HTML. Las páginas HTML suelen contener vínculos a otras páginas, lo que dificulta determinar si una actualización de contenido afecta a una página. Para asegurarse de que todas las páginas relevantes se invalidan cuando se actualiza el contenido, invalide automáticamente todas las páginas HTML. La siguiente configuración invalida todas las páginas HTML:

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

Para obtener más información sobre las propiedades de glob, consulte [Diseño de patrones para propiedades de glob](#designing-patterns-for-glob-properties).

Esta configuración provoca la siguiente actividad cuando se activa `/content/wknd/us/en`:

* Todos los archivos con patrón en.* se eliminarán de la carpeta `/content/wknd/us`.
* Se eliminará la carpeta `/content/wknd/us/en./_jcr_content`.
* El resto de archivos que coinciden con la configuración `/invalidate` no se eliminarán inmediatamente. Estos archivos se eliminarán cuando se produzca la siguiente solicitud. En nuestro ejemplo, `/content/wknd.html` no se elimina, se eliminará cuando se solicite `/content/wknd.html`.

Si ofrece archivos PDF y ZIP generados automáticamente para su descarga, es posible que tenga que invalidarlos automáticamente también. Un ejemplo de esta configuración tiene el siguiente aspecto:

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

La integración de AEM con Adobe Analytics ofrece datos de configuración en un archivo `analytics.sitecatalyst.js` del sitio web. El archivo de ejemplo `dispatcher.any` que se ofrece con Dispatcher incluye la siguiente regla de invalidación para este archivo:

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Utilizar secuencias de comandos de invalidación personalizadas {#using-custom-invalidation-scripts}

La propiedad `/invalidateHandler` permite definir una secuencia de comandos que se llame para cada solicitud de invalidación recibida por Dispatcher.

Se llama con los siguientes argumentos:

* Handle: la ruta de contenido que se invalida
* Action: la acción de replicación (por ejemplo, activar o desactivar)
* Action Scope: el ámbito de la acción de replicación (vacío, a menos que se envíe un encabezado de `CQ-Action-Scope: ResourceOnly`, consulte [Invalidación de páginas en caché de AEM](page-invalidate.md) para obtener más información).

Se puede usar para cubrir una serie de casos de uso diferentes, como invalidar otras cachés específicas de la aplicación, o para manejar casos en los que la URL externalizada de una página y su lugar en docroot no coinciden con la ruta de contenido.

A continuación, la secuencia de comandos de ejemplo registra cada solicitud invalidada en un archivo.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### ejemplo de script de administración de la invalidación {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Limitar los clientes que pueden vaciar la caché {#limiting-the-clients-that-can-flush-the-cache}

La propiedad `/allowedClients` define clientes específicos a los que se les permite vaciar la caché. Los patrones de globbing se comparan con la IP.

El siguiente ejemplo:

1. deniega el acceso a cualquier cliente
1. permite explícitamente el acceso al localhost

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

Para obtener más información sobre las propiedades de glob, consulte [Diseño de patrones para propiedades de glob](#designing-patterns-for-glob-properties).

>[!CAUTION]
>
>Se recomienda definir el `/allowedClients`.
>
>Si no, cualquier cliente puede emitir una llamada para borrar la caché; si esto se hace repetidamente, puede afectar gravemente al rendimiento del sitio.

### Ignorar parámetros de URL {#ignoring-url-parameters}

La sección `ignoreUrlParams` define qué parámetros de URL se omiten al determinar si una página se almacena en caché o se entrega desde la caché:

* Cuando una dirección URL de solicitud contiene parámetros que se omiten, la página se almacena en caché.
* Cuando una dirección URL de solicitud contiene uno o varios parámetros que no se ignoran, la página no se almacena en caché.

Cuando se ignora un parámetro para una página, la página se almacena en caché la primera vez que se solicita. Las solicitudes posteriores para la página se proporcionan en la página en caché, independientemente del valor del parámetro en la solicitud.

>[!NOTE]
>
>Se recomienda configurar la variable `ignoreUrlParams` en forma de lista de permitidos. Como tal, todos los parámetros de consulta se ignoran y solo los parámetros de consulta conocidos o esperados están exentos de ser ignorados (“denegado”). Para obtener más detalles y ejemplos, consulte [esta página](https://github.com/adobe/aem-dispatcher-optimizer-tool/blob/main/docs/Rules.md#dot—the-dispatcher-publish-farm-cache-should-have-its-ignoreurlparams-rules-configured-in-an-allow-list-manner).

Para especificar qué parámetros se ignoran, agregue reglas glob a la propiedad `ignoreUrlParams`:

* Para almacenar en caché una página a pesar de la solicitud que contiene un parámetro de URL, cree una propiedad glob que permita el parámetro (que se ignorará).
* Para evitar que la página se almacene en caché, cree una propiedad glob que rechace el parámetro (que se ignorará).

El siguiente ejemplo hace que Dispatcher ignore todos los parámetros, excepto el parámetro `nocache`. Dispatcher nunca almacena en caché las direcciones URL de solicitud que incluyen la variable `nocache`:

```xml
/ignoreUrlParams
{
    # allow-the-url-parameter-nocache-to-bypass-dispatcher-on-every-request
    /0001 { /glob "nocache" /type "deny" }
    # all-other-url-parameters-are-ignored-by-dispatcher-and-requests-are-cached
    /0002 { /glob "*" /type "allow" }
}
```

En el contexto del ejemplo de configuración `ignoreUrlParams` anterior, la siguiente petición HTTP hace que la página se almacene en caché porque el parámetro `willbecached` se ignora:

```xml
GET /mypage.html?willbecached=true
```

Con el valor de ejemplo de configuración `ignoreUrlParams`, la siguiente petición HTTP hace que la página **no** se almacene en caché porque el parámetro `nocache` no se ignora:

```xml
GET /mypage.html?nocache=true
GET /mypage.html?nocache=true&willbecached=true
```

Para obtener más información sobre las propiedades de glob, consulte [Diseño de patrones para propiedades de glob](#designing-patterns-for-glob-properties).

### Almacenar en caché encabezados de respuesta HTTP {#caching-http-response-headers}

>[!NOTE]
>
>Esta función está disponible con la versión **4.1.11** de Dispatcher.

La propiedad `/headers` permite definir los tipos de encabezado HTTP que Dispatcher va a almacenar en caché. En la primera solicitud a un recurso sin caché, todos los encabezados que coincidan con uno de los valores configurados (consulte el ejemplo de configuración que aparece a continuación) se almacenarán en un archivo independiente, junto al archivo de caché. En las solicitudes posteriores al recurso almacenado en caché, los encabezados almacenados se añadirán a la respuesta.

A continuación se muestra un ejemplo de la configuración predeterminada:

```xml
/cache {
  ...
  /headers {
    "Cache-Control"
    "Content-Disposition"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "X-Content-Type-Options"
    "Last-Modified"
  }
}
```

>[!NOTE]
>
>Tenga en cuenta también que no se permiten caracteres globbing de archivos. Para obtener más información, consulte [Diseño de patrones para las propiedades de glob](#designing-patterns-for-glob-properties).

>[!NOTE]
>
>Si necesita que Dispatcher almacene y envíe encabezados de respuesta ETag desde AEM, haga lo siguiente:
>
>* Agregue el nombre del encabezado a la sección `/cache/headers`.
>* Agregue la siguiente [directiva de Apache](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) a la sección relacionada con Dispatcher:
>
>```xml
>FileETag none
>```

### Permisos del archivo de caché de Dispatcher {#dispatcher-cache-file-permissions}

La propiedad `mode` especifica qué permisos de archivo se aplican a los nuevos directorios y archivos de la caché. Esta configuración está restringida por el `umask` del proceso de llamada. Es un número octal construido a partir de la suma de uno o más de los siguientes valores:

* `0400` Permitir la lectura por el propietario.
* `0200` Permitir la escritura por el propietario.
* `0100` Permitir que el propietario busque en directorios.
* `0040` Permitir la lectura por miembros del grupo.
* `0020` Permitir la escritura por miembros del grupo.
* `0010` Permitir que los miembros del grupo busquen en el directorio.
* `0004` Permitir que otros lo lean.
* `0002` Permitir que otros escriban.
* `0001` Permitir que otros busquen en el directorio.

El valor predeterminado es `0755` y permite al propietario leer, escribir o buscar, y al grupo y a otros leer o buscar.

### Acelerar el archivo .stat tocándolo {#throttling-stat-file-touching}

Con la propiedad predeterminada `/invalidate`, cada activación invalida efectivamente todos los archivos `.html` (cuando su ruta coincida con la sección `/invalidate`). En un sitio web con tráfico considerable, varias activaciones posteriores incrementarán la carga de la cpu en el backend. En este caso, sería deseable &quot;acelerar&quot; el archivo `.stat` tocándolo para mantener el sitio web receptivo. Para ello, utilice la propiedad `/gracePeriod`.

La propiedad `/gracePeriod` define el número de segundos que un recurso obsoleto e invalidado automáticamente puede seguir presentándose desde la caché después de la última activación. La propiedad se puede utilizar en una configuración en la que un lote de activaciones invalidaría repetidamente toda la caché. El valor recomendado es de 2 segundos.

Para obtener más información, lea las secciones anteriores `/invalidate` y `/statfileslevel`.

### Configuración de la invalidación de caché basada en tiempo - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

Si se establece en 1 (`/enableTTL "1"`), la propiedad `/enableTTL` evaluará los encabezados de respuesta del backend y, si contienen una fecha `Expires` o max-age `Cache-Control`, se creará un archivo auxiliar vacío junto al archivo de caché, con una hora de modificación igual a la fecha de expiración. Si se solicita el archivo en caché pasado el tiempo de modificación, se vuelve a solicitar automáticamente desde el backend.

>[!NOTE]
>
>Tenga en cuenta que el almacenamiento en caché basado en TTL es un superconjunto de almacenamiento en caché de encabezados y, como tal, la propiedad `/headers` también debe configurarse correctamente.

>[!NOTE]
>
>Esta función está disponible en la versión **4.1.11** o posterior de Dispatcher.

## Configuración del equilibrio de carga: /statistics {#configuring-load-balancing-statistics}

La sección `/statistics` define las categorías de archivos para los que Dispatcher puntúa la capacidad de respuesta de cada procesamiento. Dispatcher utiliza las puntuaciones para determinar qué proceso envía una solicitud.

Cada categoría que crea define un patrón global. Dispatcher compara el URI del contenido solicitado con estos patrones para determinar la categoría del contenido solicitado:

* El orden de las categorías determina el orden en que se comparan con el URI.
* El primer patrón de categoría que coincide con el URI es la categoría del archivo. No se evalúan más patrones de categoría.

Dispatcher admite un máximo de 8 categorías de estadísticas. Si define más de 8 categorías, solo se utilizarán las 8 primeras.

**Selección de procesamiento**

Cada vez que Dispatcher requiera una página representada, utilizará el siguiente algoritmo para seleccionar el procesamiento:

1. Si la solicitud contiene el nombre de procesamiento en una cookie `renderid`, Dispatcher utilizará ese procesamiento.
1. Si la solicitud no incluye ninguna cookie `renderid`, Dispatcher comparará las estadísticas de procesamiento:

   1. Dispatcher determina la categoría del URI de solicitud.
   1. Dispatcher determina qué procesamiento tiene la puntuación de respuesta más baja para esa categoría y lo selecciona.

1. Si todavía no hay ninguno seleccionado, utilizará el primero de la lista.

La puntuación de la categoría de un procesamiento se basa en tiempos de respuesta anteriores, así como en conexiones fallidas y exitosas anteriores en intentos de Dispatcher. Por cada intento, se actualiza la puntuación de la categoría del URI solicitado.

>[!NOTE]
>
>Si no utiliza el equilibrio de carga, puede omitir esta sección.

### Definición de categorías de estadística {#defining-statistics-categories}

Defina una categoría para cada tipo de documento del que desea mantener estadísticas para la selección de procesamiento. La sección `/statistics` contiene una sección `/categories`. Para definir una categoría, añada una línea debajo de la sección `/categories` que tenga el siguiente formato:

`/name { /glob "pattern"}`

La categoría `name` debe ser única en la granja. El `pattern` se describe en la sección [Diseño de patrones para propiedades glob](#designing-patterns-for-glob-properties).

Para determinar la categoría de un URI, Dispatcher compara el URI con cada patrón de categoría hasta que encuentre una coincidencia. Dispatcher comienza con la primera categoría de la lista y continúa en orden. Por tanto, coloque primero las categorías con patrones más específicos.

Por ejemplo, Dispatcher en el archivo predeterminado `dispatcher.any` define las categorías HTML y otros. La categoría HTML es más específica y, por lo tanto, aparece primero:

```xml
/statistics
  {
  /categories
    {
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

El siguiente ejemplo también incluye una categoría para páginas de búsqueda:

```xml
/statistics
  {
  /categories
    {
      /search { /glob "*search.html" }
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

### Reflejar la falta de disponibilidad del servidor en las estadísticas de Dispatcher {#reflecting-server-unavailability-in-dispatcher-statistics}

La propiedad `/unavailablePenalty` establece el tiempo (en décimas de segundo) que se aplica a las estadísticas de procesamiento cuando falla una conexión. Dispatcher agrega la hora a la categoría de estadísticas que coincida con el URI solicitado.

Por ejemplo, la penalización se aplica cuando no se puede establecer la conexión TCP/IP con el nombre de host/puerto designado, ya sea porque no se está ejecutando (ni escuchando) o debido a un problema relacionado con la red.

La propiedad `/unavailablePenalty` es un elemento secundario directo de la sección `/farm` (elemento secundario de la sección `/statistics`).

Si no existe ninguna propiedad `/unavailablePenalty`, se utiliza un valor de `"1"`.

```xml
/unavailablePenalty "1"
```

## Identificación de una carpeta de conexión fija: /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

La propiedad `/stickyConnectionsFor` define una carpeta que contiene documentos fijos; se accederá a ella mediante la dirección URL. Dispatcher enviará todas las solicitudes, de un solo usuario, que se encuentren en esta carpeta a la misma instancia de procesamiento. Las conexiones duraderas garantizan que los datos de la sesión estén presentes y sean coherentes en todos los documentos. Este mecanismo utiliza la cookie `renderid`.

En el siguiente ejemplo se define una conexión fija a la carpeta /products:

```xml
/stickyConnectionsFor "/products"
```

Cuando una página esté compuesta por contenido de varios nodos, incluya la propiedad `/paths` que enumera las rutas al contenido. Por ejemplo, una página contiene contenido de `/content/image`, `/content/video` y `/var/files/pdfs`. La siguiente configuración habilita las conexiones fijas para todo el contenido de la página:

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### httpOnly {#httponly}

Cuando las conexiones fijas estén habilitadas, el módulo de Dispatcher establecerá la cookie `renderid`. Esta cookie no tiene el indicador `httponly`, que debe añadirse para mejorar la seguridad. Puede hacerlo estableciendo la propiedad `httpOnly` en el nodo `/stickyConnections` de un archivo de configuración `dispatcher.any`. El valor de la propiedad (`0` o `1`) define si la cookie `renderid` tiene el atributo `HttpOnly` anexado. El valor predeterminado es `0`, lo que significa que no se agregará el atributo.

Para obtener más información sobre el indicador `httponly`, consulte [esta página](https://www.owasp.org/index.php/HttpOnly).

### secure {#secure}

Cuando las conexiones fijas estén habilitadas, el módulo de Dispatcher establecerá la cookie `renderid`. Esta cookie no tiene el indicador `secure`, que debe añadirse para mejorar la seguridad. Puede hacerlo estableciendo la propiedad `secure` en el nodo `/stickyConnections` de un archivo de configuración `dispatcher.any`. El valor de la propiedad (`0` o `1`) define si la cookie `renderid` tiene el atributo `secure` anexado. El valor predeterminado es `0`, lo que significa que el atributo se agregará **si** la solicitud entrante es segura. Si el valor se establece en `1`, se agregará el indicador seguro independientemente de si la solicitud entrante es segura o no.

## Administración de errores de conexión de procesamiento {#handling-render-connection-errors}

Configure el comportamiento de Dispatcher cuando el servidor de procesamiento devuelve un error 500 o no está disponible.

### Especificación de una página de comprobación de estado {#specifying-a-health-check-page}

Utilice la propiedad `/health_check` para especificar una dirección URL que se compruebe cuando se produzca un código de estado 500. Si esta página también devuelve un código de estado 500, la instancia se considera no disponible y se aplica una penalización horaria configurable (`/unavailablePenalty`) al procesamiento antes de volver a intentarlo.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### Especificación del retraso en el reintento de página {#specifying-the-page-retry-delay}

La propiedad `/retryDelay` establece el tiempo (en segundos) que Dispatcher espera entre rondas de intentos de conexión con los procesamientos de granja. Por cada ronda, el número máximo de veces que Dispatcher intenta establecer una conexión con un procesamiento es el número de procesamientos de la granja.

Dispatcher utiliza un valor de `"1"` si `/retryDelay` no está definido explícitamente. El valor predeterminado es el adecuado en la mayoría de los casos.

```xml
/retryDelay "1"
```

### Configuración del número de reintentos {#configuring-the-number-of-retries}

La propiedad `/numberOfRetries` establece el número máximo de rondas de intentos de conexión que Dispatcher realiza con los procesamientos. Si Dispatcher no se puede conectar correctamente a un proceso después de este número de reintentos, Dispatcher devuelve una respuesta fallida.

Por cada ronda, el número máximo de veces que Dispatcher intenta establecer una conexión con un procesamiento es el número de procesamientos de la granja. Por lo tanto, el número máximo de veces que Dispatcher intenta una conexión es (`/numberOfRetries`) x (el número de procesamientos).

Si el valor no se define explícitamente, el valor predeterminado es `5`.

```xml
/numberOfRetries "5"
```

### Uso del mecanismo de conmutación por error {#using-the-failover-mechanism}

Habilite el mecanismo de conmutación por error en la granja de Dispatcher para reenviar solicitudes a diferentes procesos cuando falle la solicitud original. Cuando la conmutación por error esté habilitada, Dispatcher se comportará de la siguiente forma:

* Cuando una solicitud a un procesamiento devuelva el estado HTTP 503 (NO DISPONIBLE), Dispatcher enviará la solicitud a un procesamiento diferente.
* Cuando una solicitud a un procesamiento devuelva el estado HTTP 50x (distinto de 503), Dispatcher enviará una solicitud para la página configurada para la propiedad `health_check`.
   * Si la comprobación de estado devuelve 500 (INTERNAL_SERVER_ERROR), Dispatcher enviará la solicitud original a un procesamiento diferente.
   * Si la comprobación de estado devuelve el estado HTTP 200, Dispatcher devolverá el error HTTP 500 inicial al cliente.

Para habilitar la conmutación por error, agregue la siguiente línea a la granja (o sitio web):

```xml
/failover "1"
```

>[!NOTE]
>
>Para reintentar las solicitudes HTTP que contienen un cuerpo, Dispatcher envía un encabezado de solicitud `Expect: 100-continue` al procesamiento antes de poner en cola el contenido real. CQ 5.5 con CQSE responde inmediatamente con 100 (CONTINUE) o un código de error. Otros contenedores de servlet también deberán admitir esto.

## Ignorar errores de interrupción - /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Esta opción no suele ser necesaria. Solo debe utilizar esta opción cuando vea los siguientes mensajes de registro:
>
>`Error while reading response: Interrupted system call`

Cualquier llamada al sistema orientada al sistema de archivos puede interrumpirse `EINTR` si el objeto de la llamada está ubicado en un sistema remoto al que se accede mediante NFS. El tiempo de espera de estas llamadas o su interrupción se deben a cómo se montó el sistema de archivos subyacente en el equipo local.

Utilice el parámetro `/ignoreEINTR` si la instancia tiene dicha configuración y el registro contiene el siguiente mensaje:

`Error while reading response: Interrupted system call`

Dispatcher lee internamente la respuesta del servidor remoto (es decir, AEM) utilizando un bucle que puede representarse como:

```text
while (response not finished) {  
read more data  
}
```

Estos mensajes se pueden generar cuando `EINTR` se produce en la sección &quot;`read more data`&quot; y los causa la recepción de una señal antes de recibir cualquier dato.

Para ignorar estas interrupciones, puede agregar el siguiente parámetro a `dispatcher.any` (antes de `/farms`):

`/ignoreEINTR "1"`

Establecer `/ignoreEINTR` en `"1"` hace que Dispatcher siga intentando leer los datos hasta que lea la respuesta completa. El valor predeterminado es `0` y desactiva la opción.

## Diseño de patrones para propiedades de glob {#designing-patterns-for-glob-properties}

Varias secciones del archivo de configuración de Dispatcher utilizan propiedades `glob` como criterios de selección para solicitudes de cliente. Los valores de las propiedades `glob` son patrones que Dispatcher compara con un aspecto de la solicitud, como la ruta del recurso solicitado o la dirección IP del cliente. Por ejemplo, los elementos de la sección `/filter` utilizan patrones `glob` para identificar las rutas de las páginas en las que Dispatcher actúa o rechaza.

Los valores `glob` pueden incluir caracteres comodín y alfanuméricos para definir el patrón.

| Carácter comodín | Descripción | Ejemplos |
|--- |--- |--- |
| `*` | Coincide con cero o más instancias contiguas de cualquier carácter de la cadena. El carácter final de la coincidencia viene determinado por cualquiera de las siguientes situaciones: <br/>Un carácter de la cadena coincide con el siguiente carácter del patrón y éste tiene las siguientes características:<br/><ul><li>No es *</li><li>No es ?</li><li>Un carácter literal (incluido un espacio) o una clase de carácter.</li><li>Se llega al final del patrón.</li></ul>Dentro de una clase de caracteres, el carácter se interpreta literalmente. | `*/geo*` Coincide con cualquier página debajo de los nodos `/content/geometrixx` y `/content/geometrixx-outdoors`. Las siguientes solicitudes HTTP coinciden con el patrón glob: <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*` <br/>Coincide con cualquier página debajo del nodo `/content/geometrixx-outdoors`. Por ejemplo, la siguiente solicitud HTTP coincide con el patrón glob: <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Coincide con cualquier carácter individual. Utilice clases de caracteres externos. Dentro de una clase de caracteres, este carácter se interpreta literalmente. | `*outdoors/??/*`<br/> Coincide con las páginas de cualquier idioma del sitio de geometrixx-outdoors. Por ejemplo, la siguiente solicitud HTTP coincide con el patrón glob: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>La siguiente solicitud no coincide con el patrón glob: <br/><ul><li>&quot;GET /content/geometrixx-outdoors/en.html&quot;</li></ul> |
| `[ and ]` | Marca el principio y el final de una clase de caracteres. Las clases de caracteres pueden incluir uno o más rangos de caracteres y caracteres únicos.<br/>Se produce una coincidencia si el carácter de destino coincide con cualquiera de los caracteres de la clase o dentro de un intervalo definido.<br/>Si no se incluye el soporte de cierre, el patrón no producirá coincidencias. | `*[o]men.html*`<br/> Coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*` <br/>Coincide con las siguientes solicitudes HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Indica un rango de caracteres. Para su uso en clases de caracteres.  Fuera de una clase de caracteres, este carácter se interpreta literalmente. | `*[m-p]men.html*` Coincide con la siguiente solicitud HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Anula los caracteres o clase de caracteres que sigue. Se utiliza solo para negar caracteres e intervalos de caracteres dentro de clases. Equivalente a `^ wildcard`. <br/>Fuera de una clase de caracteres, este carácter se interpreta literalmente. | `*[!o]men.html*`<br/> Coincide con la siguiente solicitud HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/>No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` o `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Anula los caracteres o el rango de caracteres que sigue. Se utiliza para negar solo caracteres e intervalos de caracteres dentro de clases. Equivale al carácter comodín `!`. <br/>Fuera de una clase de caracteres, este carácter se interpreta literalmente. | Se aplican los ejemplos del carácter comodín `!`, sustituyendo los caracteres `!` de los patrones de ejemplo por los caracteres `^`. |


<!--- need to troubleshoot table

The following table describes the wildcard characters.

<table border="1" cellpadding="1" cellspacing="0" width="100%"> 
 <tbody> 
  <tr> 
   <th>Wildcard character</th> 
   <th>Description</th> 
   <th>Examples</th> 
  </tr> 
  <tr> 
   <td>*</td> 
   <td><p>Matches zero or more contiguous instances of any character in the string. The final character of the match is determined by either of the following situations:</p> 
    <ul> 
     <li>A character in the string matches the next character in the pattern, and the pattern character has the following characteristics: 
      <ul> 
       <li>Not a *</li> 
       <li>Not a ?</li> 
       <li>A literal character (including a space) or a character class.</li> 
      </ul> </li> 
     <li>The end of the pattern is reached.</li> 
    </ul> <p>Inside a character class, the character is interpreted literally.</p> </td> 
   <td><p>*/geo*</p> <p>Matches any page below the /content/geometrixx node and the /content/geometrixx-outdoors node. The following HTTP requests match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx/en.html"</li> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> <p>*outdoors/*</p> <p>Matches any page below the /content/geometrixx-outdoors node. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html"</li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>?</td> 
   <td><p>Matches any single character. Use outside character classes.</p> <p>Inside a character class, this character is interpreted literally.</p> </td> 
   <td><p>*outdoors/??/*</p> <p>Matches the pages for any language in the geometrixx-outdoors site. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>The following request does not match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>[ and ]</td> 
   <td><p>Demarks the beginning and end of a character class.</p> <p>Character classes can include one or more character ranges and single characters.</p> <p>A match occurs if the target character matches any of the characters in the character class, or within a defined range.</p> <p>If the closing bracket is not included, the pattern produces no matches.</p> <p></p> <p></p> <p></p> </td> 
   <td><p>*[o]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p>*[o/]men.html*</p> <p>Matches the following HTTP requests: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
     <li> "GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>-</td> 
   <td><p>Denotes a range of characters. For use in character classes.<br /> </p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[m-p]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>!</td> 
   <td><p>Negates the character or character class that follows. Use only for negating characters and character ranges inside character classes. Equivalent to the ^ wildcard.</p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[!o]men.html*</p> <p>Matches the following HTTP request: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>Does not match the following HTTP request</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
    </ul> <p>*[!o!/]men.html*</p> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" or "GET /content/geometrixx-outdoors/en/men. html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>^</td> 
   <td><p>Negates the character or character range that follows. Use for negating only characters and character ranges inside character classes. Equivalent to the ! wildcard character.</p> <p>Outside of a character class, this charcter is interpreted literally.</p> </td> 
   <td>The examples for the ! wildcard character apply, substituting the ! characters in the example patterns with ^ characters.</td> 
  </tr> 
 </tbody> 
</table>
-->

## Registro {#logging}

En la configuración del servidor web, puede establecer:

* La ubicación del archivo de registro de Dispatcher.
* El nivel de registro.

Consulte la documentación del servidor web y el archivo «léame» de la instancia de Dispatcher para obtener más información.

**Registros Apache rotados/canalizados**

Si utiliza un servidor web **Apache**, puede utilizar la funcionalidad estándar para registros rotados y/o canalizados. Por ejemplo, con registros canalizados:

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Esto rotará automáticamente:

* el archivo de registro de Dispatcher; con una marca de tiempo en la extensión (`logs/dispatcher.log%Y%m%d`).
* semanalmente (60 x 60 x 24 x 7 = 604 800 segundos).

Consulte la documentación del servidor web Apache sobre la rotación de registros y los registros de canalización; por ejemplo, [Apache 2.4](https://httpd.apache.org/docs/2.4/logs.html).

>[!NOTE]
>
>Tras la instalación, el nivel de registro predeterminado es alto (es decir, nivel 3 = Depuración), de modo que Dispatcher registra todos los errores y advertencias. Esto es muy útil en las etapas iniciales.
>
>Sin embargo, esto requiere recursos adicionales, por lo que cuando Dispatcher funcione sin problemas *según sus requisitos*, puede (debe) reducir el nivel de registro.

### Seguimiento del registro {#trace-logging}

Entre otras mejoras para Dispatcher, la versión 4.2.0 también introduce el Seguimiento del registro.

Este es un nivel superior al registro de depuración, que muestra información adicional en los registros. Añade el registro para:

* Los valores de los encabezados reenviados;
* La regla que se está aplicando para una acción determinada.

Puede habilitar el Seguimiento del registro estableciendo el nivel de registro en `4` en el servidor web.

A continuación se muestra un ejemplo de registros con el seguimiento habilitado:

```xml
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Host] = "localhost:8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[User-Agent] = "curl/7.43.0"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Accept] = "*/*"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Client-Cert] = "(null)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Via] = "1.1 localhost:8443 (dispatcher)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-For] = "::1"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL] = "on"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Cipher] = "DHE-RSA-AES256-SHA"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Session-ID] = "ba931f5e4925c2dde572d766fdd436375e15a0fd24577b91f4a4d51232a934ae"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-Port] = "8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Server-Agent] = "Communique-Dispatcher"
```

Y un evento registrado cuando se solicita un archivo que coincide con una regla de bloqueo:

```xml
[Thu Mar 03 14:42:45 2016] [T] [11831] 'GET /content.infinity.json HTTP/1.1' was blocked because of /0082
```

## Confirmar operaciones básicas {#confirming-basic-operation}

Para confirmar operaciones básicas y la interacción del servidor web, Dispatcher y la instancia de AEM puede seguir los siguientes pasos:

1. Establezca el `loglevel` en `3`.

1. Inicie el servidor web; esto también iniciará Dispatcher.
1. Inicie la instancia de AEM.
1. Compruebe los archivos de registro y error del servidor web y de Dispatcher.
   * Según el servidor web, debería ver mensajes como:
      * `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured` y
      * `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Navegue por el sitio web a través del servidor web. Confirme que el contenido se muestra según sea necesario.\
   Por ejemplo, en una instalación local en la que AEM se ejecuta en el puerto `4502` y el servidor web en `80` acceden a la consola de sitios web mediante:
   * `https://localhost:4502/libs/wcm/core/content/siteadmin.html`
   * `https://localhost:80/libs/wcm/core/content/siteadmin.html`
   * Los resultados deben ser idénticos. Confirme el acceso a otras páginas con el mismo mecanismo.

1. Compruebe que se esté llenando el directorio de caché.
1. Active una página para comprobar que la caché se está vaciando correctamente.
1. Si todo funciona correctamente, puede reducir el `loglevel` a `0`.

## Utilizar varias instancias de Dispatcher {#using-multiple-dispatchers}

En configuraciones complejas, puede utilizar varias instancias de Dispatcher. Por ejemplo, puede utilizar:

* una instancia de Dispatcher para publicar un sitio web en la Intranet
* una segunda instancia de Dispatcher, en una dirección y con una configuración de seguridad diferentes, para publicar el mismo contenido en Internet.

En ese caso, asegúrese de que cada solicitud pasa por una única instancia de Dispatcher. Una instancia de Dispatcher no administra solicitudes procedentes de otra instancia de Dispatcher. Por lo tanto, asegúrese de que ambas instancias de Dispatcher acceden directamente al sitio web de AEM.

## Depuración {#debugging}

Al agregar el encabezado `X-Dispatcher-Info` a una solicitud, Dispatcher responderá si el objetivo se ha almacenado en caché, se ha devuelto desde la caché o no se puede almacenar en caché. El encabezado de respuesta `X-Cache-Info` contiene esta información en un formato legible. Puede utilizar estos encabezados de respuesta para depurar los problemas que implican respuestas almacenadas en caché por Dispatcher.

Esta funcionalidad no está habilitada de forma predeterminada, por lo que para que se incluya el encabezado de respuesta `X-Cache-Info`, la granja debe contener la siguiente entrada:

```xml
/info "1"
```

Por ejemplo,

```xml
/farm
{
    /mywebsite
    {
        # Include X-Cache-Info response header if X-Dispatcher-Info is in request header
        /info "1"
    }
}
```

Además, el encabezado `X-Dispatcher-Info` no necesita un valor, pero si utiliza `curl` para realizar pruebas debe proporcionar un valor para enviar el encabezado, por ejemplo:

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/wknd/us/en.html
```

A continuación se muestra una lista que contiene los encabezados de respuesta que `X-Dispatcher-Info` devolverá:

* **en caché**\
   El archivo de destino está en la caché y Dispatcher ha determinado que es válido entregarlo.
* **almacenamiento en caché**\
   El archivo de destino no está en la caché y Dispatcher ha determinado que es válido almacenar la salida en caché y entregarla.
* **almacenamiento en caché: el archivo stat es más reciente**. El archivo de destino está en la caché, sin embargo, está invalidado por un archivo stat más reciente. Dispatcher eliminará el archivo de destino, lo recreará de la salida y lo enviará.
* **no almacenable en caché: sin raíz de documento**. La configuración de la granja no contiene una raíz de documento (elemento de configuración 
`cache.docroot`).
* **no almacenable en caché: ruta del archivo de caché demasiado larga**\
   El archivo de destino, (la concatenación de la raíz del documento y el archivo URL), supera el nombre de archivo más largo permitido por el sistema.
* **no almacenable en caché: ruta de archivo temporal demasiado larga**\
   La plantilla del nombre de archivo temporal supera el nombre de archivo más largo permitido por el sistema. Dispatcher crea primero un archivo temporal, antes de crear o sobrescribir realmente el archivo almacenado en caché. El nombre de archivo temporal es el nombre del archivo de destino con los caracteres `_YYYYXXXXXX` anexados a él, donde los caracteres `Y` y `X` se reemplazarán para crear un nombre único.
* **no almacenable en caché: la dirección URL de solicitud no tiene extensión**\
   La dirección URL de solicitud no tiene extensión o hay una ruta después de la extensión de archivo, por ejemplo: `/test.html/a/path`.
* **no almacenable en caché: la solicitud no era un GET ni HEAD** El método HTTP no es ni GET ni HEAD. Dispatcher supone que la salida contendrá datos dinámicos que no deberían almacenarse en caché.
* **no almacenable en caché: solicitud contenida en una cadena de consulta**\
   La solicitud contenía una cadena de consulta. Dispatcher supone que el resultado depende de la cadena de consulta dada y, por lo tanto, no almacena en caché.
* **no almacenable en caché: el administrador de sesiones no se ha autentificado**\
   La caché de la granja está regida por un administrador de sesiones (la configuración contiene un nodo `sessionmanagement`) y la solicitud no contenía la información de autenticación adecuada.
* **no almacenable en caché: la solicitud contiene autorización**\
   No se permite que la granja almacene en caché la salida (`allowAuthorized 0`) y la solicitud contiene información de autenticación.
* **no almacenable en caché: el destino es un directorio**\
   El archivo de destino es un directorio. Esto podría indicar algún error conceptual, que una dirección URL y algunas direcciones URL secundarias contengan resultados almacenables en caché, por ejemplo, si una solicitud a `/test.html/a/file.ext` es la primera y contiene resultados almacenables en caché, Dispatcher no podrá almacenar en caché el resultado de una solicitud posterior a `/test.html`.
* **no almacenable en caché: la dirección URL de solicitud tiene una barra diagonal**\
   La dirección URL de la solicitud tiene una barra diagonal.
* **no almacenable en caché: la URL de solicitud no está en las reglas de caché**\
   Las reglas de caché de la granja deniegan explícitamente el almacenamiento en caché de la salida de alguna URL de solicitud.
* **no almacenable en caché: acceso denegado del verificador de autorizaciones**\
   El verificador de autorizaciones de la granja denegó el acceso al archivo en caché.
* **no almacenable en caché: sesión no válida**.
La caché de la granja está regida por un administrador de sesiones (la configuración contiene un nodo `sessionmanagement`) y la sesión del usuario ya no es válida.
* **no almacenable en caché: la respuesta contiene`no_cache`** El servidor remoto devolvió un 
encabezado `Dispatcher: no_cache`, prohibiendo que Dispatcher almacene en caché la salida.
* **no almacenable en caché: la longitud del contenido de respuesta es cero**. La longitud del contenido de la respuesta es cero; Dispatcher no creará un archivo de longitud cero.
