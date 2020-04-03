---
title: Configuración de Dispatcher
seo-title: Configuración de Dispatcher
description: Obtenga información sobre cómo configurar Dispatcher.
seo-description: Obtenga información sobre cómo configurar Dispatcher.
uuid: 253ef0f7-2491-4cec-ab22-97439df29fd6
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
topic-tags: dispatcher
content-type: reference
discoiquuid: aeffee8e-bb34-42a7-9a5e-b7d0e848391a
translation-type: tm+mt
source-git-commit: 183131dec51b67e152a8660c325ed980ae9ef458

---


# Configuración de Dispatcher{#configuring-dispatcher}

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher insertado en la documentación de una versión anterior de AEM.

Las siguientes secciones describen cómo configurar varios aspectos del despachante.

## Compatibilidad con IPv4 e IPv6 {#support-for-ipv-and-ipv}

Todos los elementos de AEM y Dispatcher se pueden instalar en redes IPv4 e IPv6. Consulte [IPV4 e IPV6](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html#AdditionalPlatformNotes).

## Archivos de configuración de Dispatcher {#dispatcher-configuration-files}

De forma predeterminada, la configuración de Dispatcher se almacena en el archivo de `dispatcher.any` texto, aunque puede cambiar el nombre y la ubicación de este archivo durante la instalación.

El archivo de configuración contiene una serie de propiedades de un solo valor o de varios valores que controlan el comportamiento de Dispatcher:

* Los nombres de propiedad llevan el prefijo una barra diagonal `/`.
* Las propiedades multivalor encierran elementos secundarios mediante llaves `{ }`.

Una configuración de ejemplo está estructurada de la siguiente manera:

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

* Si el archivo de configuración es grande, puede dividirlo en varios archivos más pequeños (más fáciles de administrar) y luego incluirlos.
* Para incluir archivos que se generan automáticamente.

Por ejemplo, para incluir el archivo myFarm.any en la configuración /Granjas, utilice el siguiente código:

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Utilice el asterisco (&quot;*&quot;) como comodín para especificar un rango de archivos que se van a incluir.

Por ejemplo, si los archivos `farm_1.any` que se van a incluir para `farm_5.any` contener la configuración de las granjas de uno a cinco, puede incluirlos de la siguiente manera:

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Uso de variables de Entorno {#using-environment-variables}

Puede utilizar variables de entorno en propiedades con valor de cadena en el archivo dispatcher.any en lugar de codificar los valores de forma rígida. Para incluir el valor de una variable de entorno, utilice el formato `${variable_name}`.

Por ejemplo, si el archivo dispatcher.any se encuentra en el mismo directorio que el directorio de la memoria caché, se puede utilizar el siguiente valor para la propiedad [docroot](dispatcher-configuration.md#main-pars-title-30) :

```xml
/docroot "${PWD}/cache"
```

Otro ejemplo: si crea una variable de entorno denominada `PUBLISH_IP` que almacena el nombre de host de la instancia de publicación de AEM, se puede utilizar la siguiente configuración de la propiedad [/renders](dispatcher-configuration.md#main-pars-127-25-0008) :

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Asignación de nombres a la instancia de Dispatcher {#naming-the-dispatcher-instance-name}

Utilice la `/name` propiedad para especificar un nombre único para identificar la instancia de Dispatcher. La `/name` propiedad es una propiedad de nivel superior de la estructura de configuración.

## Definición de Granjas {#defining-farms-farms}

La propiedad `/farms` define uno o más conjuntos de comportamientos de Dispatcher, donde cada conjunto está asociado con diferentes sitios Web o direcciones URL. La `/farms` propiedad puede incluir una o varias granjas:

* Utilice un único conjunto de servidores cuando desee que Dispatcher gestione todas las páginas web o los sitios web del mismo modo.
* Cree varios entornos cuando diferentes áreas del sitio web o de sitios web requieran un comportamiento diferente de Dispatcher.

La `/farms` propiedad es una propiedad de nivel superior de la estructura de configuración. Para definir una granja, agregue una propiedad secundaria a la `/farms` propiedad. Utilice un nombre de propiedad que identifique de forma exclusiva el conjunto de servidores dentro de la instancia de Dispatcher.

La `/farmname` propiedad tiene varios valores y contiene otras propiedades que definen el comportamiento de Dispatcher:

* Las direcciones URL de las páginas a las que se aplica el conjunto de servidores.
* Una o varias URL de servicio (normalmente de instancias de publicación de AEM) que se utilizarán para procesar documentos.
* Estadísticas que se utilizarán para equilibrar la carga de varios procesadores de documento.
* Otros comportamientos, como qué archivos se van a almacenar en caché y dónde.

El valor puede incluir cualquier carácter alfanumérico (a-z, 0-9). En el siguiente ejemplo se muestra la definición del esqueleto para dos granjas denominadas `/daycom` y `/docsdaycom`:

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
>Si utiliza más de un conjunto de procesamiento, la lista se evalúa de abajo hacia arriba. Esto es especialmente relevante a la hora de definir los hosts [virtuales](dispatcher-configuration.md#main-pars-117-15-0006) para los sitios web.

Cada propiedad de granja puede contener las siguientes propiedades secundarias:

| Nombre de la propiedad  | Descripción |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | Página principal predeterminada (opcional) (solo IIS) |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | Encabezados de la solicitud HTTP del cliente para pasar. |
| [/virtualhosts](#identifying-virtual-hosts-virtualhosts) | Los hosts virtuales de esta granja. |
| [/sessionmanagement](#enabling-secure-sessions-sessionmanagement) | Compatibilidad con la administración y autenticación de sesiones. |
| [/renders](#defining-page-renderers-renders) | Servidores que proporcionan páginas procesadas (normalmente instancias de publicación de AEM). |
| [/filter](#configuring-access-to-content-filter) | Define las direcciones URL a las que Dispatcher habilita el acceso. |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | Configura el acceso a las direcciones URL personales. |
| [/propagateSyndPost](#forwarding-syndication-requests-propagatesyndpost) | Compatibilidad con el reenvío de solicitudes de distribución. |
| [/cache](#configuring-the-dispatcher-cache-cache) | Configura el comportamiento de almacenamiento en caché. |
| [/statistics](#configuring-load-balancing-statistics) | Definición de categorías estadísticas para cálculos de equilibrio de carga. |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-stickyconnectionsfor) | Carpeta que contiene documentos adhesivos. |
| [/health_check](#specifying-a-health-check-page) | Dirección URL que se usará para determinar la disponibilidad del servidor. |
| [/reintentarDelay](#specifying-the-page-retry-delay) | El retraso antes de volver a intentar una conexión con error. |
| [/availablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | Sanciones que afectan a las estadísticas para cálculos de equilibrio de carga. |
| [/failover](#using-the-failover-mechanism) | Volver a enviar solicitudes a diferentes procesamientos cuando se produzca un error en la solicitud original. |
| [/auth_checker](permissions-cache.md) | Para almacenar en caché los permisos, consulte [Almacenamiento en caché de contenido](permissions-cache.md)seguro. |

## Especificar una página predeterminada (solo IIS) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>El `/homepage`parámetro (solo IIS) ya no funciona. En su lugar, debe utilizar el módulo [de reescritura de URL de](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module)IIS.
>
>Si utiliza Apache, debe utilizar el `mod_rewrite` módulo. Consulte la documentación del sitio Web de Apache para obtener información sobre `mod_rewrite` (por ejemplo, [Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). Cuando se utiliza `mod_rewrite`, se recomienda utilizar el indicador **[&#39;pass|PT&#39; (pasar al siguiente controlador)](https://helpx.adobe.com/dispatcher/kb/DispatcherModReWrite.html)**para forzar al motor de reescritura a establecer el`uri`campo de la`request_rec`estructura interna en el valor del`filename`campo.

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

## Especificación de los encabezados HTTP para pasar {#specifying-the-http-headers-to-pass-through-clientheaders}

La propiedad `/clientheaders` define una lista de encabezados HTTP que Dispatcher pasa de la solicitud HTTP del cliente al procesador (instancia de AEM).

De forma predeterminada, Dispatcher reenvía los encabezados HTTP estándar a la instancia de AEM. En algunos casos, es posible que desee reenviar encabezados adicionales o quitar encabezados específicos:

* Añada encabezados, como encabezados personalizados, que la instancia de AEM espera en la solicitud HTTP.
* Elimine encabezados, como encabezados de autenticación, que solo sean relevantes para el servidor web.

Si personaliza el conjunto de encabezados para pasar, debe especificar una lista exhaustiva de los encabezados, incluidos los que normalmente se incluyen de forma predeterminada.

Por ejemplo, una instancia de Dispatcher que gestiona solicitudes de activación de página para instancias de publicación requiere el `PATH` encabezado de la `/clientheaders` sección. El `PATH` encabezado habilita la comunicación entre el agente de replicación y el despachante.

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
  "if-match"
  "if-none-match"
  "if-range"
  "if-unmodified-since"
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

## Identificación de hosts virtuales {#identifying-virtual-hosts-virtualhosts}

La `/virtualhosts` propiedad define una lista de todas las combinaciones de nombre de host/URI que Dispatcher acepta para esta granja de servidores. Puede utilizar el carácter de asterisco (&quot;*&quot;) como comodín. Los valores de la propiedad / `virtualhosts` utilizan el siguiente formato:

```xml
[scheme]host[uri][*]
```

* `scheme`:: (Opcional) `https://` o `https://.`
* `host`:: El nombre o la dirección IP del equipo host y el número de puerto, si es necesario. (Consulte [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23))
* `uri`:: (Opcional) La ruta a los recursos.

La siguiente configuración de ejemplo controla las solicitudes de los dominios .com y .ch de myCompany y de todos los dominios de mySubDivision:

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

La siguiente configuración controla *todas* las solicitudes:

```xml
   /virtualhosts
    {
    "*"
    }
```

### Resolución del host virtual {#resolving-the-virtual-host}

Cuando Dispatcher recibe una solicitud HTTP o HTTPS, encuentra el valor de host virtual que mejor se corresponde con los encabezados `host,` y `uri``scheme` de la solicitud. Dispatcher evalúa los valores de las `virtualhosts` propiedades en el orden siguiente:

* Dispatcher comienza en la granja más baja y avanza hacia arriba en el archivo dispatcher.any.
* Para cada granja, Dispatcher comienza con el valor superior de la propiedad y avanza hacia abajo en la lista de valores `virtualhosts` .

Dispatcher encuentra el valor de host virtual que mejor se corresponde con él de la siguiente manera:

* Se utiliza el host virtual que se encuentra por primera vez y que coincide con los tres `host`, el `scheme`y el `uri` de la solicitud.
* Si ningún `virtualhosts` valor tiene `scheme` y `uri` partes que coincidan tanto con el `scheme` como con `uri` la solicitud, se utiliza el host virtual que se encuentra por primera vez y que coincide con el `host` de la solicitud.
* Si ningún `virtualhosts` valor tiene una parte host que coincida con el host de la solicitud, se utiliza el host virtual superior de la granja de servidores superior.

Por lo tanto, debe colocar el host virtual predeterminado en la parte superior de la `virtualhosts` propiedad en el conjunto de servidores superior del archivo dispatcher.any.

### Ejemplo de resolución de host virtual {#example-virtual-host-resolution}

El siguiente ejemplo representa un fragmento de un archivo dispatcher.any que define dos conjuntos de Dispatcher y cada conjunto de servidores define una `virtualhosts` propiedad.

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

En este ejemplo, la tabla siguiente muestra los hosts virtuales resueltos para solicitudes HTTP determinadas:

| URL de solicitud | Host virtual resuelto |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## Habilitación de sesiones seguras: /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized` **debe** configurarse `"0"` en la `/cache` sección para habilitar esta función.

Cree una sesión segura para acceder al conjunto de procesamiento, de modo que los usuarios tengan que iniciar sesión para acceder a cualquier página del conjunto de servidores. Después de iniciar sesión, los usuarios pueden acceder a las páginas de la granja. Consulte [Creación de un grupo](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/cug.html#CreatingTheUserGroupToBeUsed) de usuarios cerrado para obtener información sobre el uso de esta función con CUG. Además, consulte la lista de comprobación de [seguridad de Dispatcher](/help/using/security-checklist.md) antes de activarla.

La `/sessionmanagement` propiedad es una subpropiedad de `/farms`.

>[!CAUTION]
>
>Si las secciones del sitio web utilizan diferentes requisitos de acceso, debe definir varios conjuntos de servidores.

**/sessionmanagement** tiene varios subparámetros:

**/directory** (obligatorio)

El directorio que almacena la información de la sesión. Si el directorio no existe, se crea.

>[!CAUTION]
>
> Al configurar el subparámetro de directorio **no** apunte a la carpeta raíz (`/directory "/"`) ya que puede causar problemas graves. Siempre debe especificar la ruta a la carpeta que almacena la información de la sesión. Por ejemplo:

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions"
  }
```

**/encode** (opcional)

Cómo se codifica la información de la sesión. Utilice &quot;md5&quot; para el cifrado con el algoritmo md5 o &quot;hex&quot; para la codificación hexadecimal. Si cifra los datos de la sesión, un usuario con acceso al sistema de archivos no podrá leer el contenido de la sesión. El valor predeterminado es &quot;md5&quot;.

**/header** (opcional)

Nombre del encabezado HTTP o la cookie que almacena la información de autorización. Si almacena la información en el encabezado http, utilice `HTTP:<*header-name*>`. Para almacenar la información en una cookie, utilice `Cookie:<header-name>`. Si no especifica un valor, `HTTP:authorization` se utiliza.

**/timeout** (opcional)

El número de segundos hasta que se agota el tiempo de espera de la sesión después de que se haya utilizado por última vez. Si no se especifica &quot;800&quot;, el tiempo de espera de la sesión finaliza un poco más de 13 minutos después de la última solicitud del usuario.

A continuación se muestra un ejemplo de configuración:

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions" 
  /encode "md5" 
  /header "HTTP:authorization" 
  /timeout "800" 
  }
```

## Definición de procesadores de página {#defining-page-renderers-renders}

La propiedad /renders define la dirección URL a la que Dispatcher envía solicitudes para procesar un documento. La siguiente sección de ejemplo `/renders` identifica una sola instancia de AEM para el procesamiento:

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

La siguiente sección de ejemplo /renders identifica una instancia de AEM que se ejecuta en el mismo equipo que dispatcher:

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

Especifica el tiempo de espera de conexión que accede a la instancia de AEM en milisegundos. El valor predeterminado es &quot;0&quot;, lo que provoca que Dispatcher espere indefinidamente.

**/receivedTimeout**

Especifica el tiempo en milisegundos que puede tardar una respuesta. El valor predeterminado es &quot;600000&quot;, lo que provoca que Dispatcher espere 10 minutos. Un valor de &quot;0&quot; elimina completamente el tiempo de espera.\
Si se alcanza el tiempo de espera al analizar los encabezados de respuesta, se devuelve un estado HTTP 504 (puerta de enlace incorrecta). Si se alcanza el tiempo de espera mientras se lee el cuerpo de respuesta, Dispatcher devolverá la respuesta incompleta al cliente, pero eliminará cualquier archivo de caché que se haya escrito.

**/ipv4**

Especifica si Dispatcher utiliza la `getaddrinfo` función (para IPv6) o la `gethostbyname` función (para IPv4) para obtener la dirección IP del procesamiento. Se utiliza un valor de 0 `getaddrinfo` . Se utiliza un valor de 1 `gethostbyname` . El valor predeterminado es 0.

La función getaddrinfo devuelve una lista de direcciones IP. Dispatcher repite la lista de direcciones hasta que establece una conexión TCP/IP. Por lo tanto, la propiedad ipv4 es importante cuando el nombre de host de procesamiento está asociado con varias direcciones IP y el host, en respuesta a la función getaddrinfo, devuelve una lista de direcciones IP que siempre están en el mismo orden. En este caso, debe utilizar la función gethostbyname de modo que la dirección IP con la que se conecta Dispatcher esté aleatorizada.

El Equilibrio de carga elástica de Amazon (ELB) es un servicio que responde a getaddrinfo con una lista potencialmente similar de direcciones IP.

**/secure**

Si la `/secure` propiedad tiene el valor &quot;1&quot; Dispatcher utiliza HTTPS para comunicarse con la instancia de AEM. Para obtener más información, consulte también [Configuración de Dispatcher para Usar SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

Con Dispatcher versión **4.1.6**, puede configurar la `/always-resolve` propiedad de la siguiente manera:

* Cuando se establece en &quot;1&quot;, se resuelve el nombre de host en cada solicitud (Dispatcher nunca almacenará en caché ninguna dirección IP). Puede haber un ligero impacto en el rendimiento debido a la llamada adicional necesaria para obtener la información del host para cada solicitud.
* Si no se establece la propiedad, la dirección IP se almacenará en caché de forma predeterminada.

Además, esta propiedad se puede utilizar en caso de que se produzcan problemas de resolución dinámica de IP, como se muestra en el siguiente ejemplo:

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

## Configuración del acceso al contenido {#configuring-access-to-content-filter}

Utilice la sección `/filter` para especificar las solicitudes HTTP que acepta Dispatcher. Todas las demás solicitudes se envían al servidor web con un código de error 404 (página no encontrada). Si no existe ninguna `/filter` sección, se aceptan todas las solicitudes.

**Nota:** Las solicitudes para el [archivo](dispatcher-configuration.md#main-pars-title-28) de estado siempre se rechazan.

>[!CAUTION]
>
>Consulte la lista [de comprobación de seguridad de](security-checklist.md) Dispatcher para obtener más información sobre cómo restringir el acceso mediante Dispatcher. Además, lea la lista de comprobación de seguridad de [AEM](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html) para obtener más información sobre la seguridad de la instalación de AEM.

La sección /filter consta de una serie de reglas que deniegan o permiten el acceso al contenido según los patrones de la parte de la línea de solicitud de la solicitud HTTP. Debe utilizar una estrategia de lista de elementos permitidos para la sección /filter:

* Primero, negar el acceso a todo.
* Permita el acceso al contenido según sea necesario.

### Definición de un filtro {#defining-a-filter}

Cada elemento de la `/filter` sección incluye un tipo y un patrón que coinciden con un elemento específico de la línea de solicitud o con toda la línea de solicitud. Cada filtro puede contener los siguientes elementos:

* **Tipo**: El `/type` indica si se permite o se deniega el acceso a las solicitudes que coinciden con el patrón. El valor puede ser `allow` o `deny`.

* **Elemento de la línea de solicitud:** Incluya `/method`, `/url`, `/query`o `/protocol` y un patrón para filtrar solicitudes según estas partes específicas de la parte de línea de solicitud de la solicitud HTTP. El método de filtro preferido es filtrar los elementos de la línea de solicitud (en lugar de hacerlo en toda la línea de solicitud).

* **Elementos avanzados de la línea de solicitud:** A partir de Dispatcher 4.2.0, hay cuatro nuevos elementos de filtro disponibles para su uso. Estos nuevos elementos son `/path`, `/selectors`, `/extension` y `/suffix` respectivamente. Incluya uno o varios de estos elementos para controlar aún más los patrones de URL.

>[!NOTE]
>
>Para obtener más información sobre qué parte de la línea de solicitud hace referencia cada uno de estos elementos, consulte la página wiki de descomposición de URL de [Sling](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html) .

* **glob (propiedad**): La `/glob` propiedad se utiliza para coincidir con toda la línea de solicitud de la solicitud HTTP.

>[!CAUTION]
>
>El filtrado con globos está en desuso en Dispatcher. Por lo tanto, debe evitar usar globos en las `/filter` secciones, ya que esto puede generar problemas de seguridad. Entonces, en lugar de:

`/glob "* *.css *"`

debe usar

`/url "*.css"`

#### La parte de línea de solicitud de solicitudes HTTP {#the-request-line-part-of-http-requests}

HTTP/1.1 define la línea [de](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) solicitud de la siguiente manera:

*Método Request-URI HTTP-Version*&lt;CRLF>

Los caracteres &lt;CRLF> representan un retorno de carro seguido de una fuente de línea. El siguiente ejemplo es la línea de solicitud que se recibe cuando un cliente solicita la página final del sitio Geometrixx-Outoors:

GET /content/geometrixx-outdoors/en.html HTTP.1.1&lt;CRLF>

Los patrones deben tener en cuenta los caracteres de espacio en la línea de solicitud y los caracteres &lt;CRLF>.

#### Comillas de Doble vs. Comillas simples {#double-quotes-vs-single-quotes}

Al crear las reglas de filtro, utilice comillas de doble `"pattern"` para patrones sencillos. Si utiliza Dispatcher 4.2.0 o posterior y el patrón incluye una expresión regular, debe incluir el patrón regex `'(pattern1|pattern2)'` entre comillas simples.

#### Regular Expressions {#regular-expressions}

Después de Dispatcher 4.2.0, puede incluir Expresiones regulares POSIX Extended en los patrones de filtro.

#### Resolución de problemas de Filtros {#troubleshooting-filters}

Si los filtros no se activan de la manera esperada, habilite [Seguimiento de registro](#trace-logging) en el despachante para que pueda ver qué filtro está interceptando la solicitud.

#### Ejemplo de filtro: Denegar todo {#example-filter-deny-all}

La siguiente sección de filtro de ejemplo provoca que Dispatcher deniegue solicitudes para todos los archivos. Debe denegar el acceso a todos los archivos y, a continuación, permitir el acceso a áreas específicas.

```xml
  /0001  { /glob "*" /type "deny" }
```

Las solicitudes a un área denegada explícitamente resultan en la devolución de un código de error 404 (página no encontrada).

#### Ejemplo de filtro: Denegar acceso a áreas específicas {#example-filter-deny-acess-to-specific-areas}

Las Filtros también permiten denegar el acceso a varios elementos, por ejemplo, páginas ASP y áreas sensibles dentro de una instancia de publicación. El filtro siguiente deniega el acceso a páginas ASP:

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Ejemplo de filtro: Habilitar solicitudes POST {#example-filter-enable-post-requests}

El siguiente filtro de ejemplo permite enviar datos de formulario mediante el método POST:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Ejemplo de filtro: Permitir acceso a la consola Flujo de trabajo {#example-filter-allow-access-to-the-workflow-console}

El siguiente ejemplo muestra un filtro utilizado para denegar el acceso externo a la consola Flujo de trabajo:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

Si la instancia de publicación utiliza un contexto de aplicación web (por ejemplo, publicación), también se puede agregar a la definición del filtro.

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

Si todavía necesita acceder a páginas únicas dentro del área restringida, puede permitir el acceso a ellas. Por ejemplo, para permitir el acceso a la ficha Archivar de la consola Flujo de trabajo, agregue la siguiente sección:

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>Cuando se aplican varios patrones de filtros a una solicitud, el último patrón de filtro que se aplica es efectivo.

#### Ejemplo de filtro: Uso de Expresiones regulares {#example-filter-using-regular-expressions}

Este filtro permite extensiones en directorios de contenido no público mediante una expresión normal, definida aquí entre comillas simples:

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Ejemplo de filtro: Filtrar elementos adicionales de una URL de solicitud {#example-filter-filter-additional-elements-of-a-request-url}

A continuación se muestra un ejemplo de regla que bloquea la captura de contenido de la ruta y de su subárbol, mediante filtros para rutas, selectores y extensiones: `/content`

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }
```

### Ejemplo de sección /filter {#example-filter-section}

Al configurar Dispatcher, debe restringir el acceso externo tanto como sea posible. El siguiente ejemplo proporciona un acceso mínimo a los visitantes externos:

* `/content`
* contenido diverso, como diseños y bibliotecas de clientes; por ejemplo:

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

Después de crear filtros, [pruebe el acceso](dispatcher-configuration.md#main-pars-title-19) a la página para asegurarse de que la instancia de AEM es segura.

La siguiente sección /filter del archivo dispatcher.any puede utilizarse como base en el archivo de configuración [de](dispatcher-configuration.md) Dispatcher.

Este ejemplo se basa en el archivo de configuración predeterminado que se proporciona con Dispatcher y está diseñado como ejemplo para su uso en un entorno de producción. Los elementos con el prefijo # están desactivados (comentados), se debe tener cuidado si decide activar alguno de estos elementos (eliminando el # en esa línea) ya que esto puede tener un impacto en la seguridad.

Debe denegar el acceso a todo y, a continuación, permitir el acceso a elementos específicos (limitados):

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
      /0001 { /type "deny" /glob "*" }
      
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
>Cuando se utiliza con Apache, diseñe los patrones de URL del filtro según la propiedad DispatcherUseProcessedURL del módulo Dispatcher. (Consulte [Apache Web Server - Configurar el servidor Web Apache para Dispatcher](dispatcher-install.md#main-pars-55-35-1022)).

>[!NOTE]
>
>Los Filtros 0030 y 0031 relativos a Dynamic Media son aplicables a AEM 6.0 y versiones posteriores.

Tenga en cuenta las siguientes recomendaciones si decide ampliar el acceso:

* El acceso externo a `/admin` siempre debe estar *completamente* deshabilitado si utiliza CQ versión 5.4 o una versión anterior.

* Se debe tener cuidado al permitir el acceso a los archivos en `/libs`. El acceso debe permitirse de forma individual.
* Denegar el acceso a la configuración de replicación para que no se pueda ver:

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Denegar acceso al proxy inverso de Google Gadgets:

   * `/libs/opensocial/proxy*`

En función de la instalación, es posible que haya recursos adicionales disponibles en `/libs`o `/apps` en cualquier otro lugar. Puede utilizar el `access.log` archivo como un método para determinar los recursos a los que se accede externamente.

>[!CAUTION]
>
>El acceso a consolas y directorios puede representar un riesgo de seguridad para los entornos de producción. A menos que tenga justificaciones explícitas, deben permanecer desactivadas (comentadas).

>[!CAUTION]
>
>Si está [utilizando informes en un entorno](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/reporting.html#UsingReportsinaPublishEnvironment) de publicación, debe configurar Dispatcher para que deniegue el acceso a `/etc/reports` los visitantes externos.

### Restricción de cadenas de Consulta {#restricting-query-strings}

Desde Dispatcher versión 4.1.5, utilice la `/filter` sección para restringir las cadenas de consulta. Se recomienda encarecidamente permitir explícitamente cadenas de consulta y excluir la asignación genérica mediante elementos `allow` de filtro.

Una sola entrada puede tener *glob* o alguna combinación de *método*,*url*,*consulta* y *versión* , pero no ambos. El ejemplo siguiente permite la cadena de `a=*` consulta y deniega todas las demás cadenas de consulta para las direcciones URL que se dirigen al `/etc` nodo:

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>Si una regla contiene un `/query`, sólo coincidirá con las solicitudes que contengan una cadena de consulta y coincidirá con el patrón de consulta proporcionado.
>
>En el ejemplo anterior, si también se deben permitir solicitudes a `/etc` que no tengan una cadena de consulta, se requerirán las siguientes reglas:


```xml
/filter {  
>/0001 { /type "deny" /method “*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type “deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Comprobación de la seguridad del despachante {#testing-dispatcher-security}

Los filtros de Dispatcher deben bloquear el acceso a las siguientes páginas y secuencias de comandos en las instancias de publicación de AEM. Utilice un explorador Web para intentar abrir las páginas siguientes como lo haría un visitante del sitio y comprobar que se devuelve un código 404. Si se obtiene algún otro resultado, ajuste sus filtros.

Tenga en cuenta que debe ver el procesamiento normal de la página para /content/add_valid_page.html?debug=layout.


* /administrador
* /system/console
* /dav/crx.default
* /crx
* /bin/crxde/logs
* /jcr:system/jcr:versionStorage.json
* /_jcr_system/_jcr_versionStorage.json
* /libs/wcm/core/content/siteadmin.html
* /libs/collab/core/content/admin.html
* /libs/cq/ui/content/dumplibs.html
* /var/linkchecker.html
* /etc/linkchecker.html
* /home/users/a/admin/profile.json
* /home/users/a/admin/profile.xml
* /libs/cq/core/content/login.json
* ../libs/foundation/components/text/text.jsp
* /content/.{./libs/foundation/components/text/text.jsp
* /apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata
* /libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet
* /content.pages.json
* /content.languages.json
* /content.blueprint.json
* /content.-1.json
* /content.10.json
* /content.infinity.json
* /content.tidy.json
* /content.tidy.-1.blubber.json
* /content/dam.tidy.-100.json
* /content/content/geometrixx.sitemap.txt
* /content/add_valid_page.query.json?state=//*
* /content/add_valid_page.qu%65ry.js%6Fn?state=//*
* /content/add_valid_page.query.json?state=//*[@TransportPassword]/(@TransportPassword%20|%20@transportUri%20|%20@transportUser)
* /content/add_valid_path_to_a_page/_jcr_content.json
* /content/add_valid_path_to_a_page/jcr:content.json
* /content/add_valid_path_to_a_page/_jcr_content.feed
* /content/add_valid_path_to_a_page/jcr:content.feed
* /content/add_valid_path_to_a_page/pagename._jcr_content.feed
* /content/add_valid_path_to_a_page/pagename.jcr:content.feed
* /content/add_valid_path_to_a_page/pagename.docview.xml
* /content/add_valid_path_to_a_page/pagename.docview.json
* /content/add_valid_path_to_a_page/pagename.sysview.xml
* /etc.xml
* /content.feed.xml
* /content.rss.xml
* /content.feed
* /content/add_valid_page.html?debug=layout
* /projects
* /tagging
* /etc/replication.html
* /etc/cloudservices.html
* /bienvenido

Ejecute el siguiente comando en un terminal o símbolo del sistema para determinar si el acceso de escritura anónima está habilitado. No debería poder escribir datos en el nodo.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Ejecute el siguiente comando en un terminal o símbolo del sistema para intentar invalidar la caché de Dispatcher y asegúrese de recibir una respuesta de código 404:

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Habilitación del acceso a las direcciones URL de vanidad {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The "com.adobe.granite.dispatcher.vanityurl.content" package needs to be made public before publishing this contnet.</p>
 -->

Configure Dispatcher para habilitar el acceso a las direcciones URL personales configuradas para las páginas de CQ o AEM.

Cuando se habilita el acceso a las direcciones URL personales, Dispatcher llama periódicamente a un servicio que se ejecuta en la instancia de procesamiento para obtener una lista de direcciones URL personales. Dispatcher almacena esta lista en un archivo local. Cuando se deniega una solicitud de una página debido a un filtro en la `/filter` sección, Dispatcher consulta la lista de las direcciones URL personales. Si la URL denegada está en la lista, Dispatcher permite el acceso a la URL de vanidad.

Para habilitar el acceso a las direcciones URL personales, agregue una `/vanity_urls` sección a la `/farms` sección, similar al siguiente ejemplo:

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

La `/vanity_urls` sección contiene las siguientes propiedades:

* `/url`:: Ruta al servicio de URL de vanidad que se ejecuta en la instancia de procesamiento. El valor de esta propiedad debe ser `"/libs/granite/dispatcher/content/vanityUrls.html"`.

* `/file`:: Ruta al archivo local donde Dispatcher almacena la lista de las direcciones URL personales. Asegúrese de que Dispatcher tiene acceso de escritura a este archivo.
* `/delay`:: (Segundos) El tiempo entre llamadas al servicio de URL personal.

>[!NOTE]
>
>Si su procesamiento es una instancia de AEM, debe instalar el paquete [VanityURLS-Components](https://www.adobeaemcloud.com/content/marketplace/marketplaceProxy.html?packagePath=/content/companies/public/adobe/packages/cq600/component/vanityurls-components) para instalar el servicio de URL de vanidad. (Consulte [Inicio de sesión en Package Share](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/package-manager.html#SigningIntoPackageShare)).

Utilice el siguiente procedimiento para habilitar el acceso a las direcciones URL personales.

1. Si el servicio de procesamiento es una instancia de AEM, instale el paquete com.adobe.granite.dispatcher.vanityurl.content en la instancia de publicación (consulte la nota anterior).
1. Por cada URL de vanidad que haya configurado para una página de AEM o CQ, asegúrese de que la configuración ` [/filter](dispatcher-configuration.md#main-pars_134_32_0009)` deniegue la URL. Si es necesario, agregue un filtro que deniegue la dirección URL.
1. Añada la `/vanity_urls` sección siguiente `/farms`.
1. Reinicie el servidor web Apache.

## Reenvío de solicitudes de distribución - /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

Normalmente, las solicitudes de distribución solo están destinadas a Dispatcher, por lo que de forma predeterminada no se envían al procesador (por ejemplo, una instancia de AEM).

Si es necesario, establezca la propiedad /propagateSyndPost en &quot;1&quot; para reenviar solicitudes de distribución a Dispatcher. Si se establece, debe asegurarse de que las solicitudes POST no se denieguen en la sección de filtros.

## Configuración de la caché de Dispatcher - /cache {#configuring-the-dispatcher-cache-cache}

La `/cache` sección controla la forma en que Dispatcher almacena en caché los documentos. Configure varias subpropiedades para implementar las estrategias de almacenamiento en caché:


* /docroot
* /statfile
* /serveStaleOnError
* /allowAuthorized
* /reglas
* /statarchivoslevel
* /invalidate
* /invalidateHandler
* /allowClients
* /ignoreUrlParams
* /headers
* /mode
* /GracePeriod
* /enableTTL


Una sección de caché de ejemplo podría tener el siguiente aspecto:

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
>Para almacenar en caché los permisos, consulte [Almacenamiento en caché de contenido](permissions-cache.md)seguro.

### Especificación del directorio de caché {#specifying-the-cache-directory}

La `/docroot` propiedad identifica el directorio en el que se almacenan los archivos en caché.

>[!NOTE]
>
>El valor debe ser exactamente la misma ruta que la raíz de documento del servidor web para que Dispatcher y el servidor web gestionen los mismos archivos.\
>El servidor web es responsable de entregar el código de estado correcto cuando se utiliza el archivo de caché del despachante, por eso es importante que también lo encuentre.

Si utiliza varios campos, cada granja debe utilizar una raíz de documento diferente.

### Asignación de nombres a los archivos de estado {#naming-the-statfile}

La `/statfile` propiedad identifica el archivo que se va a utilizar como archivo de estado. Dispatcher utiliza este archivo para registrar la hora de la actualización de contenido más reciente. El archivo de estado puede ser cualquier archivo del servidor web.

El archivo de estado no tiene contenido. Cuando se actualiza el contenido, Dispatcher actualiza la marca de tiempo. El archivo de estado predeterminado se denomina .stat y se almacena en docroot. Dispatcher bloquea el acceso al archivo de estado.

>[!NOTE]
>
>Si `/statfileslevel` está configurado, Dispatcher ignora la `/statfile` propiedad y utiliza .stat como nombre.

### Servir Documentos antiguos cuando se producen errores {#serving-stale-documents-when-errors-occur}

La `/serveStaleOnError` propiedad controla si Dispatcher devuelve documentos invalidados cuando el servidor de procesamiento devuelve un error. De forma predeterminada, cuando se toca un archivo de estado e invalida el contenido almacenado en caché, Dispatcher elimina el contenido almacenado en caché la próxima vez que se solicite.

Si `/serveStaleOnError` se establece en &quot;1&quot;, Dispatcher no elimina el contenido invalidado de la caché a menos que el servidor de procesamiento devuelva una respuesta correcta. Una respuesta 5xx de AEM o un tiempo de espera de conexión hace que Dispatcher proporcione el contenido obsoleto y responda con y HTTP Status de 111 (error de revalidación).

### Almacenamiento en caché cuando se utiliza la autenticación {#caching-when-authentication-is-used}

La `/allowAuthorized` propiedad controla si se almacenan en caché las solicitudes que contienen cualquiera de la siguiente información de autenticación:

* The `authorization` header.
* Una cookie denominada `authorization`.
* Una cookie denominada `login-token`.

De forma predeterminada, las solicitudes que incluyen esta información de autenticación no se almacenan en caché porque la autenticación no se realiza cuando se devuelve un documento en caché al cliente. Esta configuración evita que Dispatcher ofrezca documentos en caché a usuarios que no tienen los derechos necesarios.

Sin embargo, si sus requisitos permiten el almacenamiento en caché de documentos autenticados, establezca /allowAuthorized en uno:

`/allowAuthorized "1"`

>[!NOTE]
>
>Para habilitar la administración de sesiones (mediante la `/sessionmanagement` propiedad), la `/allowAuthorized` propiedad debe establecerse en `"0"`.

### Especificación de los Documentos que se van a almacenar en caché {#specifying-the-documents-to-cache}

La `/rules` propiedad controla qué documentos se almacenan en caché según la ruta de documento. Independientemente de la propiedad /rules, Dispatcher nunca almacena en caché un documento en las siguientes circunstancias:

* Si el URI de la solicitud contiene el signo de interrogación (&quot;?&quot;).\
   Esto generalmente indica una página dinámica, como un resultado de búsqueda que no necesita almacenarse en caché.
* Si falta la extensión del archivo.\
   El servidor web necesita la extensión para determinar el tipo de documento (el tipo MIME).
* Si el encabezado de autenticación está establecido (esto se puede configurar)
* Si la instancia de AEM responde con los siguientes encabezados:

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>Dispatcher puede almacenar en caché los métodos GET o HEAD (para el encabezado HTTP). For additional information on response header caching, see the [Caching HTTP Response Headers](dispatcher-configuration.md#caching-http-response-headers) section.

Cada elemento de la propiedad /rules incluye un patrón [glob](#designing-patterns-for-glob-properties) y un tipo:

* El patrón glob se utiliza para coincidir con la ruta del documento.
* El tipo indica si se almacenarán en caché los documentos que coinciden con el patrón de glob. El valor puede ser allow (para almacenar en caché el documento) o Denise (para procesar siempre el documento).

Si no tiene páginas dinámicas (más allá de las ya excluidas por las reglas anteriores), puede configurar Dispatcher para que almacene todo en caché. La sección de reglas de esta página tiene el siguiente aspecto:

```xml
/rules
  { 
    /0000  {  /glob "*"   /type "allow" }
  }
```

Para obtener más información sobre las propiedades de gloob, consulte [Diseño de patrones para las propiedades](#designing-patterns-for-glob-properties)de gob.

Si hay algunas secciones de la página que son dinámicas (por ejemplo, una aplicación de noticias) o dentro de un grupo de usuarios cerrado, puede definir excepciones:

>[!NOTE]
>
>Los grupos de usuarios cerrados no deben almacenarse en la caché, ya que los derechos de usuario no se comprueban en las páginas en caché.

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

### Invalidación de archivos por nivel de carpeta {#invalidating-files-by-folder-level}

Utilice la `/statfileslevel` propiedad para invalidar los archivos en caché según su ruta de acceso:

* Dispatcher crea `.stat`archivos en cada carpeta desde la carpeta docroot hasta el nivel especificado. La carpeta docroot es el nivel 0.
* Los archivos se invalidan tocando el `.stat` archivo. La fecha de la última modificación del `.stat` archivo se compara con la fecha de la última modificación de un documento en caché. El documento se recupera si el `.stat` archivo es más reciente.

* Cuando se invalida un archivo ubicado en un determinado nivel, se tocarán **todos** los archivos desde docroot `.stat` hasta **el nivel del archivo invalidado o el configurado** `statsfilevel` (el que sea más pequeño).

   * Por ejemplo: si establece la `statfileslevel` propiedad en 6 y se invalida un archivo en el nivel 5, se tocará cada `.stat` archivo de docroot a 5. Continuando con este ejemplo, si un archivo se invalida en el nivel 7, entonces cada . `stat` desde docroot hasta 6 se tocará (desde `/statfileslevel = "6"`).

Solo se ven afectados los recursos **a lo largo de la ruta** al archivo invalidado. Considere el siguiente ejemplo: un sitio web utiliza la estructura `/content/myWebsite/xx/.` Si se establece `statfileslevel` como 3, se crea un `.stat`archivo de la siguiente manera:

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

Cuando se invalida un archivo en `/content/myWebsite/xx` , todos los archivos `.stat` de docroot hasta `/content/myWebsite/xx`se tocan. Este sería el caso únicamente `/content/myWebsite/xx` y no por ejemplo `/content/myWebsite/yy` o `/content/anotherWebSite`.

>[!NOTE]
>
>La invalidación se puede evitar enviando un encabezado adicional `CQ-Action-Scope:ResourceOnly`. Se puede utilizar para vaciar recursos concretos sin invalidar otras partes de la caché. Consulte [esta página](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) y [Invalidación manual de la caché](https://helpx.adobe.com/experience-manager/dispatcher/using/page-invalidate.html) de despachantes para obtener más detalles.

>[!NOTE]
>
>Si especifica un valor para la `/statfileslevel` propiedad, se omite la `/statfile` propiedad.

### Invalidación automática de archivos en caché {#automatically-invalidating-cached-files}

La `/invalidate` propiedad define los documentos que se invalidan automáticamente al actualizar el contenido.

Con la invalidación automática, Dispatcher no elimina los archivos en caché después de una actualización de contenido, sino que comprueba su validez cuando se solicitan por primera vez. Los Documentos de la caché que no se invalidan automáticamente permanecerán en la caché hasta que una actualización de contenido los elimine explícitamente.

La invalidación automática se utiliza generalmente para las páginas HTML. Las páginas HTML suelen contener vínculos a otras páginas, lo que dificulta la determinación de si una actualización de contenido afecta a una página. Para asegurarse de que todas las páginas relevantes se invalidan cuando se actualiza el contenido, invalide automáticamente todas las páginas HTML. La siguiente configuración invalida todas las páginas HTML:

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

Para obtener más información sobre las propiedades de gloob, consulte [Diseño de patrones para las propiedades](#designing-patterns-for-glob-properties)de gob.

Esta configuración provoca la siguiente actividad cuando se activa /content/geometrixx/en:

* Todos los archivos con pattern en.* se eliminan de la carpeta /content/geometrixx/.
* Se elimina la carpeta /content/geometrixx/en/_jcr_content.
* Los demás archivos que coinciden con la configuración /invalidate no se eliminan inmediatamente. Estos archivos se eliminan cuando se produce la siguiente solicitud. En nuestro ejemplo /content/geometrixx.html no se elimina, se eliminará cuando se solicite /content/geometrixx.html.

Si oferta archivos PDF y ZIP generados automáticamente para descargarlos, es posible que también tenga que invalidarlos automáticamente. Un ejemplo de configuración tiene el siguiente aspecto:

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

La integración de AEM con Adobe Analytics ofrece datos de configuración en un archivo analytics.sitecatalyst.js de su sitio web. El archivo dispatcher.any de ejemplo que se proporciona con Dispatcher incluye la siguiente regla de invalidación para este archivo:

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Uso de secuencias de comandos de invalidación personalizadas {#using-custom-invalidation-scripts}

La propiedad /invalidateHandler permite definir una secuencia de comandos que se llama para cada solicitud de invalidación recibida por Dispatcher.

Se llama con los siguientes argumentos:

* Control\
   La ruta de contenido que se invalida
* Acción\
   Acción de replicación (por ejemplo, Activar, Desactivar)
* Ámbito de acción\
   El ámbito de la acción de replicación (vacío, a menos que `CQ-Action-Scope: ResourceOnly` se envíe un encabezado, consulte [Invalidación de páginas en caché desde AEM](page-invalidate.md) para obtener más detalles)

Se puede utilizar para cubrir una serie de casos de uso diferentes, como invalidar otras memorias caché específicas de la aplicación, o para tratar casos en los que la dirección URL externalizada de una página y su lugar en docroot no coinciden con la ruta de contenido.

A continuación, la secuencia de comandos de ejemplo registra cada solicitud invalidada en un archivo.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### script de controlador de invalidación de muestra {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Limitación de los clientes que pueden vaciar la caché {#limiting-the-clients-that-can-flush-the-cache}

La propiedad /allowClients define los clientes específicos a los que se permite vaciar la caché. Los patrones de globalización se comparan con la IP.

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

Para obtener más información sobre las propiedades de gloob, consulte [Diseño de patrones para las propiedades](#designing-patterns-for-glob-properties)de gob.

>[!CAUTION]
>
>Se recomienda definir el /allowClients.
>
>Si esto no se hace, cualquier cliente puede emitir una llamada para borrar la caché; si esto se hace repetidamente, puede afectar seriamente el rendimiento del sitio.

### Ignorar parámetros de URL {#ignoring-url-parameters}

La `ignoreUrlParams` sección define qué parámetros de URL se omiten al determinar si una página se almacena en caché o se envía desde la caché:

* Cuando una dirección URL de solicitud contiene parámetros que se omiten, la página se almacena en caché.
* Cuando una dirección URL de solicitud contiene uno o varios parámetros que no se omiten, la página no se almacena en caché.

Cuando se omite un parámetro para una página, ésta se almacena en la caché la primera vez que se solicita. Las solicitudes posteriores para la página se proporcionan en la página en caché, independientemente del valor del parámetro en la solicitud.

Para especificar qué parámetros se omiten, agregue reglas de gob a la `ignoreUrlParams` propiedad:

* Para ignorar un parámetro, cree una propiedad glob que permita el parámetro.
* Para evitar que la página se almacene en caché, cree una propiedad glob que deniegue el parámetro.

El ejemplo siguiente hace que Dispatcher ignore el parámetro &quot;q&quot;, de modo que las direcciones URL de solicitud que incluyen el parámetro q se almacenen en caché:

```xml
/ignoreUrlParams
{
    /0001 { /glob "*" /type "deny" }
    /0002 { /glob "q" /type "allow" }
}
```

Con el valor de ejemplo `ignoreUrlParams` , la siguiente solicitud HTTP hace que la página se almacene en caché porque se omite el `q` parámetro:

```xml
GET /mypage.html?q=5
```

Con el valor de ejemplo `ignoreUrlParams` , la siguiente solicitud HTTP hace que la página **no se almacene** en caché porque no se ignora el `p` parámetro:

```xml
GET /mypage.html?q=5&p=4
```

Para obtener más información sobre las propiedades de gloob, consulte [Diseño de patrones para las propiedades](#designing-patterns-for-glob-properties)de gob.

### Almacenamiento en caché de encabezados de respuesta HTTP {#caching-http-response-headers}

>[!NOTE]
>
>Esta función está disponible con la versión **4.1.11** del despachante.

La `/headers` propiedad permite definir los tipos de encabezados HTTP que va a almacenar en caché el despachante. En la primera solicitud a un recurso sin almacenar en caché, todos los encabezados que coincidan con uno de los valores configurados (consulte el ejemplo de configuración siguiente) se almacenan en un archivo independiente, junto al archivo de caché. En solicitudes posteriores al recurso almacenado en caché, los encabezados almacenados se agregan a la respuesta.

A continuación se presenta un ejemplo de la configuración predeterminada:

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
>Además, tenga en cuenta que no se permiten caracteres de globalización de archivos. Para obtener más información, consulte [Diseño de patrones para propiedades](#designing-patterns-for-glob-properties)de gob.

>[!NOTE]
>
>Si necesita que Dispatcher almacene y envíe encabezados de respuesta ETag desde AEM, haga lo siguiente:
>
>* Añada el nombre del encabezado en la `/cache/headers`sección.
>* Añada la siguiente directiva [](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) Apache en la sección relacionada con Dispatcher:
>



```xml
FileETag none
```

### Permisos de archivos de caché de Dispatcher {#dispatcher-cache-file-permissions}

La `mode` propiedad especifica los permisos de archivo que se aplican a los nuevos directorios y archivos de la caché. Esta configuración está restringida por el `umask` proceso de llamada. Es un número octal construido a partir de la suma de uno o más de los siguientes valores:

* 0400 Permitir lectura por propietario.
* 0200 Permitir escritura por propietario.
* 0100 Permite al propietario buscar en directorios.
* 0040 Permitir lectura por miembros del grupo.
* 0020 Permitir escritura por miembros del grupo.
* 0010 Permitir que los miembros del grupo busquen en el directorio.
* 0004 Permitir que otros lean.
* 0002 Permitir escritura de otros.
* 0001 Permitir que otros usuarios busquen en el directorio.

El valor predeterminado es 0755, que permite al propietario leer, escribir o buscar y al grupo y a otros usuarios leer o buscar.

### Tocación del archivo .stat {#throttling-stat-file-touching}

Con la propiedad predeterminada `/invalidate` , cada activación invalida todos `.html` los archivos de forma efectiva (cuando su ruta de acceso coincide con la `/invalidate` sección). En un sitio web con tráfico considerable, varias activaciones subsiguientes incrementarán la carga de la CPU en el servidor. En tal caso, sería deseable &quot;acelerar&quot; el contacto de archivos `.stat` para mantener el sitio web adaptable. Para ello, utilice la `/gracePeriod` propiedad .

La `/gracePeriod` propiedad define el número de segundos que un recurso antiguo e invalidado automáticamente puede seguir sirviéndose desde la caché después de la última activación que se está produciendo. La propiedad se puede utilizar en una configuración en la que un lote de activaciones invalidaría repetidamente la caché completa. El valor recomendado es de 2 segundos.

Para obtener más información, lea también las `/invalidate` secciones y `/statfileslevel`secciones anteriores.

### Configuración de la invalidación de caché basada en tiempo - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

Si se establece, la `/enableTTL` propiedad evaluará los encabezados de respuesta desde el servidor y, si contienen una `Cache-Control` `Expires` antigüedad o una fecha máxima, se creará un archivo auxiliar vacío junto al archivo de caché, con una hora de modificación igual a la fecha de caducidad. Cuando se solicita el archivo en caché más allá del tiempo de modificación, se vuelve a solicitar automáticamente desde el servidor.

>[!NOTE]
>
>Esta función está disponible en la versión **4.1.11** o posterior del despachante.

## Configuración del equilibrio de carga - /statistics {#configuring-load-balancing-statistics}

La `/statistics` sección define categorías de archivos para los que Dispatcher puntúa la respuesta de cada procesamiento. Dispatcher utiliza las puntuaciones para determinar qué procesamiento enviar una solicitud.

Cada categoría que cree define un patrón de gob. Dispatcher compara el URI del contenido solicitado con estos patrones para determinar la categoría del contenido solicitado:

* El orden de las categorías determina el orden en que se comparan con el URI.
* El primer patrón de categoría que coincide con el URI es la categoría del archivo. No se evalúan más patrones de categoría.

Dispatcher admite un máximo de 8 categorías estadísticas. Si define más de 8 categorías, solo se utilizarán las 8 primeras.

**Representar selección**

Cada vez que Dispatcher requiere una página representada, utiliza el siguiente algoritmo para seleccionar el procesamiento:

1. Si la solicitud contiene el nombre de procesamiento en una `renderid` cookie, Dispatcher utiliza ese procesamiento.
1. Si la solicitud no incluye ninguna `renderid` cookie, Dispatcher compara las estadísticas de procesamiento:

   1. Dispatcher determina la categoría del URI de solicitud.
   1. Dispatcher determina qué procesamiento tiene la puntuación de respuesta más baja para esa categoría y selecciona ese procesamiento.

1. Si todavía no se ha seleccionado ningún procesamiento, utilice el primer procesamiento de la lista.

La puntuación de la categoría de un procesamiento se basa en los tiempos de respuesta anteriores, así como en las conexiones exitosas y fallidas anteriores que Dispatcher intenta realizar. Para cada intento, se actualiza la puntuación de la categoría del URI solicitado.

>[!NOTE]
>
>Si no utiliza el equilibrio de carga, puede omitir esta sección.

### Definición de Categorías de Estadísticas {#defining-statistics-categories}

Defina una categoría para cada tipo de documento para el que desee mantener estadísticas para la selección de procesamiento. La sección /statistics contiene una sección /categorías. Para definir una categoría, agregue una línea debajo de la sección /categorías con el siguiente formato:

`/name { /glob "pattern"}`

La categoría `name` debe ser exclusiva de la granja. El `pattern` se describe en la sección Patrones de [diseño para propiedades](#designing-patterns-for-glob-properties) de gob.

Para determinar la categoría de un URI, Dispatcher compara el URI con cada patrón de categoría hasta que se encuentra una coincidencia. Dispatcher comienza con la primera categoría de la lista y continúa en orden. Por lo tanto, coloque primero categorías con patrones más específicos.

Por ejemplo, Dispatcher el archivo dispatcher.any predeterminado define una categoría HTML y otra categoría. La categoría HTML es más específica, por lo que aparece primero:

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

El siguiente ejemplo también incluye una categoría para las páginas de búsqueda:

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

### Reflejo de la falta de disponibilidad del servidor en las estadísticas de Dispatcher {#reflecting-server-unavailability-in-dispatcher-statistics}

La `/unavailablePenalty` propiedad establece el tiempo (en décimas de segundo) que se aplica a las estadísticas de procesamiento cuando falla una conexión al procesamiento. Dispatcher agrega la hora a la categoría de estadísticas que coincide con el URI solicitado.

Por ejemplo, la penalización se aplica cuando no se puede establecer la conexión TCP/IP con el nombre de host/puerto designado, ya sea porque AEM no se está ejecutando (y no escucha) o debido a un problema relacionado con la red.

La `/unavailablePenalty` propiedad es un elemento secundario directo de la `/farm` sección (un elemento secundario de la `/statistics` sección).

Si no existe ninguna `/unavailablePenalty` propiedad, se utiliza un valor de &quot;1&quot;.

```xml
/unavailablePenalty "1"
```

## Identificación de una carpeta de conexión fija - /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

La `/stickyConnectionsFor` propiedad define una carpeta que contiene documentos adhesivos; se accederá a esto mediante la dirección URL. Dispatcher envía todas las solicitudes de un solo usuario que se encuentren en esta carpeta a la misma instancia de procesamiento. Las conexiones fijas garantizan que los datos de la sesión estén presentes y sean coherentes para todos los documentos. Este mecanismo utiliza la `renderid` cookie.

El ejemplo siguiente define una conexión fija a la carpeta /products:

```xml
/stickyConnectionsFor "/products"
```

Cuando una página está compuesta de contenido de varios nodos de contenido, incluya la `/paths` propiedad que lista las rutas al contenido. Por ejemplo, una página contiene contenido de `/content/image`, `/content/video`y `/var/files/pdfs`. La siguiente configuración habilita las conexiones adhesivas para todo el contenido de la página:

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

Cuando las conexiones adhesivas están habilitadas, el módulo del despachante establece la `renderid` cookie. Esta cookie no tiene el `httponly` indicador, que debe agregarse para mejorar la seguridad. Puede hacerlo estableciendo la `httpOnly` propiedad en el `/stickyConnections` nodo de un archivo de `dispatcher.any` configuración. El valor de la propiedad (0 o 1) define si la `renderid` cookie tiene el `HttpOnly` atributo anexado. El valor predeterminado es 0, lo que significa que no se agregará el atributo.

Para obtener información adicional sobre el `httponly` indicador, lea [esta página](https://www.owasp.org/index.php/HttpOnly).

### secure {#secure}

Cuando las conexiones adhesivas están habilitadas, el módulo del despachante establece la `renderid` cookie. Esta cookie no tiene el indicador **seguro** , que debe agregarse para mejorar la seguridad. Puede hacerlo estableciendo la `secure` propiedad en el `/stickyConnections` nodo de un archivo de `dispatcher.any` configuración. El valor de la propiedad (0 o 1) define si la `renderid` cookie tiene el `secure` atributo anexado. El valor predeterminado es 0, lo que significa que el atributo se agregará **si** la solicitud entrante es segura. Si el valor se establece en 1, se agregará el indicador seguro independientemente de si la solicitud entrante es segura o no.

## Gestión de errores de conexión de procesamiento {#handling-render-connection-errors}

Configure el comportamiento de Dispatcher cuando el servidor de procesamiento devuelve un error 500 o no está disponible.

### Especificación de una página de comprobación de estado {#specifying-a-health-check-page}

Utilice la `/health_check` propiedad para especificar una URL que se compruebe cuando se produce un código de estado 500. Si esta página también devuelve un código de estado 500, la instancia se considera no disponible y se aplica una penalización de tiempo configurable ( `/unavailablePenalty`) al procesamiento antes de volver a intentarlo.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### Especificación del retraso en el reintento de página {#specifying-the-page-retry-delay}

La propiedad / `retryDelay` establece el tiempo (en segundos) que el despachante espera entre rondas de intentos de conexión con los procesamientos del conjunto de servidores. Para cada ronda, el número máximo de veces que Dispatcher intenta establecer una conexión con un procesamiento es el número de representaciones en el conjunto de servidores.

Dispatcher utiliza un valor de `"1"` si no `/retryDelay` se define explícitamente. El valor predeterminado es adecuado en la mayoría de los casos.

```xml
/retryDelay "1"
```

### Configuración del número de Reintentos {#configuring-the-number-of-retries}

La `/numberOfRetries` propiedad establece el número máximo de rondas de intentos de conexión que Dispatcher realiza con los procesamientos. Si Dispatcher no se puede conectar correctamente a un procesamiento después de este número de reintentos, Dispatcher devuelve una respuesta incorrecta.

Para cada ronda, el número máximo de veces que Dispatcher intenta establecer una conexión con un procesamiento es el número de representaciones en el conjunto de servidores. Por lo tanto, el número máximo de veces que Dispatcher intenta establecer una conexión es ( `/numberOfRetries`) x (el número de procesamientos).

Si el valor no se define explícitamente, el valor predeterminado es **5**.

```xml
/numberOfRetries "5"
```

### Uso del mecanismo de conmutación por error {#using-the-failover-mechanism}

Habilite el mecanismo de conmutación por error en la granja de Dispatcher para reenviar solicitudes a diferentes representaciones cuando falle la solicitud original. Cuando la conmutación por error está habilitada, Dispatcher tiene el siguiente comportamiento:

* Cuando una solicitud a un procesamiento devuelve el estado HTTP 503 (NO DISPONIBLE), Dispatcher envía la solicitud a otro procesamiento.
* Cuando una solicitud a un procesamiento devuelve el estado HTTP 50x (distinto de 503), Dispatcher envía una solicitud para la página configurada para la `health_check` propiedad.

   * Si la comprobación de estado devuelve 500 (INTERNAL_SERVER_ERROR), Dispatcher envía la solicitud original a un procesamiento diferente.
   * Si la comprobación de estado devuelve el estado HTTP 200, Dispatcher devuelve el error HTTP 500 inicial al cliente.

Para habilitar la conmutación por error, agregue la línea siguiente a la granja (o sitio web):

```xml
/failover "1" 
```

>[!NOTE]
>
>Para reintentar solicitudes HTTP que contienen un cuerpo, Dispatcher envía un encabezado de `Expect: 100-continue` solicitud al procesamiento antes de colocar en cola el contenido real. CQ 5.5 con CQSE responde inmediatamente con 100 (CONTINUE) o un código de error. Otros contenedores servlet también deberían admitir esto.

## Ignorar errores de interrupción - /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Esta opción no suele ser necesaria. Solo debe utilizarlo cuando vea los siguientes mensajes de registro:
>
>`Error while reading response: Interrupted system call`

Cualquier llamada al sistema orientada al sistema de archivos se puede interrumpir `EINTR` si el objeto de la llamada al sistema se encuentra en un sistema remoto al que se accede mediante NFS. El tiempo de espera o la interrupción de estas llamadas del sistema se basa en la forma en que el sistema de archivos subyacente se montó en el equipo local.

Utilice el parámetro /ignoreEINTR si la instancia tiene dicha configuración y el registro contiene el siguiente mensaje:

`Error while reading response: Interrupted system call`

Internamente, Dispatcher lee la respuesta del servidor remoto (es decir, AEM) utilizando un bucle que puede representarse como:

`while (response not finished) {  
read more data  
}`

Estos mensajes pueden generarse cuando `EINTR` se producen en la sección &quot; `read more data`&quot; y se deben a la recepción de una señal antes de recibir datos.

Para ignorar estas interrupciones, puede agregar el siguiente parámetro a `dispatcher.any` (antes `/farms`):

`/ignoreEINTR "1"`

La configuración `/ignoreEINTR` en `"1"` hace que Dispatcher continúe intentando leer datos hasta que se lea la respuesta completa. El valor predeterminado es 0 y desactiva la opción.

## Diseño de patrones para propiedades de gob {#designing-patterns-for-glob-properties}

Varias secciones del archivo de configuración de Dispatcher utilizan `glob` propiedades como criterios de selección para solicitudes de cliente. Los valores de las propiedades glob son patrones que Dispatcher compara con un aspecto de la solicitud, como la ruta del recurso solicitado o la dirección IP del cliente. Por ejemplo, los elementos de la `/filter` sección utilizan patrones de glob para identificar las rutas de las páginas en las que Dispatcher actúa o rechaza.

Los valores de los gob pueden incluir caracteres comodín y caracteres alfanuméricos para definir el patrón.

| Carácter comodín | Descripción | Ejemplos |
|--- |--- |--- |
| `*` | Coincide con cero o más instancias contiguas de cualquier carácter de la cadena. El carácter final de la coincidencia está determinado por cualquiera de las situaciones siguientes: <br/>Un carácter de la cadena coincide con el siguiente carácter del patrón y éste tiene las siguientes características:<br/><ul><li>No es *</li><li>¿No es un ?</li><li>Un carácter literal (incluido un espacio) o una clase de caracteres.</li><li>Se llega al final del patrón.</li></ul>Dentro de una clase de caracteres, el carácter se interpreta literalmente. | `*/geo*` Coincide con cualquier página debajo del `/content/geometrixx` nodo y del `/content/geometrixx-outdoors` nodo. Las siguientes solicitudes HTTP coinciden con el patrón glob: <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*` <br/>Coincide con cualquier página debajo del `/content/geometrixx-outdoors` nodo. Por ejemplo, la siguiente solicitud HTTP coincide con el patrón glob: <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Coincide con cualquier carácter individual. Utilice clases de caracteres externos. Dentro de una clase de caracteres, este carácter se interpreta literalmente. | `*outdoors/??/*`<br/> Coincide con las páginas de cualquier idioma del sitio de Geometrixx-outdoors. Por ejemplo, la siguiente solicitud HTTP coincide con el patrón glob: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>La siguiente solicitud no coincide con el patrón de glob: <br/><ul><li>&quot;GET /content/geometrixx-outdoors/en.html&quot;</li></ul> |
| `[ and ]` | Marca el principio y el final de una clase de caracteres. Las clases de caracteres pueden incluir uno o varios rangos de caracteres y caracteres únicos.<br/>Se produce una coincidencia si el carácter de destinatario coincide con alguno de los caracteres de la clase de caracteres o dentro de un rango definido.<br/>Si no se incluye el soporte de cierre, el patrón no produce coincidencias. | `*[o]men.html*`<br/> Coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*` <br/>Coincide con las siguientes solicitudes HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Indica un rango de caracteres. Para su uso en clases de caracteres.  Fuera de una clase de caracteres, este carácter se interpreta literalmente. | `*[m-p]men.html*` Coincide con la siguiente solicitud HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Anula la clase de caracteres o caracteres que sigue. Se utiliza solo para negar caracteres e intervalos de caracteres dentro de clases de caracteres. Equivalente al `^ wildcard`. <br/>Fuera de una clase de caracteres, este carácter se interpreta literalmente. | `*[!o]men.html*`<br/> Coincide con la siguiente solicitud HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>No coincide con la siguiente solicitud HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` o `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Anula el rango de caracteres o caracteres que sigue. Se utiliza para negar solo caracteres e intervalos de caracteres dentro de clases de caracteres. Equivalente al carácter `!` comodín. <br/>Fuera de una clase de caracteres, este carácter se interpreta literalmente. | Se aplican los ejemplos del carácter `!` comodín, sustituyendo los `!` caracteres de los patrones de ejemplo por `^` caracteres. |


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

* Ubicación del archivo de registro de Dispatcher.
* Nivel de registro.

Consulte la documentación del servidor web y el archivo léame de la instancia de Dispatcher para obtener más información.

**Registros rotados/canjeados de Apache**

Si utiliza un servidor web **Apache** , puede utilizar la funcionalidad estándar para los registros rotados y/o canalizados. Por ejemplo, con registros de tubería:

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Esto girará automáticamente:

* el archivo de registro del despachante; con una marca de tiempo en la extensión (logs/dispatcher.log%Y%m%d).
* semanalmente (60 x 60 x 24 x 7 = 604800 segundos).

Consulte la documentación del servidor web Apache sobre Rotación de registro y Registros de tubería; por ejemplo, [Apache 2.4](https://httpd.apache.org/docs/2.4/logs.html).

>[!NOTE]
>
>Tras la instalación, el nivel de registro predeterminado es alto (es decir, nivel 3 = Depuración), de modo que Dispatcher registra todos los errores y advertencias. Esto es muy útil en las etapas iniciales.
>
>Sin embargo, esto requiere recursos adicionales, por lo que cuando el despachante funcione sin problemas *según sus necesidades*, puede(debe) reducir el nivel de registro.

### Seguimiento del registro {#trace-logging}

Entre otras mejoras para Dispatcher, la versión 4.2.0 también incorpora el registro de seguimiento.

Es un nivel superior al de registro de depuración, que muestra información adicional en los registros. Agrega el registro para:

* Los valores de los encabezados reenviados;
* Regla que se está aplicando para una acción determinada.

Puede habilitar el registro de seguimiento estableciendo el nivel de registro en `4` el servidor web.

A continuación se muestra un ejemplo de registros con seguimiento habilitado:

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

## Confirmación de la operación básica {#confirming-basic-operation}

Para confirmar el funcionamiento básico y la interacción del servidor web, Dispatcher y la instancia de AEM pueden seguir los pasos siguientes:

1. Configure el `loglevel` en `3`.

1. Inicio del servidor web; esto también inicio al despachante.
1. Inicio de la instancia de AEM.
1. Compruebe el registro y los archivos de error del servidor web y del despachante.\
   Según el servidor web, debería ver mensajes como:\
   `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured`\
   y:\
   `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Navegue por el sitio web a través del servidor web. Confirme que el contenido se muestra según sea necesario.\
   Por ejemplo, en una instalación local en la que AEM se ejecuta en el puerto `4502` y en el servidor web, `80` acceda a la consola Sitios web mediante:\
   ` https://localhost:4502/libs/wcm/core/content/siteadmin.html  
https://localhost:80/libs/wcm/core/content/siteadmin.html  
`Los resultados deberían ser idénticos. Confirme el acceso a otras páginas con el mismo mecanismo.

1. Compruebe que el directorio de la memoria caché se está llenando.
1. Active una página para comprobar que la caché se está vaciando correctamente.
1. Si todo funciona correctamente, puede reducir el `loglevel` a `0`.

## Uso de varias instancias de Dispatcher {#using-multiple-dispatchers}

En configuraciones complejas, puede utilizar varias instancias de Dispatcher. Por ejemplo, puede utilizar:

* una instancia de Dispatcher para publicar un sitio web en la Intranet
* una segunda instancia de Dispatcher, en una dirección y con una configuración de seguridad diferentes, para publicar el mismo contenido en Internet.

En ese caso, asegúrese de que cada solicitud pasa por una única instancia de Dispatcher. Una instancia de Dispatcher no gestiona solicitudes procedentes de otra instancia de Dispatcher. Por lo tanto, asegúrese de que ambas instancias de Dispatcher acceden directamente al sitio web de AEM.

## Depuración {#debugging}

Al agregar el encabezado `X-Dispatcher-Info` a una solicitud, Dispatcher responde si el destinatario se almacenó en caché, se devolvió desde la caché o no se pudo almacenar en caché. El encabezado de respuesta `X-Cache-Info` contiene esta información en un formato legible. Puede utilizar estos encabezados de respuesta para depurar los problemas relacionados con las respuestas almacenadas en caché por Dispatcher.

Esta funcionalidad no está habilitada de forma predeterminada, por lo que para que se incluya el encabezado de respuesta, el conjunto de servidores debe contener `X-Cache-Info` la siguiente entrada:

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

Además, el `X-Dispatcher-Info` encabezado no necesita un valor, pero si lo utiliza `curl` para realizar pruebas, debe proporcionar un valor para enviar el encabezado, como:

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/we-retail/us/en.html
```

Debajo hay una lista que contiene los encabezados de respuesta que `X-Dispatcher-Info` se devolverán:

* **en caché**\
   El archivo destinatario está contenido en la memoria caché y el despachante ha determinado que es válido entregarlo.
* **almacenamiento en caché**\
   El archivo destinatario no está contenido en la caché y el despachante ha determinado que es válido almacenar en caché la salida y entregarla.
* **almacenamiento en caché: el archivo stat es más reciente** El archivo destinatario está contenido en la caché, pero se invalida por un archivo stat más reciente. El despachante eliminará el archivo destinatario, lo recreará de la salida y lo entregará.
* **no se puede almacenar en caché: sin raíz** de documento La configuración de la granja no contiene una raíz de documento (elemento de configuración `cache.docroot`).
* **no se puede almacenar en caché: la ruta del archivo caché es demasiado larga**\
   El archivo destinatario (la concatenación de la raíz del documento y el archivo URL) supera el nombre de archivo más largo posible del sistema.
* **no se puede almacenar en caché: ruta de archivo temporal demasiado larga**\
   La plantilla de nombre de archivo temporal supera el nombre de archivo más largo posible del sistema. El despachante crea primero un archivo temporal antes de crear o sobrescribir realmente el archivo en caché. El nombre de archivo temporal es el nombre del archivo destinatario con los caracteres `_YYYYXXXXXX` anexados, donde el `Y` `X` y se reemplazará para crear un nombre único.
* **no se puede almacenar en caché: la dirección URL de solicitud no tiene extensión**\
   La dirección URL de la solicitud no tiene extensión o existe una ruta después de la extensión del archivo, por ejemplo: `/test.html/a/path`.
* **no se puede almacenar en caché: solicitud no era GET ni HEAD** El método HTTP no es GET ni HEAD. El despachante supone que el resultado contendrá datos dinámicos que no deben almacenarse en caché.
* **no se puede almacenar en caché: la solicitud contenía una cadena de consulta**\
   La solicitud contenía una cadena de consulta. El despachante supone que el resultado depende de la cadena de consulta proporcionada y, por lo tanto, no se almacena en caché.
* **no se puede almacenar en caché: el administrador de sesiones no se autenticó**\
   La caché del conjunto de servidores está gobernada por un administrador de sesiones (la configuración contiene un `sessionmanagement` nodo) y la solicitud no contenía la información de autenticación adecuada.
* **no se puede almacenar en caché: solicitud contiene autorización**\
   El conjunto de servidores no puede almacenar en caché la salida ( `allowAuthorized 0`) y la solicitud contiene información de autenticación.
* **no se puede almacenar en caché: destinatario es un directorio**\
   El archivo destinatario es un directorio. Esto podría indicar algún error conceptual, en el que una dirección URL y algunas subdirecciones URL contienen resultados que se pueden almacenar en caché; por ejemplo, si una solicitud `/test.html/a/file.ext` llega primero y contiene resultados que se pueden almacenar en caché, el despachante no podrá almacenar en caché el resultado de una solicitud posterior a `/test.html`.
* **no se puede almacenar en caché: la dirección URL de solicitud tiene una barra diagonal final**\
   La dirección URL de la solicitud tiene una barra diagonal final.
* **no se puede almacenar en caché: la dirección URL de solicitud no está en las reglas de caché**\
   Las reglas de caché del conjunto de servidores deniegan explícitamente el almacenamiento en caché del resultado de alguna URL de solicitud.
* **no se puede almacenar en caché: acceso denegado del comprobador de autorización**\
   El comprobador de autorización de la granja denegó el acceso al archivo en caché.
* **no se puede almacenar en caché: sesión no válida** La caché del conjunto de servidores está gobernada por un administrador de sesiones (la configuración contiene un `sessionmanagement` nodo) y la sesión del usuario ya no es válida.
* **no se puede almacenar en caché: contiene`no_cache `**El servidor remoto devolvió un`Dispatcher: no_cache`encabezado, que prohíbe al despachante almacenar en caché la salida.
* **no se puede almacenar en caché: la longitud del contenido de respuesta es cero**. La longitud del contenido de la respuesta es cero; el despachante no creará un archivo de longitud cero.
