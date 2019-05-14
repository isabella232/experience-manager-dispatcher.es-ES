---
title: Configuración de Dispatcher
seo-title: Configuración de Dispatcher
description: Descubra cómo configurar Dispatcher.
seo-description: Descubra cómo configurar Dispatcher.
uuid: 253 ef 0 f 7-2491-4 cec-ab 22-97439 df 29 fd 6
cmgrlastmodified: 01.11.2007 08 22 29 [ahedisoz]
pageversionid: '1193211344162'
topic-tags: dispatcher
content-type: referencia
discoiquuid: aeffee 8 e-bb 34-42 a 7-9 a 5 e-b 7 d 0 e 848391 a
translation-type: tm+mt
source-git-commit: 2f0ca874c23cb7aecbcedc22802c46a295bb4d75

---


# Configuración de Dispatcher{#configuring-dispatcher}

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher incrustada en la documentación de una versión anterior de AEM.

Las secciones siguientes describen cómo configurar distintos aspectos de Dispatcher.

## Compatibilidad con ipv 4 y ipv 6 {#support-for-ipv-and-ipv}

Todos los elementos de AEM y Dispatcher se pueden instalar en redes ipv 4 y ipv 6. Consulte [IPV 4 y IPV 6](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html#AdditionalPlatformNotes).

## Archivos de configuración de Dispatcher {#dispatcher-configuration-files}

De forma predeterminada, la configuración de Dispatcher se almacena en el archivo `dispatcher.any` de texto, aunque puede cambiar el nombre y la ubicación de este archivo durante la instalación.

El archivo de configuración contiene una serie de propiedades de valor único o varios valores que controlan el comportamiento de Dispatcher:

* Los nombres de propiedad tienen un prefijo barra diagonal `/`.
* Las propiedades de varios valores encierran elementos secundarios con llaves `{ }`.

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

* Si el archivo de configuración es grande, puede dividirlo en varios archivos pequeños (que son más fáciles de administrar), y luego incluirlos.
* Para incluir archivos que se generan automáticamente.

Por ejemplo, para incluir el archivo myfarm. any en la /farms, use el siguiente código:

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Utilice el asterisco (&quot; *&quot;) como comodín para especificar una serie de archivos que incluir.

Por ejemplo, si los archivos `farm_1.any` para `farm_5.any` contener la configuración de las granjas de uno a cinco, puede incluirlos de la siguiente manera:

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Uso de variables de entorno {#using-environment-variables}

Puede utilizar variables de entorno en propiedades de valor de cadena en el archivo dispatcher. any en lugar de preprogramar los valores. Para incluir el valor de una variable de entorno, utilice el formato `${variable_name}`.

Por ejemplo, si el archivo dispatcher. any se encuentra en el mismo directorio que el directorio de la memoria caché, se puede utilizar el siguiente valor para la [propiedad docroot](dispatcher-configuration.md#main-pars-title-30) :

```xml
/docroot "${PWD}/cache"
```

Otro ejemplo: si crea una variable de entorno denominada `PUBLISH_IP` que almacena el nombre de host de la instancia de publicación AEM, se puede utilizar la siguiente configuración de [/renders](dispatcher-configuration.md#main-pars-127-25-0008) .

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Asignación de nombre a la instancia Dispatcher {#naming-the-dispatcher-instance-name}

Utilice `/name` la propiedad para especificar un nombre único para identificar su instancia de Dispatcher. `/name` La propiedad es una propiedad de nivel superior en la estructura de configuración.

## Definición de granjas {#defining-farms-farms}

`/farms` La propiedad define uno o varios conjuntos de comportamientos Dispatcher, donde cada conjunto está asociado a sitios web o direcciones URL diferentes. `/farms` La propiedad puede incluir una sola granja o varias granjas:

* Utilice una sola granja cuando desee que Dispatcher gestione todas las páginas Web o sitios Web del mismo modo.
* Cree varias granjas cuando diferentes áreas del sitio Web o distintos sitios Web requieran un comportamiento de Dispatcher diferente.

`/farms` La propiedad es una propiedad de nivel superior en la estructura de configuración. Para definir una granja, agregue una propiedad secundaria a `/farms` la propiedad. Utilice un nombre de propiedad que identifique exclusivamente a la granja dentro de la instancia Dispatcher.

La `/*farmname*` propiedad tiene varios valores y contiene otras propiedades que definen el comportamiento Dispatcher:

* Las direcciones URL de las páginas a las que se aplica la granja.
* Una o más URL de servicio (normalmente de instancias de publicación de AEM) que se utilizarán para procesar documentos.
* Estadísticas que se utilizan para equilibrar varios documentos de equilibrio de carga.
* Hay otros comportamientos, como los que se almacenan en caché y dónde.

El valor puede haber incluido cualquier carácter alfanumérico (a-z, 0-9). El siguiente ejemplo muestra la definición de esqueleto para dos granjas denominadas `/daycom` y `/docsdaycom`:

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
>Si utiliza más de una granja de procesamiento, la lista se evalúa en la parte inferior. Esto es especialmente relevante al definir los hosts [virtuales](dispatcher-configuration.md#main-pars-117-15-0006) para sus sitios web.

Cada propiedad granja puede contener las siguientes propiedades secundarias:

| Nombre de la propiedad  | Descripción |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | Página principal predeterminada (opcional) (IIS solamente) |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | Encabezados de la solicitud HTTP del cliente que se van a pasar. |
| [/virtualhosts](#identifying-virtual-hosts-virtual-hosts) | Los hosts virtuales para esta granja. |
| [/sessionmanagement](#enabling-secure-sessions-session-management) | Compatibilidad con la autenticación y gestión de sesiones. |
| [/renders](#defining-page-renderers-renders) | Los servidores que proporcionan páginas procesadas (normalmente instancias de publicación de AEM). |
| [/filter](#configuring-access-to-content-filter) | Define las direcciones URL a las que el despachante habilita el acceso. |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | Configura el acceso a las URL personales. |
| [/propagateSyndPost](#forwarding-syndication-requests-propagate-syndpost) | Compatibilidad con el reenvío de solicitudes de distribución. |
| [/cache](#configuring-the-dispatcher-cache-cache) | Configura el comportamiento de almacenamiento en caché. |
| [/statistics](#configuring-load-balancing-statistics) | Definición de categorías estadísticas para cálculos de equilibrio de carga. |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-sticky-connections-for) | La carpeta que contiene documentos adhesivos. |
| [/health_check](#specifying-a-health-check-page) | La URL que se utiliza para determinar la disponibilidad del servidor. |
| [/retryDelay](#specifying-the-page-retry-delay) | El retraso antes de reintentar una conexión fallida. |
| [/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | Sanciones que afectan a las estadísticas de los cálculos de equilibrio de carga. |
| [/failover](#using-the-fail-over-mechanism) | Cuando se produce un error en la solicitud original, se vuelven a enviar las solicitudes a diferentes procesamientos. |

## Especificar una página predeterminada (IIS solamente) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>El `/homepage`parámetro (IIS solamente) ya no funciona. En su lugar, debe utilizar el módulo Reescribir URL [de IIS](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>Si utiliza Apache, debe utilizar `mod_rewrite` el módulo. Consulte la documentación del sitio Web Apache para obtener información sobre `mod_rewrite` (por ejemplo [, Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). Al utilizar `mod_rewrite`, se recomienda utilizar el indicador** [&#39; passthrough &#39;| PT&#39; (pasar al siguiente controlador)](https://helpx.adobe.com/dispatcher/kb/DispatcherModReWrite.html)** para obligar al motor de reescritura a definir `uri` el campo de `request_rec` la estructura interna en el valor del `filename` campo.

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

## Especificación de encabezados HTTP para pasar {#specifying-the-http-headers-to-pass-through-clientheaders}

La `/clientheaders` propiedad define una lista de encabezados HTTP que Dispatcher pasa desde la solicitud HTTP del cliente al procesador (instancia de AEM).

De forma predeterminada, Dispatcher envía los encabezados HTTP estándar a la instancia de AEM. En algunos casos, es posible que quiera reenviar encabezados adicionales o quitar encabezados específicos:

* Agregue encabezados, como encabezados personalizados, que su instancia de AEM espera en la solicitud HTTP.
* Elimine encabezados, como encabezados de autenticación, que solo sean relevantes para el servidor web.

Si personaliza el conjunto de encabezados que se van a pasar, debe especificar una lista exhaustiva de encabezados, incluidos aquellos que normalmente se incluyen de forma predeterminada.

Por ejemplo, una instancia de Dispatcher que gestione solicitudes de activación de página para instancias de publicación requiere `PATH` el encabezado en `/clientheaders` la sección. El `PATH` encabezado permite la comunicación entre el agente de replicación y el despachante.

El siguiente código es una configuración de ejemplo para `/clientheaders`:

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

## Identificación de los hosts virtuales {#identifying-virtual-hosts-virtualhosts}

La `/virtualhosts` propiedad define una lista de todas las combinaciones de nombre host/URI que Dispatcher acepta para esta granja. Puede utilizar el carácter asterisco (&quot; *&quot;) como comodín. Los valores de la propiedad/ `virtualhosts` property utilizan el siguiente formato:

```xml
[scheme]host[uri][*]
```

* `scheme`: (Opcional) O `https://` bien `https://.`
* `host`: El nombre o la dirección IP del equipo host y el número de puerto, si es necesario. (Consulte [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23)
* `uri`: (Opcional) La ruta a los recursos.

La siguiente configuración de ejemplo gestiona solicitudes de los dominios.com y. ch de mycompany y todos los dominios de mysubdivision:

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

La siguiente configuración gestiona *todas* las solicitudes:

```xml
   /virtualhosts
    {
    "*"
    }
```

### Resolución del host virtual {#resolving-the-virtual-host}

Cuando Dispatcher recibe una solicitud HTTP o HTTPS, encuentra el valor de host virtual que mejor coincide con los `host,``uri`encabezados y `scheme` los encabezados de la solicitud. Dispatcher evalúa los valores en `virtualhosts` las propiedades en el orden siguiente:

* Dispatcher comienza en la granja más baja y avanza hacia arriba en el despachante. cualquier archivo.
* Para cada granja, Dispatcher comienza con el valor superior de la `virtualhosts` propiedad y avanza por la lista de valores.

Dispatcher encuentra el mejor valor de host virtual coincidente de la siguiente manera:

* El primer host virtual que coincide con los tres, `host`el `scheme`y el `uri` de la solicitud.
* Si no `virtualhosts` hay valores `scheme` y `uri` partes que coinciden con el `scheme` y `uri` de la solicitud, se utilizará el primer host virtual que coincida con el `host` de la solicitud.
* Si ningún `virtualhosts` valor tiene una parte de host que coincide con el host de la solicitud, se utiliza el host virtual superior de la granja superior.

Por lo tanto, debe colocar el host virtual predeterminado en la parte superior de `virtualhosts` la propiedad en la granja superior de su despachante. cualquier archivo.

### Ejemplo de resolución de host virtual {#example-virtual-host-resolution}

El siguiente ejemplo representa un fragmento de un despachante. cualquier archivo que define dos granjas Dispatcher y cada granja define una `virtualhosts` propiedad.

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

Con este ejemplo, la tabla siguiente muestra los hosts virtuales resueltos para solicitudes HTTP determinadas:

| Solicitar URL | Se ha resuelto el host virtual |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/*;` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## Activación de sesiones seguras: /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized`**debe** configurarse en `"0"``/cache` la sección para habilitar esta función.

Cree una sesión segura para acceder a la granja de procesamiento a fin de que los usuarios deban iniciar sesión para acceder a cualquier página de la granja. Después de iniciar sesión, los usuarios pueden acceder a todas las páginas de la granja. Consulte [Creación de un grupo de usuarios cerrado](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/cug.html#CreatingTheUserGroupToBeUsed) para obtener información sobre el uso de esta función con cug.

La `/sessionmanagement` propiedad es una subpropiedad de `/farms`.

>[!CAUTION]
>
>Si las secciones del sitio web utilizan diferentes requisitos de acceso, debe definir varias granjas.

**/sessionmanagement** tiene varios subparámetros:

**/directory** (obligatorio)

Directorio que almacena la información de la sesión. Si el directorio no existe, se crea.

**/encode** (opcional)

Cómo se codifica la información de la sesión. Utilice &quot;md 5&quot; para la codificación utilizando el algoritmo md 5 o &quot;hexadecimal&quot; para la codificación hexadecimal. Si codifica los datos de la sesión, un usuario con acceso al sistema de archivos no podrá leer el contenido de la sesión. El valor predeterminado es &quot;md 5&quot;.

**/header** (opcional)

Nombre del encabezado HTTP o cookie que almacena la información de autorización. Si almacena la información en el encabezado http, utilice `HTTP:<*header-name*>`. Para almacenar la información en una cookie, utilice `Cookie:<header-name>`. Si no especifica un valor `HTTP:authorization` , se utiliza.

**/timeout** (opcional)

Cantidad de segundos hasta que se agotó el tiempo de espera de la sesión después de que se haya utilizado el último. Si no se especifica &quot;800&quot;, la sesión se agotará un poco más de 13 minutos después de la última solicitud del usuario.

A continuación se presenta una configuración de ejemplo:

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions" 
  /encode "md5" 
  /header "HTTP:authorization" 
  /timeout "800" 
  }
```

## Definición de procesadores de páginas {#defining-page-renderers-renders}

La propiedad /renders define la URL a la que Dispatcher envía solicitudes para procesar un documento. La siguiente sección de ejemplo `/renders` identifica una instancia de AEM única para procesar:

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

En el siguiente ejemplo /renders se identifica una instancia de AEM que se ejecuta en el mismo equipo que Dispatcher:

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

En el siguiente ejemplo /renders se distribuye las solicitudes de procesamiento de forma equitativa entre dos instancias de AEM:

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

### Procesa las opciones {#renders-options}

**/timeout**

Especifica el tiempo de espera de conexión que accede a la instancia de AEM en milisegundos. El valor predeterminado es &quot;0&quot;, lo que hace que Dispatcher espere indefinidamente.

**/receiveTimeout**

Especifica el tiempo en milisegundos que se permite realizar una respuesta. El valor predeterminado es &quot;600000&quot;, lo que provoca que Dispatcher espere 10 minutos. Una configuración de &quot;0&quot; elimina el tiempo de espera completamente.\
Si se alcanza el tiempo de espera al analizar los encabezados de respuesta, se devuelve un estado HTTP de 504 (puerta de enlace incorrecta). Si se alcanza el tiempo de espera mientras se lee el cuerpo de respuesta, Dispatcher devolverá la respuesta incompleta al cliente pero eliminará cualquier archivo caché que pueda haber sido escrito.

**/ipv4**

Especifica si Dispatcher utiliza `getaddrinfo` la función (para ipv 6) o `gethostbyname` la función (para ipv 4) para obtener la dirección IP del procesamiento. Se `getaddrinfo` utiliza un valor de 0. Se utiliza un `gethostbyname` valor de 1. El valor predeterminado es 0.

La función getaddrinfo devuelve una lista de direcciones IP. Dispatcher repite la lista de direcciones hasta que establece una conexión TCP/IP. Por lo tanto, la propiedad ipv 4 es importante cuando el nombre de host de procesamiento está asociado con direcciones IP mutliple y el host, como respuesta a la función getaddrinfo, devuelve una lista de direcciones IP que siempre están en el mismo orden. En este caso, debe utilizar la función gethostbyname para que la dirección IP con la que se conecta Dispatcher se asigne al azar.

El equilibrio de carga elástica de Amazon (ELB) es un servicio que responde a getaddrinfo con una lista potencialmente ordenada de direcciones IP.

**/secure**

Si `/secure` la propiedad tiene un valor de &quot;1&quot; Dispatcher utiliza HTTPS para comunicarse con la instancia de AEM. Para obtener más información, consulte [Configuración de Dispatcher para utilizar SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

Con Dispatcher versión **4.1.6**, puede configurar `/always-resolve` la propiedad de la siguiente manera:

* Cuando se establece en &quot;1&quot;, se resolverá el nombre de host en cada solicitud (Dispatcher nunca almacenará en caché ninguna dirección IP). Puede haber un ligero impacto de rendimiento debido a la llamada adicional necesaria para obtener la información del host de cada solicitud.
* Si la propiedad no está configurada, la dirección IP se almacenará en caché de forma predeterminada.

Además, esta propiedad puede utilizarse en caso de que se ejecute en problemas dinámicos de resolución IP, como se muestra en la siguiente muestra:

```xml
/rend {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## Configuración del acceso al contenido {#configuring-access-to-content-filter}

Utilice `/filter` la sección para especificar las solicitudes HTTP que Dispatcher acepta. Todas las demás solicitudes se devuelven al servidor Web con un código de error 404 (página no encontrada). Si no `/filter` existe ninguna sección, se aceptan todas las solicitudes.

**Nota:** Las solicitudes del [statfile](dispatcher-configuration.md#main-pars-title-28) siempre se rechazan.

>[!CAUTION]
>
>Consulte la lista de comprobación de seguridad [de Dispatcher](security-checklist.md) para conocer otras consideraciones al restringir el acceso mediante Dispatcher. Asimismo, lea [la lista de seguridad](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html) de AEM para obtener más detalles sobre seguridad relacionados con su instalación de AEM.

La /filter ón consiste en una serie de reglas que deniegan o permiten el acceso al contenido según patrones en la parte de la línea de solicitud de la solicitud HTTP. Debe utilizar una estrategia de telefonía para su /filter ón:

* Primero, denegar el acceso a todo.
* Permitir el acceso al contenido según sea necesario.

### Definición de un filtro {#defining-a-filter}

Cada elemento de la `/filter` sección incluye un tipo y un patrón que coinciden con un elemento específico de la línea de solicitud o con la línea de solicitud completa. Cada filtro puede contener los siguientes elementos:

* **Tipo**: `/type` Indica si se debe permitir o denegar el acceso de las solicitudes que coinciden con el patrón. El valor puede ser `allow` o `deny`.

* **Elemento de la línea de solicitud:** Incluya `/method``/url`, `/query`o `/protocol` y un patrón para filtrar solicitudes según estas partes específicas de la parte de la línea de solicitud de la solicitud HTTP. El filtrado en elementos de la línea de solicitud (en lugar de en la línea de solicitud completa) es el método de filtro preferido.

* **Elementos avanzados de la línea de solicitud:** A partir de Dispatcher 4.2.0, hay cuatro nuevos elementos de filtro disponibles para su uso. Estos nuevos elementos son `/path`, `/selectors`y `/extension``/suffix` respectivamente. Incluya uno o más de estos elementos para controlar aún más los patrones de URL.

>[!NOTE]
>
>Para obtener más información sobre la parte de la línea de solicitud que hace referencia a cada uno de estos elementos, consulte la [página Sling URL Decomposition](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html) wiki.

* **propiedad de resplandor**: La `/glob` propiedad se utiliza para coincidir con toda la línea de solicitud de la solicitud HTTP.

>[!CAUTION]
>
>El filtrado con globos está obsoleto en Dispatcher. Por lo tanto, debe evitar el uso de globos en `/filter` las secciones, ya que puede provocar problemas de seguridad. Entonces, en lugar de:

`/glob "* *.css *"`

debe utilizar

`/url "*.css"`

#### La parte de la línea de solicitud de solicitudes HTTP {#the-request-line-part-of-http-requests}

HTTP/1.1 define la [línea de solicitud](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) como se indica a continuación:

*Método Request-URI HTTP-Version*&lt; CRLF &gt;

Los caracteres &lt; CRLF &gt; ponderan un retorno de carro seguido de un salto de línea. El siguiente ejemplo es la línea de solicitud que se recibe cuando un cliente solicita la página en la página del sitio Geometrixx-Outoors:

GET /content/geometrixx-outdoors/en.html HTTP .1 .1 &lt; CRLF &gt;

Los patrones deben tener en cuenta los caracteres de espacio en la línea de solicitud y los caracteres &lt; CRLF &gt;.

#### Comillas dobles vs. comillas simples {#double-quotes-vs-single-quotes}

Al crear las reglas de filtro, utilice comillas dobles `"pattern"` para patrones simples. Si utiliza Dispatcher 4.2.0 o posterior y el patrón incluye una expresión regular, debe encerrar el patrón regex `'(pattern1|pattern2)'` dentro de comillas simples.

#### Expresiones regulares {#regular-expressions}

Después de Dispatcher 4.2.0, puede incluir expresiones regulares POSIX Extended en los patrones de filtro.

#### Solución de problemas de filtros {#troubleshooting-filters}

Si los filtros no se activan de la manera esperada, habilite [Rastrear registro](#trace-logging) en dispatcher para que pueda ver qué filtro está interceptando la solicitud.

#### Filtro de ejemplo: Denegar todo {#example-filter-deny-all}

La siguiente sección de filtro de ejemplo hace que Dispatcher deniegue solicitudes de todos los archivos. Debe denegar el acceso a todos los archivos y, a continuación, permitir el acceso a áreas específicas.

```xml
  /0001  { /glob "*" /type "deny" }
```

Las solicitudes a un área denegada explícitamente dan como resultado un código de error 404 (página no encontrada) que se devuelve.

#### Filtro de ejemplo: Denegar Acess a áreas específicas {#example-filter-deny-acess-to-specific-areas}

Los filtros también permiten denegar el acceso a varios elementos, por ejemplo páginas ASP y áreas sensibles dentro de una instancia de publicación. El filtro siguiente deniega el acceso a las páginas ASP:

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

#### Filtro de ejemplo: Permitir acceso a la consola Flujo de trabajo {#example-filter-allow-access-to-the-workflow-console}

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

Si todavía necesita acceder a páginas individuales dentro del área restringida, puede permitir el acceso a ellas. Por ejemplo, para permitir el acceso a la ficha Archivar de la consola Flujo de trabajo, agregue la siguiente sección:

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>Cuando se aplican varios patrones de filtros a una solicitud, el último patrón de filtro que se aplique es efectivo.

#### Filtro de ejemplo: Uso de expresiones regulares {#example-filter-using-regular-expressions}

Este filtro permite extensiones en directorios de contenido no públicos usando una expresión regular, definida aquí entre comillas simples:

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Filtro de ejemplo: Filtrar elementos adicionales de una dirección URL de solicitud {#example-filter-filter-additional-elements-of-a-request-url}

A continuación se muestra un ejemplo de regla que bloquea la toma de contenido desde `/content` la ruta y el subárbol, utilizando filtros para ruta, selectores y extensiones:

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }
```

### Ejemplo de /filter ón {#example-filter-section}

Al configurar Dispatcher, debe restringir el acceso externo en la medida de lo posible. El siguiente ejemplo proporciona un acceso mínimo para los visitantes externos:

* `/content`
* contenido misceláneo como diseños y bibliotecas de cliente; por ejemplo:

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

Después de crear filtros [, pruebe el acceso a la página](dispatcher-configuration.md#main-pars-title-19) para asegurarse de que la instancia de AEM sea segura.

La siguiente /filter ón del archivo Dispatcher. any se puede utilizar como base en el archivo [de configuración](dispatcher-configuration.md) Dispatcher.

Este ejemplo se basa en el archivo de configuración predeterminado que se proporciona con Dispatcher y se ha diseñado como ejemplo para utilizarlo en un entorno de producción. Los elementos con el prefijo # se desactivan (comentados), se debe tener cuidado si decide activar cualquiera de ellos (eliminando el # en esa línea), ya que esto puede tener un impacto de seguridad.

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
>Cuando se utiliza con Apache, diseñe los patrones de URL de filtro según la propiedad dispatcheruseprocessedurl del módulo Dispatcher. (Consulte [Apache Web Server - Configurar el servidor web Apache para Dispatcher](dispatcher-install.md#main-pars-55-35-1022)).

>[!NOTE]
>
>Los filtros 0030 y 0031 relativos a Dynamic Media se aplican a AEM 6.0 y posterior.

Tenga en cuenta las siguientes recomendaciones si decide ampliar el acceso:

* El acceso externo debe `/admin`*estar completamente deshabilitado* si utiliza la versión 5.4 o anterior de CQ.

* Se debe tener cuidado al permitir el acceso a los archivos en `/libs`. El acceso debe permitirse por separado.
* Denegar el acceso a la configuración de replicación para que no pueda verse:

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Denegar el acceso al gadgets Google Inversi inverso:

   * `/libs/opensocial/proxy*`

Según la instalación, puede haber recursos adicionales dentro `/libs`o `/apps` en cualquier otra parte que deban estar disponibles. Puede utilizar `access.log` el archivo como un método para determinar los recursos a los que se accede externamente.

>[!CAUTION]
>
>El acceso a consolas y directorios puede presentar un riesgo de seguridad para los entornos de producción. A menos que tenga justificaciones explícitas, deben permanecer desactivadas (comentadas).

>[!CAUTION]
>
>Si [utiliza informes en un entorno](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/reporting.html#UsingReportsinaPublishEnvironment) de publicación, debe configurar Dispatcher para denegar el acceso `/etc/reports` a los visitantes externos.

### Restricción de cadenas de consulta {#restricting-query-strings}

Desde la versión 4.1.5 de Dispatcher, utilice `/filter` la sección para restringir cadenas de consulta. Se recomienda encarecidamente permitir explícitamente cadenas de consulta y excluir asignaciones genéricas mediante `allow` elementos de filtro.

Una sola entrada puede tener *glob* o alguna combinación de *método*,*url*,*consulta* y *versión* , pero no ambos. El siguiente ejemplo permite la cadena `a=*` de consulta y deniega todas las demás cadenas de consulta para direcciones URL que responden al `/etc` nodo:

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>Si una regla contiene un `/query`, solo coincidirá con las solicitudes que contengan una cadena de consulta y que coincidan con el patrón de consulta proporcionado.
>
>En el ejemplo anterior, si no `/etc` hay ninguna cadena de consultas permitidas, se requerirán las siguientes reglas:


```xml
/filter {  
>/0001 { /type "deny" /method “*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type “deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Comprobación de la seguridad de Dispatcher {#testing-dispatcher-security}

Los filtros de Dispatcher deben bloquear el acceso a las siguientes páginas y secuencias de comandos en instancias de publicación de AEM. Utilice un explorador Web para intentar abrir las páginas siguientes como visitante del sitio y verificar que se devuelva un código 404. Si se obtiene algún otro resultado, ajuste los filtros.

Tenga en cuenta que debe ver el procesamiento de páginas normal para /content/add_valid_page.html? debug = layout.


* /admin
* /system/console
* /dav/crx.default
* /crx
* /bin/crxde/logs
* /jcr: system/jcr: Versionstorage. json
* /_jcr_system/_jcr_versionStorage.json
* /libs/wcm/core/content/siteadmin.html
* /libs/collab/core/content/admin.html
* /libs/cq/ui/content/dumplibs.html
* /var/linkchecker.html
* /etc/linkchecker.html
* /home/users/a/admin/profile.json
* /home/users/a/admin/profile.xml
* /libs/cq/core/content/login.json
* /content/../libs/foundation/components/text/text.jsp
* /content/.{.}/libs/foundation/components/text/text. jsp
* /apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr % 3 acontent/jcr % 3 adata
* /libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet
* /content.pages.json
* /content.languages.json
* /content.blueprint.json
* /content.-1. json
* /content.10.json
* /content.infinity.json
* /content.tidy.json
* /content.tidy.-1. blubber. json
* /content/dam.tidy.-100. json
* /content/content/geometrixx.sitemap.txt
* /content/add_valid_page.query.json? statement =//*
* /content/add_valid_page.qu % 65 ry. js % 6 Fn? statement =//*
* /content/add_valid_page.query.json? statement =//*[@ transportpassword]/(@ transportpassword % 20|% 20@transportUri % 20|% 20@transportUser)
* /content/add_valid_path_to_a_page/_jcr_content.json
* /content/add_valid_path_to_a_page/jcr: content. json
* /content/add_valid_path_to_a_page/_jcr_content.feed
* /content/add_valid_path_to_a_page/jcr: content. feed
* /content/add_valid_path_to_a_page/pagename._ jcr_ content. feed
* /content/add_valid_path_to_a_page/pagename.jcr: content. feed
* /content/add_valid_path_to_a_page/pagename.docview.xml
* /content/add_valid_path_to_a_page/pagename.docview.json
* /content/add_valid_path_to_a_page/pagename.sysview.xml
* /etc.xml
* /content.feed.xml
* /content.rss.xml
* /content.feed.html
* /content/add_valid_page.html? debug = layout
* /projects
* /tagging
* /etc/replication.html
* /etc/cloudservices.html
* /bienvenido

Envíe el siguiente comando en un terminal o un símbolo del sistema para determinar si el acceso de escritura anónima está habilitado. No debe poder escribir datos en el nodo.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Introduzca el siguiente comando en una terminal o un símbolo del sistema para intentar invalidar la caché de Dispatcher y asegurarse de recibir una respuesta de código 404:

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Habilitación de acceso a URL personales {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The com.adobe.granite.dispatcher.vanityurl.content package needs to be made public before publishing this contnet.</p>

 -->

Configure Dispatcher para permitir el acceso a URL personales configuradas para sus páginas CQ o AEM.

Cuando se habilita el acceso a URL personales, Dispatcher llama periódicamente a un servicio que se ejecuta en la instancia de procesamiento para obtener una lista de URL personales. Dispatcher almacena esta lista en un archivo local. Cuando se deniega una solicitud de una página debido a un filtro en `/filter` la sección, Dispatcher consulta la lista de URL personales. Si la URL denegada está en la lista, Dispatcher permite el acceso a la URL de vanidad.

Para habilitar el acceso a URL personales, agregue `/vanity_urls` una sección a la `/farms` sección, similar al siguiente ejemplo:

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

La `/vanity_urls` sección contiene las siguientes propiedades:

* `/url`: La ruta al servicio URL de vanidad que se ejecuta en la instancia de procesamiento. El valor de esta propiedad debe ser `"/libs/granite/dispatcher/content/vanityUrls.html"`.

* `/file`: La ruta al archivo local donde Dispatcher almacena la lista de URL personales. Asegúrese de que Dispatcher tiene acceso de escritura a este archivo.
* `/delay`: (Segundos) El tiempo entre llamadas al servicio URL de vanidad.

>[!NOTE]
>
>Si su procesamiento es una instancia de AEM, debe instalar [el](https://www.adobeaemcloud.com/content/marketplace/marketplaceProxy.html?packagePath=/content/companies/public/adobe/packages/cq600/component/vanityurls-components) paquete vanityurls-Components para instalar el servicio URL de vanidad. (Consulte [Inicio de sesión en Package Share](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/package-manager.html#SigningIntoPackageShare)).

Siga el procedimiento siguiente para permitir el acceso a URL personales.

1. Si el servicio de procesamiento es una instancia de AEM, instale el paquete com. adobe. granite. dispatcher. vanityurl. content en la instancia de publicación (consulte la nota anterior).
1. Para cada URL de vanidad que haya configurado para una página de AEM o CQ, asegúrese de que ` [/filter](dispatcher-configuration.md#main-pars_134_32_0009)` la configuración deniegue la URL. Si es necesario, agregue un filtro que deniegue la dirección URL.
1. Agregue `/vanity_urls` la siguiente sección `/farms`.
1. Reinicie el servidor Web de Apache.

## Reenvío de solicitudes de distribución: /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

Normalmente, las solicitudes de distribución solo se destinan a Dispatcher. Por lo tanto, de forma predeterminada no se envían al procesador (por ejemplo, una instancia de AEM).

Si es necesario, establezca la propiedad /propagateSyndPost en &quot;1&quot; para enviar solicitudes de distribución a Dispatcher. Si se establece, debe asegurarse de que las solicitudes POST no se denieguen en la sección del filtro.

## Configuración de la caché de Dispatcher: /cache {#configuring-the-dispatcher-cache-cache}

La `/cache` sección controla cómo se guardan los documentos en caché Dispatcher. Configure varias subpropiedades para implementar sus estrategias de almacenamiento en caché:


* /docroot
* /statfile
* /serveStaleOnError
* /allowAuthorized
* /reglas
* /statfileslevel
* /invalidate
* /invalidateHandler
* /allowedClients
* /ignoreUrlParams
* /headers
* /mode
* /gracePeriod


Una sección de caché de ejemplo puede tener el aspecto siguiente:

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
>Para el almacenamiento en caché sensible a los permisos, lea [Caché Contenido seguro](permissions-cache.md).

### Especificación del directorio de caché {#specifying-the-cache-directory}

`/docroot` La propiedad identifica el directorio donde se almacenan los archivos en caché.

>[!NOTE]
>
>El valor debe ser exactamente la misma ruta que la raíz del documento del servidor Web para que Dispatcher y el servidor Web gestione los mismos archivos.\
>El servidor Web es responsable de proporcionar el código de estado correcto cuando se utiliza el archivo de caché de Dispatcher, por eso es importante que también lo encuentre.

Si utiliza varias granjas, cada granja debe utilizar una raíz de documento diferente.

### Asignación de nombres al archivo {#naming-the-statfile}

La `/statfile` propiedad identifica el archivo que se va a utilizar como archivo. Dispatcher utiliza este archivo para registrar el tiempo de la actualización de contenido más reciente. El archivo puede ser cualquier archivo en el servidor Web.

El archivo no tiene contenido. Cuando se actualiza el contenido, Dispatcher actualiza la marca de fecha y hora. El archivo predeterminado se denomina. stat y se almacena en el docroot. Dispatcher bloquea el acceso al archivo.

>[!NOTE]
>
>Si `/statfileslevel` se configura, Dispatcher ignora la `/statfile` propiedad y utiliza. stat como nombre.

### Documentos antiguos al producirse errores {#serving-stale-documents-when-errors-occur}

La `/serveStaleOnError` propiedad controla si Dispatcher devuelve documentos invalidados cuando el servidor de procesamiento devuelve un error. De forma predeterminada, cuando se toca un archivo e invalida el contenido almacenado en caché, Dispatcher elimina el contenido almacenado en caché la próxima vez que se solicita.

Si `/serveStaleOnError` se define como &quot;1&quot;, Dispatcher no elimina el contenido invalidado de la caché a menos que el servidor de procesamiento devuelva una respuesta correcta. Una respuesta de 5 xx de AEM o un tiempo de espera de conexión hace que Dispatcher proporcione el contenido obsoleto y responda con el estado de HTTP de 111 (error de validación).

### Almacenamiento en caché cuando se utiliza la autenticación {#caching-when-authentication-is-used}

`/allowAuthorized` La propiedad controla si las solicitudes que contienen la siguiente información de autenticación se almacenan en caché:

* El `authorization` encabezado.
* Una cookie denominada `authorization`.
* Una cookie denominada `login-token`.

De forma predeterminada, las solicitudes que incluyen esta información de autenticación no se almacenan en caché porque la autenticación no se realiza cuando se devuelve un documento en caché al cliente. Esta configuración evita que Dispatcher ofrezca documentos almacenados en caché a usuarios que no dispongan de los derechos necesarios.

Sin embargo, si sus requisitos permiten el almacenamiento en caché de documentos autenticados, establezca /allowAuthorized en uno:

`/allowAuthorized "1"`

>[!NOTE]
>
>To enable session management (using the `/sessionmanagement` property), the `/allowAuthorized` property must be set to `"0"`.

### Especificación de documentos a caché {#specifying-the-documents-to-cache}

La `/rules` propiedad controla qué documentos se almacenan en caché según la ruta del documento. Independientemente de la propiedad /rules, Dispatcher nunca almacena en caché un documento en las siguientes circunstancias:

* Si el URI de la solicitud contiene un signo de interrogación (&quot;? &quot;).\
   Esto suele indicar una página dinámica, como un resultado de búsqueda que no es necesario almacenar en caché.
* Falta la extensión del archivo.\
   El servidor web necesita la extensión para determinar el tipo de documento (el tipo MIME).
* Se establece el encabezado de autenticación (esto puede configurarse)
* Si la instancia de AEM responde con los encabezados siguientes:

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>Los métodos GET o HEAD (para los encabezados HTTP) son almacenables por Dispatcher. Para obtener más información sobre el almacenamiento en caché de encabezados de respuesta, consulte [la sección Caché de encabezados de respuesta](dispatcher-configuration.md#caching-http-response-headers) HTTP.

Cada elemento de la propiedad /rules incluye un [patrón de resplandor](#designing-patterns-for-glob-properties) y un tipo:

* El patrón de resplandor se utiliza para coincidir con la ruta del documento.
* El tipo indica si se almacenan en caché los documentos que coinciden con el patrón de resplandor. El valor puede ser permitir (para almacenar en caché el documento) o denegar (para procesar siempre el documento).

Si no tiene páginas dinámicas (más allá de aquellas ya excluidas por las reglas anteriores), puede configurar Dispatcher para almacenar en caché todo. La sección Reglas tiene este aspecto:

```xml
/rules
  { 
    /0000  {  /glob "*"   /type "allow" }
  }
```

Para obtener más información acerca de las propiedades de resplandor, consulte [Diseño de patrones para propiedades](#designing-patterns-for-glob-properties)de resplandor.

Si hay algunas secciones de la página dinámicas (por ejemplo, una aplicación de noticias) o dentro de un grupo de usuarios cerrado, puede definir excepciones:

>[!NOTE]
>
>Los grupos de usuarios cerrados no se deben almacenar en caché porque los derechos de usuario no están marcados en las páginas en caché.

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }   
  }
```

**Compresión**

En los servidores Web Apache, puede comprimir los documentos en caché. La compresión permite que Apache devuelva el documento en un formulario comprimido si lo solicita el cliente. La compresión se realiza automáticamente activando el módulo `mod_deflate`Apache, por ejemplo:

```xml
AddOutputFilterByType DEFLATE text/plain
```

El módulo se instala de forma predeterminada con Apache 2. x.

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

Utilice `/statfileslevel` la propiedad para invalidar los archivos almacenados en caché según su ruta:

* Dispatcher crea `.stat`archivos en cada carpeta desde la carpeta docroot al nivel que especifique. La carpeta docroot es level 0.
* Los archivos se invalidan al tocar el `.stat` archivo. La última fecha de modificación `.stat` del archivo se compara con la última fecha de modificación de un documento en caché. El documento se vuelve a buscar si `.stat` el archivo es más nuevo.

* Cuando un archivo ubicado en un nivel determinado se invalida, **se tocarán todos** `.stat` los archivos del docroot **al** nivel del archivo invalidado o el configurado `statsfilevel` (cualquiera sea menor).

   * Por ejemplo: si establece `statfileslevel` la propiedad en 6 y un archivo se invalida en el nivel 5, se tocará cada `.stat` archivo de docroot a 5. Continuando con este ejemplo, si un archivo se invalida en el nivel 7, cada uno. `stat` de docroot a 6 se tocará (ya que `/statfileslevel = "6"`).

Solo se afectan los recursos** a lo largo de la ruta** al archivo invalidado. Considere el siguiente ejemplo: Un sitio web utiliza la estructura `/content/myWebsite/xx/.` Si establece `statfileslevel` 3, un `.stat`archivo se crea de la siguiente manera:

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

Cuando un archivo se `/content/myWebsite/xx` invalida, se toca cada `.stat` archivo desde docroot `/content/myWebsite/xx`hacia abajo. Este sería el caso solamente para `/content/myWebsite/xx` , por ejemplo `/content/myWebsite/yy` , o `/content/anotherWebSite`.

>[!NOTE]
>
>La invalidación se puede evitar enviando un Encabezado `CQ-Action-Scope:ResourceOnly`adicional. Esto se puede utilizar para vaciar recursos concretos sin invalidar otras partes de la caché. Consulte [esta página](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) e [Invalidar manualmente la caché de Dispatcher](https://helpx.adobe.com/experience-manager/dispatcher/using/page-invalidate.html) para obtener más información.

>[!NOTE]
>
>Si especifica un valor para `/statfileslevel` la propiedad, se omite `/statfile` la propiedad.

### Invalidar automáticamente los archivos caché {#automatically-invalidating-cached-files}

`/invalidate` La propiedad define los documentos que se invalidan automáticamente cuando se actualiza el contenido.

Con la invalidación automática, Dispatcher no elimina los archivos caché después de una actualización de contenido, sino que comprueba su validez cuando se solicitan. Los documentos de la caché que no se invalidan automáticamente permanecerán en la caché hasta que una actualización de contenido los elimine explícitamente.

La invalidación automática suele utilizarse para páginas HTML. Las páginas HTML generalmente contienen vínculos a otras páginas, lo que dificulta la tarea de determinar si una actualización de contenido afecta a una página. Para asegurarse de que todas las páginas relevantes se invalidan cuando se actualice el contenido, invalida automáticamente todas las páginas HTML. La siguiente configuración invalida todas las páginas HTML:

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

Para obtener más información acerca de las propiedades de resplandor, consulte [Diseño de patrones para propiedades](#designing-patterns-for-glob-properties)de resplandor.

Esta configuración produce la siguiente actividad cuando se activa /content/geometrixx/en:

* Todos los archivos con patrón en.* se eliminan de la carpeta /content/geometrixx/.
* Se eliminará la carpeta /content/geometrixx/en/_jcr_content.
* El resto de archivos que coinciden con la configuración /invalidate no se eliminan inmediatamente. Estos archivos se eliminan cuando se produce la siguiente solicitud. En nuestro ejemplo /content/geometrixx.html no se elimina, se eliminará cuando se solicite /content/geometrixx.html.

Si ofrece archivos PDF y ZIP generados automáticamente para su descarga, es posible que también tenga que invalidarlos automáticamente. Ejemplo de configuración siguiente:

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

La integración de AEM con Adobe Analytics proporciona datos de configuración en un archivo analytics. sitecatalyst. js en su sitio web. El ejemplo dispatcher. any file que se proporciona con Dispatcher incluye la siguiente regla de invalidación para este archivo:

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Uso de scripts de invalidación personalizados {#using-custom-invalidation-scripts}

La propiedad /invalidateHandler permite definir una secuencia de comandos llamada para cada solicitud de invalidación recibida por Dispatcher.

Se llama con los siguientes argumentos:

* Control\
   Ruta de contenido invalidada
* Acción\
   Acción de replicación (p. ej. Activar, Desactivar)
* Ámbito de acción\
   El ámbito de Acción de replicación (vacío, a menos que se envíe `CQ-Action-Scope: ResourceOnly` un encabezado, consulte [Invalidating Cached Pages from AEM](page-invalidate.md) for details).

Esto puede utilizarse para cubrir distintos casos de uso, como invalidar otros cachés específicos de la aplicación, o para manejar casos en los que la URL externalizada de una página y su lugar en el docroot no coincide con la ruta de contenido.

En la secuencia de comandos de ejemplo siguiente se registra cada solicitud invalidada a un archivo.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### script de controlador de invalidación de muestra {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Limitación de los clientes que pueden vaciar la caché {#limiting-the-clients-that-can-flush-the-cache}

La propiedad /allowedClients define clientes específicos que pueden vaciar la caché. Los patrones globales se comparan con la IP.

El siguiente ejemplo:

1. deniega el acceso a cualquier cliente
1. permite expresamente el acceso al localhost

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

Para obtener más información acerca de las propiedades de resplandor, consulte [Diseño de patrones para propiedades](#designing-patterns-for-glob-properties)de resplandor.

>[!CAUTION]
>
>Se recomienda definir /allowedClients.
>
>Si esto no se realiza, cualquier cliente puede enviar una llamada para borrar la caché; Si esto se realiza repetidamente, puede afectar gravemente el rendimiento del sitio.

### Ignorar parámetros de URL {#ignoring-url-parameters}

La `ignoreUrlParams` sección define los parámetros de URL que se omiten al determinar si una página se almacena en caché o se envía desde la caché:

* Cuando una dirección URL de solicitud contiene parámetros que se omiten todos, la página se almacena en caché.
* Cuando una dirección URL de solicitud contiene uno o varios parámetros que no se omiten, la página no se almacena en caché.

Cuando se omite un parámetro para una página, la página se almacena en caché la primera vez que se solicita la página. Las solicitudes posteriores de la página se sirven en la página en caché, independientemente del valor del parámetro en la solicitud.

Para especificar qué parámetros se omiten, agregue reglas de glob a `ignoreUrlParams` la propiedad:

* Para ignorar un parámetro, cree una propiedad de glob que permita el parámetro.
* Para evitar que la página se almacene en caché, cree una propiedad de glob que deniegue el parámetro.

El siguiente ejemplo hace que Dispatcher ignore el parámetro &quot;q&quot;, de modo que las URL de solicitud que incluyen el parámetro q se almacenan en caché:

```xml
/ignoreUrlParams
{
    /0001 { /glob "*" /type "deny" }
    /0002 { /glob "q" /type "allow" }
}
```

Con `ignoreUrlParams` el valor de ejemplo, la siguiente solicitud HTTP hace que la página se almacene en caché porque se ignora `q` el parámetro:

```xml
GET /mypage.html?q=5
```

Con `ignoreUrlParams` el valor de ejemplo, la siguiente solicitud HTTP hace que la página **no** se almacene en caché porque no se ignora el `p` parámetro:

```xml
GET /mypage.html?q=5&p=4
```

Para obtener más información acerca de las propiedades de resplandor, consulte [Diseño de patrones para propiedades](#designing-patterns-for-glob-properties)de resplandor.

### Almacenamiento en caché de encabezados de respuesta HTTP {#caching-http-response-headers}

>[!NOTE]
>
>Esta función está disponible con la versión **4.1.11** de Dispatcher.

La `/headers` propiedad permite definir los tipos de encabezados HTTP que el despachante va a almacenar en caché. En la primera solicitud a un recurso sin almacenar en caché, todos los encabezados que coincidan con uno de los valores configurados (ver la muestra de configuración siguiente) se almacenan en un archivo independiente, junto al archivo caché. En solicitudes posteriores al recurso en caché, los encabezados almacenados se agregan a la respuesta.

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
>Asimismo, tenga en cuenta que no se permiten los caracteres de globalización de archivos. Para obtener más información, consulte [Diseño de patrones para propiedades](#designing-patterns-for-glob-properties)de resplandor.

>[!NOTE]
>
>Si necesita Dispatcher para almacenar y enviar encabezados de respuesta etag desde AEM, haga lo siguiente:
>
>* Agregue el nombre del encabezado en `/cache/headers`la sección.
>* Agregue la siguiente [directiva Apache](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) en la sección relacionada con Dispatcher:
>



```xml
FileETag none
```

### Permisos de archivos caché de Dispatcher {#dispatcher-cache-file-permissions}

La `mode` propiedad especifica qué permisos de archivo se aplican a nuevos directorios y archivos en la caché. Esta configuración está restringida por el `umask` proceso de llamada. Es un número octal construido a partir de la suma de uno o más de los valores siguientes:

* 0400 Permitir lectura por propietario.
* 0200 Permitir escritura por propietario.
* 0100 Permitir al propietario buscar en directorios.
* 0040 Permitir lectura por parte de los miembros del grupo.
* 0020 Permitir escritura por miembros del grupo.
* 0010 Permitir que los miembros del grupo realicen búsquedas en el directorio.
* 0004 Permitir lectura por otros usuarios.
* 0002 Permitir escritura por otros usuarios.
* 0001 Permitir que otras personas realicen búsquedas en el directorio.

El valor predeterminado es 0755, que permite al propietario leer, escribir o buscar, el grupo y otros para leerlos o buscarlos.

### Pulsación de archivos. stat {#throttling-stat-file-touching}

Con `/invalidate` la propiedad predeterminada, cada activación invalida de forma eficaz todos `.html` los archivos (cuando su ruta coincide con `/invalidate` la sección). En un sitio web con mucho tráfico, varias, las activaciones subsiguientes aumentarán la carga de cpu en el back-end. En este escenario, sería deseable «acelerar» `.stat` el tocado de archivos para mantener el sitio interactivo. Para ello, utilice `/gracePeriod` la propiedad.

La `/gracePeriod` propiedad define el número de segundos a antiguo, el recurso invalidado automáticamente se puede seguir ofreciendo desde la caché después de la última activación. La propiedad se puede utilizar en una configuración en la que un lote de activaciones invalidaría repetidamente toda la caché. El valor recomendado es de 2 segundos.

Para obtener más información, consulte también las `/invalidate` secciones y `/statfileslevel`los apartados anteriores.

## Configuración de invalidación de caché basada en tiempo - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

Si se establece, la `enableTTL` propiedad evaluará los encabezados de respuesta del back-end y, si contiene `Cache-Control` una fecha o `Expires` una fecha máximas, se crea un archivo auxiliar vacío junto al archivo caché, con el tiempo de modificación igual a la fecha de caducidad. Cuando se solicita el archivo en caché, después de la modificación se vuelve a solicitar automáticamente del servidor.

Puede habilitar la función agregando esta línea al `dispatcher.any` archivo:

```xml
/enableTTL "1"
```

>[!NOTE]
>
>Esta función está disponible con la versión **4.1.11** de Dispatcher.

## Configuración del equilibrio de carga: /statistics {#configuring-load-balancing-statistics}

La `/statistics` sección define las categorías de archivos para los que Dispatcher puntua la respuesta de cada representación. Dispatcher usa las puntuaciones para determinar cuál es la representación para enviar una solicitud.

Cada categoría que cree define un patrón de resplandor. Dispatcher compara el URI del contenido solicitado con estos patrones para determinar la categoría del contenido solicitado:

* El orden de las categorías determina el orden en que se comparan con el URI.
* El primer patrón de categoría que coincide con la URI es la categoría del archivo. No se evalúan más patrones de categoría.

Dispatcher admite un máximo de 8 categorías estadísticas. Si define más de 8 categorías, solo se utilizará la primera 8.

**Procesar selección**

Cada vez que Dispatcher requiere una página representada, utiliza el siguiente algoritmo para seleccionar el procesamiento:

1. Si la solicitud contiene el nombre de procesamiento en una `renderid` cookie, Dispatcher utiliza ese procesamiento.
1. Si la solicitud no incluye `renderid` ninguna cookie, Dispatcher compara las estadísticas de procesamiento:

   1. Dispatcher determina el cateogry del URI de la solicitud.
   1. Dispatcher determina qué representación tiene la puntuación de respuesta más baja para esa categoría y selecciona que se procesa.

1. Si aún no se selecciona ningún procesamiento, utilice el primer procesamiento de la lista.

La puntuación de una categoría de procesamiento se basa en tiempos de respuesta anteriores, como así también en conexiones exitosas anteriores y fallidas que Dispatcher intenta. Para cada intento, se actualiza la puntuación de la categoría del URI solicitado.

>[!NOTE]
>
>Si no utiliza equilibrio de carga, puede omitir esta sección.

### Definición de categorías estadísticas {#defining-statistics-categories}

Defina una categoría para cada tipo de documento para el cual desee mantener las estadísticas para la selección de procesamiento. La /statistics ón contiene una /categories ón. Para definir una categoría, agregue una línea debajo de la /categories ón que tenga el siguiente formato:

`/name { /glob "pattern"}`

La categoría `name` debe ser única para la granja. Esto `pattern` se describe en la sección [Diseño de patrones para propiedades](#designing-patterns-for-glob-properties) de resplandor.

Para determinar la categoría de una URI, Dispatcher compara el URI con cada patrón de categoría hasta que se encuentra una coincidencia. Dispatcher comienza con la primera categoría de la lista y las cotintinues en orden. Por lo tanto, coloque primero categorías con patrones más específicos.

Por ejemplo, Dispatcher el despachante predeterminado. cualquier archivo define una categoría HTML y otra categoría. La categoría HTML es más específica y por lo tanto aparece primero:

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

### Reflejo del servidor no disponible en las estadísticas de Dispatcher {#reflecting-server-unavailability-in-dispatcher-statistics}

`/unavailablePenalty` La propiedad establece el tiempo (en décimas de segundo) que se aplica a las estadísticas de procesamiento cuando falla una conexión con el procesamiento. Dispatcher agrega el tiempo a la categoría de estadísticas que coincide con el URI solicitado.

Por ejemplo, la penalización se aplica cuando no se puede establecer la conexión TCP/IP con el nombre de host o puerto designado, ya sea porque AEM no se está ejecutando (ni escuchando) o debido a un problema relacionado con la red.

`/unavailablePenalty` La propiedad es un elemento secundario directo de `/farm` la sección (un elemento secundario de `/statistics` la sección).

Si no `/unavailablePenalty` existe ninguna propiedad, se utiliza el valor &quot;1&quot;.

```xml
/unavailablePenalty "1"
```

## Identificación de una carpeta de conexión adhesiva: /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

`/stickyConnectionsFor` La propiedad define una carpeta que contiene documentos adhesivos; a esto se accede mediante la dirección URL. Dispatcher envía todas las solicitudes, desde un único usuario, que están en esta carpeta a la misma instancia de procesamiento. Las conexiones adhesivas garantizan que los datos de la sesión estén presentes y sean coherentes para todos los documentos. Este mecanismo utiliza `renderid` la cookie.

El siguiente ejemplo define una conexión adhesiva a la carpeta /products:

```xml
/stickyConnectionsFor "/products"
```

Cuando una página está compuesta por contenido de varios nodos de contenido, incluya la `/paths` propiedad que enumera las rutas al contenido. Por ejemplo, una página contiene contenido, `/content/image``/content/video`y `/var/files/pdfs`. La siguiente configuración permite conexiones adhesivas para todo el contenido de la página:

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### Httponly {#httponly}

Cuando se habilitan conexiones adhesivas, el módulo dispatcher establece la `renderid` cookie. Esta cookie no tiene `httponly` el indicador, que debe agregarse para mejorar la seguridad. Para ello, configure `httpOnly` la propiedad en `/stickyConnections` el nodo de un archivo `dispatcher.any` de configuración. El valor de la propiedad (0 o 1) define si `renderid` la cookie tiene el `HttpOnly` atributo anexado. El valor predeterminado es 0, lo que significa que no se agregará el atributo.

Para obtener información adicional sobre `httponly` el indicador, lea [esta página](https://www.owasp.org/index.php/HttpOnly).

### secure {#secure}

Cuando se habilitan conexiones adhesivas, el módulo dispatcher establece la `renderid` cookie. Esta cookie no tiene el indicador **seguro** , que debe agregarse para mejorar la seguridad. Para ello, configure `secure` la propiedad en `/stickyConnections` el nodo de un archivo `dispatcher.any` de configuración. El valor de la propiedad (0 o 1) define si `renderid` la cookie tiene el `secure` atributo anexado. El valor predeterminado es 0, lo que significa que se agregará el atributo si * * la solicitud entrante es segura. Si el valor se establece en 1, se agregará el indicador seguro independientemente de si la solicitud entrante es segura o no.

## Gestión de errores de conexión de procesamiento {#handling-render-connection-errors}

Configure el comportamiento de Dispatcher cuando el servidor de procesamiento devuelve un error 500 o no está disponible.

### Especificación de una página de comprobación de estado {#specifying-a-health-check-page}

Utilice `/health_check` la propiedad para especificar una URL que se compruebe cuando se produce un código de estado 500. Si esta página también devuelve un código de estado 500, la instancia se considera no disponible y se aplicará una penalización de hora configurable ( `/unavailablePenalty`) al procesamiento antes de volver a intentarlo.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### Especificación del retraso de reintento de página {#specifying-the-page-retry-delay}

La propiedad/ `retryDelay` property establece el tiempo (en segundos) que Dispatcher espera entre rondas de intentos de conexión con la granja procesada. Para cada ronda, el número máximo de veces que Dispatcher intenta establecer una conexión con un procesamiento es el número de procesadores en la granja.

Dispatcher utiliza un valor de `"1"` if `/retryDelay` no se define de forma explícita. El valor predeterminado es adecuado en la mayoría de los casos.

```xml
/retryDelay "1"
```

### Configuración del número de reintentos {#configuring-the-number-of-retries}

`/numberOfRetries` La propiedad define el número máximo de rondas de conexión que Dispatcher realiza con los procesadores. Si Dispatcher no se puede conectar correctamente a un procesamiento después de este número de intentos, Dispatcher devuelve una respuesta fallida.

Para cada ronda, el número máximo de veces que Dispatcher intenta establecer una conexión con un procesamiento es el número de procesadores en la granja. Por lo tanto, el número máximo de veces que Dispatcher intenta una conexión es ( `/numberOfRetries`) x (el número de procesadores).

Si el valor no se define de forma explícita, el valor predeterminado es **5**.

```xml
/numberOfRetries "5"
```

### Uso del mecanismo de conmutación por error {#using-the-failover-mechanism}

Habilitar el mecanismo de conmutación por error en la granja Dispatcher para volver a enviar solicitudes a diferentes procesamientos cuando falla la solicitud original. Cuando se habilita la conmutación por error, Dispatcher tiene el siguiente comportamiento:

* Cuando una solicitud a un procesamiento devuelve el estado HTTP 503 (UNAVAILABLE), Dispatcher envía la solicitud a un procesamiento diferente.
* Cuando una solicitud a un procesamiento devuelve el estado HTTP 50 x (que no sea 503), Dispatcher envía una solicitud para la página configurada para la `health_check` propiedad.

   * Si la comprobación de estado devuelve 500 (INTERNAL_ SERVER_ ERROR), Dispatcher envía la solicitud original a otro procesamiento.
   * Si la comprobación de santch devuelve el estado HTTP 200, Dispatcher devuelve el error HTTP 500 inicial al cliente.

Para habilitar la conmutación por error, agregue la siguiente línea a la granja (o sitio web):

```xml
/failover "1" 
```

>[!NOTE]
>
>Para volver a intentar las solicitudes HTTP que contienen cuerpo, Dispatcher envía un `Expect: 100-continue` encabezado de solicitud al procesamiento antes de poner en cola el contenido real. CQ 5.5 con CQSE, y responde inmediatamente con 100 (CONTINUE) o con un código de error. Otros contenedores servlet también deben admitir esto.

## Ignorar errores de interrupción: /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Esta opción no suele ser necesaria. Solo debe utilizarlo cuando vea los siguientes mensajes de registro:
>
>`Error while reading response: Interrupted system call`

Cualquier llamada al sistema orientada al sistema de archivos se puede interrumpir `EINTR` si el objeto de la llamada al sistema se encuentra en un sistema remoto al que se accede mediante NFS. Si estas llamadas del sistema pueden agotar el tiempo de espera o si se interrumpe se basa en cómo se montó el sistema de archivos subyacente en el equipo local.

Utilice el parámetro /ignoreEINTR si su instancia tiene dicha configuración y el registro contiene el siguiente mensaje:

`Error while reading response: Interrupted system call`

Dispatcher lee la respuesta del servidor remoto (es decir, AEM) utilizando un bucle que puede representarse como:

`while (response not finished) {  
read more data  
}`

Estos mensajes se pueden generar cuando sucede algo `EINTR` en la sección &quot; `read more data`&quot; y se producen por la recepción de una señal antes de que se reciban datos.

Para omitir estas interrupciones, puede agregar el siguiente parámetro a `dispatcher.any` (antes `/farms`):

`/ignoreEINTR "1"`

Configuración `/ignoreEINTR` para `"1"` que Dispatcher siga intentando leer datos hasta que se lea la respuesta completa. El valor predeterminado es 0 y desactiva la opción.

## Diseño de patrones para propiedades de resplandor {#designing-patterns-for-glob-properties}

Varias secciones del archivo de configuración Dispatcher usan `glob` propiedades como criterios de selección para solicitudes del cliente. Los valores de las propiedades de resplandor son patrones que Dispatcher compara con un aspecto de la solicitud, como la ruta del recurso solicitado o la dirección IP del cliente. Por ejemplo, los elementos de `/filter` la sección utilizan patrones de resplandor para identificar las rutas de las páginas en las que Dispatcher actúa o rechaza.

Los valores de resplandor pueden incluir caracteres comodín y caracteres alfanuméricos para definir el patrón.

| Carácter comodín | Descripción | Ejemplos |
|--- |--- |--- |
| `*` | Coincide con cero o con más instancias contiguas de cualquier carácter de la cadena. El carácter final de la coincidencia está determinado por cualquiera de las siguientes situaciones: <br/>Un carácter de la cadena coincide con el siguiente carácter del patrón y el carácter del patrón tiene las características siguientes:<br/><ul><li>No es *</li><li>Not a?</li><li>Carácter literal (incluyendo un espacio) o una clase de caracteres.</li><li>Se llega al final del patrón.</li></ul>Dentro de una clase de caracteres, el carácter se interpreta literalmente. | `*/geo*` Coincide con cualquier página debajo del `/content/geometrixx` nodo y `/content/geometrixx-outdoors` del nodo. Las siguientes solicitudes HTTP coinciden con el patrón de resplandor: <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*`<br/>Coincide con cualquier página debajo del `/content/geometrixx-outdoors` nodo. Por ejemplo, la siguiente solicitud HTTP coincide con el patrón de resplandor: <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Coincide con cualquier carácter individual. Utilice clases de caracteres externos. Dentro de una clase de caracteres, este carácter se interpreta literalmente. | `*outdoors/??/*`<br/> Coincide con las páginas de cualquier idioma del sitio geometrixx outdoors. Por ejemplo, la siguiente solicitud HTTP coincide con el patrón de resplandor: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>La siguiente solicitud no coincide con el patrón de resplandor: <br/><ul><li>&quot; GET /content/geometrixx-outdoors/en.html &quot;</li></ul> |
| `[ and ]` | Detiene el principio y el final de una clase de caracteres. Las clases de caracteres pueden incluir uno o más rangos de caracteres y caracteres únicos.<br/>Se produce una coincidencia si el carácter objetivo coincide con cualquiera de los caracteres de la clase de caracteres o dentro de un intervalo definido.<br/>Si no se incluye el corchete de cierre, el patrón no produce coincidencias. | `*[o]men.html*`<br/> Coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*`<br/>Coincide con las siguientes solicitudes HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Denota un rango de caracteres. Para utilizar en clases de caracteres. Fuera de una clase de caracteres, este carácter se interpreta literalmente. | `*[m-p]men.html*` Coincide con la siguiente solicitud HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Deniega la clase de caracteres o caracteres a continuación. Utilice solamente para negar caracteres y rangos de caracteres dentro de clases de caracteres. Equivalente al `^ wildcard`. <br/>Fuera de una clase de caracteres, este carácter se interpreta literalmente. | `*[!o]men.html*`<br/> Coincide con la siguiente solicitud HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>No coincide con la siguiente solicitud HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` o `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Deniega el carácter o el rango de caracteres a continuación. Se utiliza para negar solo caracteres y rangos de caracteres dentro de clases de caracteres. Equivale al carácter `!` comodín. <br/>Fuera de una clase de caracteres, este carácter se interpreta literalmente. | Se aplican los ejemplos del carácter `!` comodín, sustituyendo `!` los caracteres de los patrones de ejemplo por `^` caracteres. |


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

* La ubicación del archivo de registro Dispatcher.
* Nivel de registro.

Consulte la documentación del servidor web y el archivo Léame de la instancia de Dispatcher para obtener más información.

**Apache Rotated/Named Logs**

Si utiliza un **servidor** web Apache, puede utilizar la funcionalidad estándar para registros rotados y/o registrados. Por ejemplo, utilizando registros acoplados:

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Se rotará automáticamente:

* el archivo de registro de Dispatcher; con una marca de tiempo en la extensión (registros/dispatcher. log % Y % m % d).
* semanalmente (60 x 60 x 24 x 7 = 604800 segundos).

Consulte la documentación del servidor Web Apache sobre rotación de registro y registros con pitidos; por ejemplo [Apache 2.2](https://httpd.apache.org/docs/2.2/logs.html).

>[!NOTE]
>
>Tras la instalación, el nivel de registro predeterminado es alto (es decir, level 3 = Debug), de modo que Dispatcher registra todos los errores y advertencias. Esto es muy útil en las fases iniciales.
>
>Sin embargo, esto requiere recursos adicionales, de modo que, cuando Dispatcher funcione sin problemas *según sus necesidades*, puede (debe) bajar el nivel de registro.

### Registro de rastreo {#trace-logging}

Entre otras mejoras para Dispatcher, la versión 4.2.0 también introduce el registro de rastreo.

Este es un nivel superior que el registro de depuración, que muestra información adicional en los registros. Agrega registro para:

* Valores de los encabezados reenviados;
* Regla que se aplica para una acción determinada.

Puede habilitar el registro de seguimiento estableciendo el nivel de registro en `4` el servidor web.

A continuación se muestra un ejemplo de registros con el rastreo activado:

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

Y se registra un evento cuando se solicita un archivo que coincide con una regla de bloqueo:

```xml
[Thu Mar 03 14:42:45 2016] [T] [11831] 'GET /content.infinity.json HTTP/1.1' was blocked because of /0082
```

## Confirmar operación básica {#confirming-basic-operation}

Para confirmar la operación básica y la interacción del servidor web, Dispatcher y la instancia de AEM, puede seguir estos pasos:

1. Establezca el `loglevel` valor `3`.

1. Inicie el servidor Web; esto también inicia Dispatcher.
1. Inicie la instancia de AEM.
1. Compruebe los archivos de registro y error de su servidor web y Dispatcher.\
   Según el servidor web, debería ver mensajes como:\
   `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured`\
   y:\
   `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Navegar por el sitio web a través del servidor web. Confirme que el contenido se muestre según sea necesario.\
   Por ejemplo, en una instalación local en la que AEM se ejecuta en el puerto `4502` y el servidor web en `80` acceso a la consola Sitios web mediante ambos:\
   ` https://localhost:4502/libs/wcm/core/content/siteadmin.html  
https://localhost:80/libs/wcm/core/content/siteadmin.html  
`Los resultados deberían ser idénticos. Confirme el acceso a otras páginas con el mismo mecanismo.

1. Compruebe que el directorio de la memoria caché se esté rellenando.
1. Active una página para comprobar que la caché se esté vaciando correctamente.
1. Si todo funciona correctamente, puede reducirlo `loglevel``0`.

## Uso de varios despachantes {#using-multiple-dispatchers}

En las configuraciones complejas, puede utilizar varios Dispatcher. Por ejemplo, puede utilizar:

* un despachante para publicar un sitio web en la intranet
* un segundo despachante, bajo una dirección diferente y con diferentes configuraciones de seguridad, para publicar el mismo contenido en Internet.

En este caso, asegúrese de que cada solicitud pasa sólo por un despachante. Dispatcher no gestiona las solicitudes procedentes de otro despachante. Por lo tanto, asegúrese de que ambos Dispatcher accedan directamente al sitio Web de AEM.

## Depuración {#debugging}

Al agregar el encabezado `X-Dispatcher-Info` a una solicitud, Dispatcher responde si el destino se ha almacenado en caché, se devuelve de la caché o no se puede almacenar en caché. El encabezado de respuesta `X-Cache-Info` contiene esta información en un formulario legible. Puede utilizar estos encabezados de respuesta para depurar problemas relacionados con respuestas almacenadas en caché por Dispatcher.

Esta funcionalidad no está habilitada de forma predeterminada, por lo que para que se incluya el encabezado `X-Cache-Info` de respuesta, la granja debe contener la siguiente entrada:

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

Además, el `X-Dispatcher-Info` encabezado no necesita un valor, pero si se utiliza `curl` para realizar pruebas debe especificar un valor para enviar el encabezado, como:

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/we-retail/us/en.html
```

A continuación se muestra una lista que contiene los encabezados de respuesta `X-Dispatcher-Info` que devolverán:

* **en caché**\
   El archivo de destino se encuentra en la caché y el despachante ha determinado que es válido enviarlo.
* **caché**\
   El archivo de destino no está contenido en la caché y el despachante ha determinado que es válido para almacenar en caché el resultado y enviarlo.
* **caché: archivo de stat es más reciente El**
archivo de destino está contenido en la caché. Sin embargo, se invalida por un archivo de almacenamiento más reciente. El despachante eliminará el archivo de destino, lo volverá a crear desde la salida y lo enviará.
* **not cacheable: ningún documento raíz**
La configuración de granja no contiene una raíz de documento (elemento `cache.docroot`de configuración).
* **not cacheable: ruta de archivo de caché demasiado larga**\
   El archivo de destino, la concatenación de raíz del documento y el archivo URL, excede el nombre de archivo más largo posible en el sistema.
* **not cacheable: ruta de archivo temporal demasiado larga**\
   La plantilla de nombre de archivo temporal excede el nombre de archivo más largo posible en el sistema. El despachante crea primero un archivo temporal antes de crear o sobrescribir el archivo en caché. El nombre de archivo temporal es el nombre de archivo de destino con los caracteres `_YYYYXXXXXX` añadidos, donde `Y` se `X` reemplazará y se sustituirá para crear un nombre único.
* **not cacheable: La dirección URL de solicitud no tiene extensión**\
   La dirección URL de la solicitud no tiene extensión, o bien existe una ruta que sigue la extensión del archivo, por ejemplo: `/test.html/a/path`.
* **not cacheable: request was&#39;t GET OR HEAD**
The HTTP method is ni a GET ni a HEAD. El despachante asume que el resultado contendrá datos dinámicos que no deberían almacenarse en caché.
* **not cacheable: la solicitud contiene una cadena de consulta**\
   La solicitud contenía una cadena de consulta. El despachante asume que la salida depende de la cadena de consulta proporcionada y, por lo tanto, no la almacena en caché.
* **not cacheable: el administrador de sesión no se autenticó**\
   La caché de granja está regulada por un administrador de sesión (la configuración contiene un `sessionmanagement` nodo) y la solicitud no contiene la información de autenticación adecuada.
* **not cacheable: solicitud contiene autorización**\
   La granja no está permitida en la caché ( `allowAuthorized 0`) y la solicitud contiene información de autenticación.
* **not cacheable: target es un directorio**\
   El archivo de destino es un directorio. Esto podría apuntar a algunos errores conceptuales, donde una dirección URL y alguna subdirección URL contienen salida almacenable, por ejemplo, si una solicitud para `/test.html/a/file.ext` llegar primero y contiene salida almacenable, el despachante no podrá almacenar en caché el resultado de una solicitud posterior a `/test.html`.
* **not cacheable: URL de solicitud tiene una barra diagonal final**\
   La dirección URL de la solicitud tiene una barra diagonal final.
* **not cacheable: URL de solicitud no en reglas de caché**\
   Las reglas de caché de granja deniegan explícitamente el almacenamiento en caché del resultado de alguna URL de solicitud.
* **not cacheable: acceso denegado denegado denegado**\
   El verificador de autorización de la granja denegó el acceso al archivo almacenado en caché.
* **not cacheable: sesión no válida**
La caché de la granja está regulada por un gestor de sesión (la configuración contiene un `sessionmanagement` nodo) y la sesión del usuario ya no es válida.
* **not cacheable: response contiene`no_cache `** el servidor remoto devuelta un `Dispatcher: no_cache` encabezado, lo que prohíbe al despachante almacenar la salida en caché.
* **not cacheable: la longitud del contenido de respuesta es cero la**
longitud de contenido de la respuesta es cero; el despachante no creará un archivo de longitud cero.
