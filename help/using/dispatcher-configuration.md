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
source-git-commit: a997d2296e80d182232677af06a2f4ab5a14bfd5

---


# Configuring Dispatcher{#configuring-dispatcher}

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher incrustada en la documentación de una versión anterior de AEM.

Las secciones siguientes describen cómo configurar distintos aspectos de Dispatcher.

## Support for IPv4 and IPv6 {#support-for-ipv-and-ipv}

Todos los elementos de AEM y Dispatcher se pueden instalar en redes ipv 4 y ipv 6. See [IPV4 and IPV6](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html#AdditionalPlatformNotes).

## Dispatcher Configuration Files {#dispatcher-configuration-files}

By default the Dispatcher configuration is stored in the `dispatcher.any` text file, though you can change the name and location of this file during installation.

El archivo de configuración contiene una serie de propiedades de valor único o varios valores que controlan el comportamiento de Dispatcher:

* Property names are prefixed with a forward slash `/`.
* Multi-valued properties enclose child items using braces `{ }`.

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

For example, if the files `farm_1.any` through to `farm_5.any` contain the configuration of farms one to five, you can include them as follows:

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Using Environment Variables {#using-environment-variables}

Puede utilizar variables de entorno en propiedades de valor de cadena en el archivo dispatcher. any en lugar de preprogramar los valores. To include the value of an environment variable, use the format `${variable_name}`.

For example, if the dispatcher.any file is located in the same directory as the cache directory, the following value for the [docroot](dispatcher-configuration.md#main-pars-title-30) property can be used:

```xml
/docroot "${PWD}/cache"
```

As another example, if you create an environment variable named `PUBLISH_IP` that stores the hostname of the AEM publish instance, the following configuration of the [/renders](dispatcher-configuration.md#main-pars-127-25-0008) property can be used:

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Naming the Dispatcher Instance {#naming-the-dispatcher-instance-name}

Use the `/name` property to specify a unique name to identify your Dispatcher instance. `/name` La propiedad es una propiedad de nivel superior en la estructura de configuración.

## Defining Farms {#defining-farms-farms}

`/farms` La propiedad define uno o varios conjuntos de comportamientos Dispatcher, donde cada conjunto está asociado a sitios web o direcciones URL diferentes. `/farms` La propiedad puede incluir una sola granja o varias granjas:

* Utilice una sola granja cuando desee que Dispatcher gestione todas las páginas Web o sitios Web del mismo modo.
* Cree varias granjas cuando diferentes áreas del sitio Web o distintos sitios Web requieran un comportamiento de Dispatcher diferente.

`/farms` La propiedad es una propiedad de nivel superior en la estructura de configuración. To define a farm, add a child property to the `/farms` property. Utilice un nombre de propiedad que identifique exclusivamente a la granja dentro de la instancia Dispatcher.

The `/*farmname*` property is multi-valued, and contains other properties that define Dispatcher behavior:

* Las direcciones URL de las páginas a las que se aplica la granja.
* Una o más URL de servicio (normalmente de instancias de publicación de AEM) que se utilizarán para procesar documentos.
* Estadísticas que se utilizan para equilibrar varios documentos de equilibrio de carga.
* Hay otros comportamientos, como los que se almacenan en caché y dónde.

El valor puede haber incluido cualquier carácter alfanumérico (a-z, 0-9). The following example shows the skeleton definition for two farms named `/daycom` and `/docsdaycom`:

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
>Si utiliza más de una granja de procesamiento, la lista se evalúa en la parte inferior. This is particularly relevant when defining [Virtual Hosts](dispatcher-configuration.md#main-pars-117-15-0006) for your websites.

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

## Specify a Default Page (IIS Only) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>The `/homepage`parameter (IIS only) no longer works. Instead, you should use the [IIS URL Rewrite Module](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>If you are using Apache, you should use the `mod_rewrite` module. See the Apache web site documentation for information about `mod_rewrite` (for example, [Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). When using `mod_rewrite`, it is advisable to use the flag ** [&#39;passthrough|PT&#39; (pass through to next handler)](https://helpx.adobe.com/dispatcher/kb/DispatcherModReWrite.html)** to force the rewrite engine to set the `uri` field of the internal `request_rec` structure to the value of the `filename` field.

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

## Specifying the HTTP Headers to Pass Through {#specifying-the-http-headers-to-pass-through-clientheaders}

The `/clientheaders` property defines a list of HTTP headers that Dispatcher passes from the client HTTP request to the renderer (AEM instance).

De forma predeterminada, Dispatcher envía los encabezados HTTP estándar a la instancia de AEM. En algunos casos, es posible que quiera reenviar encabezados adicionales o quitar encabezados específicos:

* Agregue encabezados, como encabezados personalizados, que su instancia de AEM espera en la solicitud HTTP.
* Elimine encabezados, como encabezados de autenticación, que solo sean relevantes para el servidor web.

Si personaliza el conjunto de encabezados que se van a pasar, debe especificar una lista exhaustiva de encabezados, incluidos aquellos que normalmente se incluyen de forma predeterminada.

For example, a Dispatcher instance that handles page activation requests for publish instances requires the `PATH` header in the `/clientheaders` section. The `PATH` header enables communication between the replication agent and the dispatcher.

The following code is an example configuration for `/clientheaders`:

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

## Identifying Virtual Hosts {#identifying-virtual-hosts-virtualhosts}

The `/virtualhosts` property defines a list of all hostname/URI combinations that Dispatcher accepts for this farm. Puede utilizar el carácter asterisco (&quot; *&quot;) como comodín. Values for the / `virtualhosts` property use the following format:

```xml
[scheme]host[uri][*]
```

* `scheme`: (Opcional) O `https://` bien `https://.`
* `host`: El nombre o la dirección IP del equipo host y el número de puerto, si es necesario. (See [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23))
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

The following configuration handles *all* requests:

```xml
   /virtualhosts
    {
    "*"
    }
```

### Resolving the Virtual Host {#resolving-the-virtual-host}

When Dispatcher receives an HTTP or HTTPS request, it finds the virtual host value that best-matches the `host,` `uri`, and `scheme` headers of the request. Dispatcher evaluates the values in the `virtualhosts` properties in the following order:

* Dispatcher comienza en la granja más baja y avanza hacia arriba en el despachante. cualquier archivo.
* For each farm, Dispatcher begins with the topmost value in the `virtualhosts` property and progresses down the list of values.

Dispatcher encuentra el mejor valor de host virtual coincidente de la siguiente manera:

* The first-encountered virtual host that matches all three of the `host`, the `scheme`, and the `uri` of the request is used.
* If no `virtualhosts` values has `scheme` and `uri` parts that both match the `scheme` and `uri` of the request, the first-encountered virtual host that matches the `host` of the request is used.
* If no `virtualhosts` values have a host part that matches the host of the request, the topmost virtual host of the topmost farm is used.

Therefore, you should place your default virtual host at the top of the `virtualhosts` property in the topmost farm of your dispatcher.any file.

### Example Virtual Host Resolution {#example-virtual-host-resolution}

The following example represents a snippet from a dispatcher.any file that defines two Dispatcher farms, and each farm defines a `virtualhosts` property.

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

## Enabling Secure Sessions - /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized`**debe** configurarse en `"0"``/cache` la sección para habilitar esta función.

Cree una sesión segura para acceder a la granja de procesamiento a fin de que los usuarios deban iniciar sesión para acceder a cualquier página de la granja. Después de iniciar sesión, los usuarios pueden acceder a las páginas de la granja. See [Creating a Closed User Group](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/cug.html#CreatingTheUserGroupToBeUsed) for information about using this feature with CUGs. Also, see the Dispatcher [Security Checklist](/help/using/security-checklist.md) before going live.

The `/sessionmanagement` property is a subproperty of `/farms`.

>[!CAUTION]
>
>Si las secciones del sitio web utilizan diferentes requisitos de acceso, debe definir varias granjas.

**/sessionmanagement** tiene varios subparámetros:

**/directory** (obligatorio)

Directorio que almacena la información de la sesión. Si el directorio no existe, se crea.

>[!CAUTION]
>
> When configuring the directory sub-parameter **do not** point to the root folder (`/directory "/"`) as it can cause serious problems. Siempre debe especificar la ruta a la carpeta que almacena la información de la sesión. Por ejemplo:

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions"
  }
```

**/encode** (opcional)

Cómo se codifica la información de la sesión. Utilice &quot;md 5&quot; para la codificación utilizando el algoritmo md 5 o &quot;hexadecimal&quot; para la codificación hexadecimal. Si codifica los datos de la sesión, un usuario con acceso al sistema de archivos no podrá leer el contenido de la sesión. El valor predeterminado es &quot;md 5&quot;.

**/header** (opcional)

Nombre del encabezado HTTP o cookie que almacena la información de autorización. If you store the information in the http header, use `HTTP:<*header-name*>`. To store the information in a cookie, use `Cookie:<header-name>`. If you do not specify a value `HTTP:authorization` is used.

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

## Defining Page Renderers {#defining-page-renderers-renders}

La propiedad /renders define la URL a la que Dispatcher envía solicitudes para procesar un documento. The following example `/renders` section identifies a single AEM instance for rendering:

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

### Renders options {#renders-options}

**/timeout**

Especifica el tiempo de espera de conexión que accede a la instancia de AEM en milisegundos. El valor predeterminado es &quot;0&quot;, lo que hace que Dispatcher espere indefinidamente.

**/receiveTimeout**

Especifica el tiempo en milisegundos que se permite realizar una respuesta. El valor predeterminado es &quot;600000&quot;, lo que provoca que Dispatcher espere 10 minutos. Una configuración de &quot;0&quot; elimina el tiempo de espera completamente.\
Si se alcanza el tiempo de espera al analizar los encabezados de respuesta, se devuelve un estado HTTP de 504 (puerta de enlace incorrecta). Si se alcanza el tiempo de espera mientras se lee el cuerpo de respuesta, Dispatcher devolverá la respuesta incompleta al cliente pero eliminará cualquier archivo caché que pueda haber sido escrito.

**/ipv4**

Specifies whether Dispatcher uses the `getaddrinfo` function (for IPv6) or the `gethostbyname` function (for IPv4) for obtaining the IP address of the render. A value of 0 causes `getaddrinfo` to be used. A value of 1 causes `gethostbyname` to be used. El valor predeterminado es 0.

La función getaddrinfo devuelve una lista de direcciones IP. Dispatcher repite la lista de direcciones hasta que establece una conexión TCP/IP. Por lo tanto, la propiedad ipv 4 es importante cuando el nombre de host de procesamiento está asociado con direcciones IP mutliple y el host, como respuesta a la función getaddrinfo, devuelve una lista de direcciones IP que siempre están en el mismo orden. En este caso, debe utilizar la función gethostbyname para que la dirección IP con la que se conecta Dispatcher se asigne al azar.

El equilibrio de carga elástica de Amazon (ELB) es un servicio que responde a getaddrinfo con una lista potencialmente ordenada de direcciones IP.

**/secure**

If the `/secure` property has a value of &quot;1&quot; Dispatcher uses HTTPS to communicate with the AEM instance. For additional details, also see [Configuring Dispatcher to Use SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

With Dispatcher version **4.1.6**, you can configure the `/always-resolve` property as follows:

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

## Configuring Access to Content {#configuring-access-to-content-filter}

Use the `/filter` section to specify the HTTP requests that Dispatcher accepts. Todas las demás solicitudes se devuelven al servidor Web con un código de error 404 (página no encontrada). If no `/filter` section exists, all requests are accepted.

**Nota:** Las solicitudes del [statfile](dispatcher-configuration.md#main-pars-title-28) siempre se rechazan.

>[!CAUTION]
>
>See the [Dispatcher Security Checklist](security-checklist.md) for further considerations when restricting access using Dispatcher. Also, read the [AEM Security Cheklist](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html) for additional security details regarding your AEM installation.

La /filter ón consiste en una serie de reglas que deniegan o permiten el acceso al contenido según patrones en la parte de la línea de solicitud de la solicitud HTTP. Debe utilizar una estrategia de telefonía para su /filter ón:

* Primero, denegar el acceso a todo.
* Permitir el acceso al contenido según sea necesario.

### Defining a Filter {#defining-a-filter}

Each item in the `/filter` section includes a type and a pattern that is matched with a specific element of the request line or the entire request line. Cada filtro puede contener los siguientes elementos:

* **Tipo**: `/type` Indica si se debe permitir o denegar el acceso de las solicitudes que coinciden con el patrón. The value can be either `allow` or `deny`.

* **Elemento de la línea de solicitud:** Incluya `/method``/url`, `/query`o `/protocol` y un patrón para filtrar solicitudes según estas partes específicas de la parte de la línea de solicitud de la solicitud HTTP. El filtrado en elementos de la línea de solicitud (en lugar de en la línea de solicitud completa) es el método de filtro preferido.

* **Elementos avanzados de la línea de solicitud:** A partir de Dispatcher 4.2.0, hay cuatro nuevos elementos de filtro disponibles para su uso. These new elements are `/path`, `/selectors`, `/extension` and `/suffix` respectively. Incluya uno o más de estos elementos para controlar aún más los patrones de URL.

>[!NOTE]
>
>For more information about what part of the request line each of these elements references, see the [Sling URL Decomposition](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html) wiki page.

* **propiedad de resplandor**: La `/glob` propiedad se utiliza para coincidir con toda la línea de solicitud de la solicitud HTTP.

>[!CAUTION]
>
>El filtrado con globos está obsoleto en Dispatcher. As such, you should avoid using globs in the `/filter` sections since it may lead to security issues. Entonces, en lugar de:

`/glob "* *.css *"`

debe utilizar

`/url "*.css"`

#### The request-line Part of HTTP Requests {#the-request-line-part-of-http-requests}

HTTP/1.1 defines the [request-line](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) as follows:

*Método Request-URI HTTP-Version*&lt; CRLF &gt;

Los caracteres &lt; CRLF &gt; ponderan un retorno de carro seguido de un salto de línea. El siguiente ejemplo es la línea de solicitud que se recibe cuando un cliente solicita la página en la página del sitio Geometrixx-Outoors:

GET /content/geometrixx-outdoors/en.html HTTP .1 .1 &lt; CRLF &gt;

Los patrones deben tener en cuenta los caracteres de espacio en la línea de solicitud y los caracteres &lt; CRLF &gt;.

#### Double-quotes vs Single-quotes {#double-quotes-vs-single-quotes}

When creating your filter rules, use double quotation marks `"pattern"` for simple patterns. If you are using Dispatcher 4.2.0 or later and your pattern includes a regular expression, you must enclose the regex pattern `'(pattern1|pattern2)'` within single quotation marks.

#### Regular Expressions {#regular-expressions}

Después de Dispatcher 4.2.0, puede incluir expresiones regulares POSIX Extended en los patrones de filtro.

#### Troubleshooting Filters {#troubleshooting-filters}

If your filters are not triggering in the way you would expect, enable [Trace Logging](#trace-logging) on dispatcher so you can see which filter is intercepting the request.

#### Example Filter: Deny All {#example-filter-deny-all}

La siguiente sección de filtro de ejemplo hace que Dispatcher deniegue solicitudes de todos los archivos. Debe denegar el acceso a todos los archivos y, a continuación, permitir el acceso a áreas específicas.

```xml
  /0001  { /glob "*" /type "deny" }
```

Las solicitudes a un área denegada explícitamente dan como resultado un código de error 404 (página no encontrada) que se devuelve.

#### Example Filter: Deny Acess to Specific Areas {#example-filter-deny-acess-to-specific-areas}

Los filtros también permiten denegar el acceso a varios elementos, por ejemplo páginas ASP y áreas sensibles dentro de una instancia de publicación. El filtro siguiente deniega el acceso a las páginas ASP:

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Example Filter: Enable POST Requests {#example-filter-enable-post-requests}

El siguiente filtro de ejemplo permite enviar datos de formulario mediante el método POST:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Example Filter: Allow Access to the Workflow Console {#example-filter-allow-access-to-the-workflow-console}

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

#### Example filter: Using Regular Expressions {#example-filter-using-regular-expressions}

Este filtro permite extensiones en directorios de contenido no públicos usando una expresión regular, definida aquí entre comillas simples:

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Example filter: Filter Additional Elements of a Request URL {#example-filter-filter-additional-elements-of-a-request-url}

Below is a rule example that blocks content grabbing from the `/content` path and its subtree, using filters for path, selectors and extensions:

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }
```

### Example /filter section {#example-filter-section}

Al configurar Dispatcher, debe restringir el acceso externo en la medida de lo posible. El siguiente ejemplo proporciona un acceso mínimo para los visitantes externos:

* `/content`
* contenido misceláneo como diseños y bibliotecas de cliente; por ejemplo:

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

After you create filters, [test page access](dispatcher-configuration.md#main-pars-title-19) to ensure your AEM instance is secure.

The following /filter section of the dispatcher.any file can be used as a basis in your [Dispatcher configuration](dispatcher-configuration.md) file.

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
>Cuando se utiliza con Apache, diseñe los patrones de URL de filtro según la propiedad dispatcheruseprocessedurl del módulo Dispatcher. (See [Apache Web Server - Configure your Apache Web Server for Dispatcher](dispatcher-install.md#main-pars-55-35-1022).)

>[!NOTE]
>
>Los filtros 0030 y 0031 relativos a Dynamic Media se aplican a AEM 6.0 y posterior.

Tenga en cuenta las siguientes recomendaciones si decide ampliar el acceso:

* External access to `/admin` should always be *completely* disabled if you are using CQ version 5.4 or an earlier version.

* Care must be taken when allowing access to files in `/libs`. El acceso debe permitirse por separado.
* Denegar el acceso a la configuración de replicación para que no pueda verse:

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Denegar el acceso al gadgets Google Inversi inverso:

   * `/libs/opensocial/proxy*`

Depending on your installation, there might be additional resources under `/libs`, `/apps` or elsewhere, that must be made available. You can use the `access.log` file as one method of determining resources that are being accessed externally.

>[!CAUTION]
>
>El acceso a consolas y directorios puede presentar un riesgo de seguridad para los entornos de producción. A menos que tenga justificaciones explícitas, deben permanecer desactivadas (comentadas).

>[!CAUTION]
>
>If you are [using reports in a publish environment](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/reporting.html#UsingReportsinaPublishEnvironment) you should configure Dispatcher to deny access to `/etc/reports` for external visitors.

### Restricting Query Strings {#restricting-query-strings}

Since Dispatcher version 4.1.5, use the `/filter` section to restrict query strings. It is highly recommended to explicitly allow query strings and exclude generic allowance through `allow` filter elements.

A single entry can have either *glob* or some combination of *method*,*url*,*query* and *version* but not both. The following example allows the `a=*` query string and denies all other query strings for URLs that resolve to the `/etc` node:

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>If a rule contains a `/query`, it will only match requests that contain a query string and match the provided query pattern.
>
>In above example, if requests to `/etc` that have no query string should be allowed as well, the following rules would be required:


```xml
/filter {  
>/0001 { /type "deny" /method “*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type “deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Testing Dispatcher Security {#testing-dispatcher-security}

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
* /content/add_valid_page.query.json?statement=//*[@transportPassword]/(@transportPassword%20|%20@transportUri%20|%20@transportUser)
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

## Enabling Access to Vanity URLs {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The "com.adobe.granite.dispatcher.vanityurl.content" package needs to be made public before publishing this contnet.</p>
 -->

Configure Dispatcher para permitir el acceso a URL personales configuradas para sus páginas CQ o AEM.

Cuando se habilita el acceso a URL personales, Dispatcher llama periódicamente a un servicio que se ejecuta en la instancia de procesamiento para obtener una lista de URL personales. Dispatcher almacena esta lista en un archivo local. When a request for a page is denied due to a filter in the `/filter` section, Dispatcher consults the list of vanity URLs. Si la URL denegada está en la lista, Dispatcher permite el acceso a la URL de vanidad.

To enable access to vanity URLs, add a `/vanity_urls` section to the `/farms` section, similar to the following example:

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

The `/vanity_urls` section contains the following properties:

* `/url`: La ruta al servicio URL de vanidad que se ejecuta en la instancia de procesamiento. The value of this property must be `"/libs/granite/dispatcher/content/vanityUrls.html"`.

* `/file`: La ruta al archivo local donde Dispatcher almacena la lista de URL personales. Asegúrese de que Dispatcher tiene acceso de escritura a este archivo.
* `/delay`: (Segundos) El tiempo entre llamadas al servicio URL de vanidad.

>[!NOTE]
>
>If your render is an instance of AEM you must install the [VanityURLS-Components](https://www.adobeaemcloud.com/content/marketplace/marketplaceProxy.html?packagePath=/content/companies/public/adobe/packages/cq600/component/vanityurls-components) package to install the vanity URL service. (See [Signing In to Package Share](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/package-manager.html#SigningIntoPackageShare).)

Siga el procedimiento siguiente para permitir el acceso a URL personales.

1. Si el servicio de procesamiento es una instancia de AEM, instale el paquete com. adobe. granite. dispatcher. vanityurl. content en la instancia de publicación (consulte la nota anterior).
1. For each vanity URL that you have configured for an AEM or CQ page, ensure that the ` [/filter](dispatcher-configuration.md#main-pars_134_32_0009)` configuration denies the URL. Si es necesario, agregue un filtro que deniegue la dirección URL.
1. Add the `/vanity_urls` section below `/farms`.
1. Reinicie el servidor Web de Apache.

## Forwarding Syndication Requests - /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

Normalmente, las solicitudes de distribución solo se destinan a Dispatcher. Por lo tanto, de forma predeterminada no se envían al procesador (por ejemplo, una instancia de AEM).

Si es necesario, establezca la propiedad /propagateSyndPost en &quot;1&quot; para enviar solicitudes de distribución a Dispatcher. Si se establece, debe asegurarse de que las solicitudes POST no se denieguen en la sección del filtro.

## Configuring the Dispatcher Cache - /cache {#configuring-the-dispatcher-cache-cache}

The `/cache` section controls how Dispatcher caches documents. Configure varias subpropiedades para implementar sus estrategias de almacenamiento en caché:


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
>For permission-sensitive caching, read [Caching Secured Content](permissions-cache.md).

### Specifying the Cache Directory {#specifying-the-cache-directory}

`/docroot` La propiedad identifica el directorio donde se almacenan los archivos en caché.

>[!NOTE]
>
>El valor debe ser exactamente la misma ruta que la raíz del documento del servidor Web para que Dispatcher y el servidor Web gestione los mismos archivos.\
>El servidor Web es responsable de proporcionar el código de estado correcto cuando se utiliza el archivo de caché de Dispatcher, por eso es importante que también lo encuentre.

Si utiliza varias granjas, cada granja debe utilizar una raíz de documento diferente.

### Naming the Statfile {#naming-the-statfile}

The `/statfile` property identifies the file to use as the statfile. Dispatcher utiliza este archivo para registrar el tiempo de la actualización de contenido más reciente. El archivo puede ser cualquier archivo en el servidor Web.

El archivo no tiene contenido. Cuando se actualiza el contenido, Dispatcher actualiza la marca de fecha y hora. El archivo predeterminado se denomina. stat y se almacena en el docroot. Dispatcher bloquea el acceso al archivo.

>[!NOTE]
>
>If `/statfileslevel` is configured, Dispatcher ignores the `/statfile` property and uses .stat as the name.

### Serving Stale Documents When Errors Occur {#serving-stale-documents-when-errors-occur}

The `/serveStaleOnError` property controls whether Dispatcher returns invalidated documents when the render server returns an error. De forma predeterminada, cuando se toca un archivo e invalida el contenido almacenado en caché, Dispatcher elimina el contenido almacenado en caché la próxima vez que se solicita.

If `/serveStaleOnError` is set to &quot;1&quot;, Dispatcher does not delete invalidated content from the cache unless the render server returns a successful response. Una respuesta de 5 xx de AEM o un tiempo de espera de conexión hace que Dispatcher proporcione el contenido obsoleto y responda con el estado de HTTP de 111 (error de validación).

### Caching When Authentication is Used {#caching-when-authentication-is-used}

`/allowAuthorized` La propiedad controla si las solicitudes que contienen la siguiente información de autenticación se almacenan en caché:

* The `authorization` header.
* A cookie named `authorization`.
* A cookie named `login-token`.

De forma predeterminada, las solicitudes que incluyen esta información de autenticación no se almacenan en caché porque la autenticación no se realiza cuando se devuelve un documento en caché al cliente. Esta configuración evita que Dispatcher ofrezca documentos almacenados en caché a usuarios que no dispongan de los derechos necesarios.

Sin embargo, si sus requisitos permiten el almacenamiento en caché de documentos autenticados, establezca /allowAuthorized en uno:

`/allowAuthorized "1"`

>[!NOTE]
>
>To enable session management (using the `/sessionmanagement` property), the `/allowAuthorized` property must be set to `"0"`.

### Specifying the Documents to Cache {#specifying-the-documents-to-cache}

The `/rules` property controls which documents are cached according to the document path. Independientemente de la propiedad /rules, Dispatcher nunca almacena en caché un documento en las siguientes circunstancias:

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
>Los métodos GET o HEAD (para los encabezados HTTP) son almacenables por Dispatcher. For additional information on response header caching, see the [Caching HTTP Response Headers](dispatcher-configuration.md#caching-http-response-headers) section.

Each item in the /rules property includes a [glob](#designing-patterns-for-glob-properties) pattern and a type:

* El patrón de resplandor se utiliza para coincidir con la ruta del documento.
* El tipo indica si se almacenan en caché los documentos que coinciden con el patrón de resplandor. El valor puede ser permitir (para almacenar en caché el documento) o denegar (para procesar siempre el documento).

Si no tiene páginas dinámicas (más allá de aquellas ya excluidas por las reglas anteriores), puede configurar Dispatcher para almacenar en caché todo. La sección Reglas tiene este aspecto:

```xml
/rules
  { 
    /0000  {  /glob "*"   /type "allow" }
  }
```

For information about glob properties, see [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties).

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

En los servidores Web Apache, puede comprimir los documentos en caché. La compresión permite que Apache devuelva el documento en un formulario comprimido si lo solicita el cliente. Compression is done automatically by enabling the Apache module `mod_deflate`, for example:

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

### Invalidating Files by Folder Level {#invalidating-files-by-folder-level}

Use the `/statfileslevel` property to invalidate cached files according to their path:

* Dispatcher creates `.stat`files in each folder from the docroot folder to the level that you specify. La carpeta docroot es level 0.
* Files are invalidated by touching the `.stat` file. The `.stat` file&#39;s last modification date is compared to the last modification date of a cached document. The document is re-fetched if the `.stat` file is newer.

* When a file located at a certain level is invalidated then **all** `.stat` files from the docroot **to** the level of the invalidated file or the configured `statsfilevel` (whichever is smaller) will be touched.

   * For example: if you set the `statfileslevel` property to 6 and a file is invalidated at level 5 then every `.stat` file from docroot to 5 will be touched. Continuando con este ejemplo, si un archivo se invalida en el nivel 7, cada uno. `stat` de docroot a 6 se tocará (ya que `/statfileslevel = "6"`).

Solo se afectan los recursos** a lo largo de la ruta** al archivo invalidado. Consider the following example: a website uses the structure `/content/myWebsite/xx/.` If you set `statfileslevel` as 3, a `.stat`file is created as follows:

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

When a file in `/content/myWebsite/xx` is invalidated then every `.stat` file from docroot down to `/content/myWebsite/xx`is touched. This would be the case only for `/content/myWebsite/xx` and not for example `/content/myWebsite/yy` or `/content/anotherWebSite`.

>[!NOTE]
>
>Invalidation can be prevented by sending an additional Header `CQ-Action-Scope:ResourceOnly`. Esto se puede utilizar para vaciar recursos concretos sin invalidar otras partes de la caché. See [this page](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) and [Manually Invalidating the Dispatcher Cache](https://helpx.adobe.com/experience-manager/dispatcher/using/page-invalidate.html) for additional details.

>[!NOTE]
>
>If you specify a value for the `/statfileslevel` property, the `/statfile` property is ignored.

### Automatically Invalidating Cached Files {#automatically-invalidating-cached-files}

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

For information about glob properties, see [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties).

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

### Using custom invalidation scripts {#using-custom-invalidation-scripts}

La propiedad /invalidateHandler permite definir una secuencia de comandos llamada para cada solicitud de invalidación recibida por Dispatcher.

Se llama con los siguientes argumentos:

* Control\
   Ruta de contenido invalidada
* Acción\
   Acción de replicación (p. ej. Activar, Desactivar)
* Ámbito de acción\
   The replication Action&#39;s Scope (empty, unless a header of `CQ-Action-Scope: ResourceOnly` is sent, see [Invalidating Cached Pages from AEM](page-invalidate.md) for details)

Esto puede utilizarse para cubrir distintos casos de uso, como invalidar otros cachés específicos de la aplicación, o para manejar casos en los que la URL externalizada de una página y su lugar en el docroot no coincide con la ruta de contenido.

En la secuencia de comandos de ejemplo siguiente se registra cada solicitud invalidada a un archivo.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### sample invalidation handler script {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Limiting the Clients That Can Flush the Cache {#limiting-the-clients-that-can-flush-the-cache}

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

For information about glob properties, see [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties).

>[!CAUTION]
>
>Se recomienda definir /allowedClients.
>
>Si esto no se realiza, cualquier cliente puede enviar una llamada para borrar la caché; Si esto se realiza repetidamente, puede afectar gravemente el rendimiento del sitio.

### Ignoring URL Parameters {#ignoring-url-parameters}

The `ignoreUrlParams` section defines which URL parameters are ignored when determining whether a page is cached or delivered from cache:

* Cuando una dirección URL de solicitud contiene parámetros que se omiten todos, la página se almacena en caché.
* Cuando una dirección URL de solicitud contiene uno o varios parámetros que no se omiten, la página no se almacena en caché.

Cuando se omite un parámetro para una página, la página se almacena en caché la primera vez que se solicita la página. Las solicitudes posteriores de la página se sirven en la página en caché, independientemente del valor del parámetro en la solicitud.

To specify which parameters are ignored, add glob rules to the `ignoreUrlParams` property:

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

Using the example `ignoreUrlParams` value, the following HTTP request causes the page to be cached because the `q` parameter is ignored:

```xml
GET /mypage.html?q=5
```

Using the example `ignoreUrlParams` value, the following HTTP request causes the page to **not** be cached because the `p` parameter is not ignored:

```xml
GET /mypage.html?q=5&p=4
```

For information about glob properties, see [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties).

### Caching HTTP Response Headers {#caching-http-response-headers}

>[!NOTE]
>
>This feature is avaiable with version **4.1.11** of the Dispatcher.

The `/headers` property allows you to define the HTTP header types that are going to be cached by the Dispatcher. En la primera solicitud a un recurso sin almacenar en caché, todos los encabezados que coincidan con uno de los valores configurados (ver la muestra de configuración siguiente) se almacenan en un archivo independiente, junto al archivo caché. En solicitudes posteriores al recurso en caché, los encabezados almacenados se agregan a la respuesta.

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
>Asimismo, tenga en cuenta que no se permiten los caracteres de globalización de archivos. For more details, see [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties).

>[!NOTE]
>
>Si necesita Dispatcher para almacenar y enviar encabezados de respuesta etag desde AEM, haga lo siguiente:
>
>* Add the header name in the `/cache/headers`section.
>* Add the following [Apache directive](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) in the Dispatcher related section:
>



```xml
FileETag none
```

### Dispatcher Cache File Permissions {#dispatcher-cache-file-permissions}

The `mode` property specifies what file permissions are applied to new directories and files in the cache. This setting is restricted by the `umask` of the calling process. Es un número octal construido a partir de la suma de uno o más de los valores siguientes:

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

### Throttling .stat file touching {#throttling-stat-file-touching}

With the default `/invalidate` property, every activation effectively invalidates all `.html` files (when their path matches the `/invalidate` section). En un sitio web con mucho tráfico, varias, las activaciones subsiguientes aumentarán la carga de cpu en el back-end. In such a scenario, it would be desirable to &quot;throttle&quot; `.stat` file touching to keep the website responsive. You can do this by using the `/gracePeriod` property.

The `/gracePeriod` property defines the number of seconds a stale, auto-invalidated resource may still be served from the cache after the last occuring activation. La propiedad se puede utilizar en una configuración en la que un lote de activaciones invalidaría repetidamente toda la caché. El valor recomendado es de 2 segundos.

For additional details, also read the `/invalidate` and `/statfileslevel`sections above.

## Configuring Time Based Cache Invalidation - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

If set, the `enableTTL` property will evaluate the response headers from the backend, and if they contain a `Cache-Control` max-age or `Expires` date, an auxiliary, empty file next to the cache file is created, with modification time equal to the expiry date. Cuando se solicita el archivo en caché, después de la modificación se vuelve a solicitar automáticamente del servidor.

You can enable the feature by adding this line to the `dispatcher.any` file:

```xml
/enableTTL "1"
```

>[!NOTE]
>
>This feature is avaiable with version **4.1.11** of the Dispatcher.

## Configuring Load Balancing - /statistics {#configuring-load-balancing-statistics}

The `/statistics` section defines categories of files for which Dispatcher scores the responsiveness of each render. Dispatcher usa las puntuaciones para determinar cuál es la representación para enviar una solicitud.

Cada categoría que cree define un patrón de resplandor. Dispatcher compara el URI del contenido solicitado con estos patrones para determinar la categoría del contenido solicitado:

* El orden de las categorías determina el orden en que se comparan con el URI.
* El primer patrón de categoría que coincide con la URI es la categoría del archivo. No se evalúan más patrones de categoría.

Dispatcher admite un máximo de 8 categorías estadísticas. Si define más de 8 categorías, solo se utilizará la primera 8.

**Procesar selección**

Cada vez que Dispatcher requiere una página representada, utiliza el siguiente algoritmo para seleccionar el procesamiento:

1. If the request contains the render name in a `renderid` cookie, Dispatcher uses that render.
1. If the request includes no `renderid` cookie, Dispatcher compares the render statistics:

   1. Dispatcher determina el cateogry del URI de la solicitud.
   1. Dispatcher determina qué representación tiene la puntuación de respuesta más baja para esa categoría y selecciona que se procesa.

1. Si aún no se selecciona ningún procesamiento, utilice el primer procesamiento de la lista.

La puntuación de una categoría de procesamiento se basa en tiempos de respuesta anteriores, como así también en conexiones exitosas anteriores y fallidas que Dispatcher intenta. Para cada intento, se actualiza la puntuación de la categoría del URI solicitado.

>[!NOTE]
>
>Si no utiliza equilibrio de carga, puede omitir esta sección.

### Defining Statistics Categories {#defining-statistics-categories}

Defina una categoría para cada tipo de documento para el cual desee mantener las estadísticas para la selección de procesamiento. La /statistics ón contiene una /categories ón. Para definir una categoría, agregue una línea debajo de la /categories ón que tenga el siguiente formato:

`/name { /glob "pattern"}`

The category `name` must be unique to the farm. The `pattern` is described in the [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties) section.

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

### Reflecting Server Unavailability in Dispatcher Statistics {#reflecting-server-unavailability-in-dispatcher-statistics}

`/unavailablePenalty` La propiedad establece el tiempo (en décimas de segundo) que se aplica a las estadísticas de procesamiento cuando falla una conexión con el procesamiento. Dispatcher agrega el tiempo a la categoría de estadísticas que coincide con el URI solicitado.

Por ejemplo, la penalización se aplica cuando no se puede establecer la conexión TCP/IP con el nombre de host o puerto designado, ya sea porque AEM no se está ejecutando (ni escuchando) o debido a un problema relacionado con la red.

`/unavailablePenalty` La propiedad es un elemento secundario directo de `/farm` la sección (un elemento secundario de `/statistics` la sección).

If no `/unavailablePenalty` property exists, a value of &quot;1&quot; is used.

```xml
/unavailablePenalty "1"
```

## Identifying a Sticky Connection Folder - /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

`/stickyConnectionsFor` La propiedad define una carpeta que contiene documentos adhesivos; a esto se accede mediante la dirección URL. Dispatcher envía todas las solicitudes, desde un único usuario, que están en esta carpeta a la misma instancia de procesamiento. Las conexiones adhesivas garantizan que los datos de la sesión estén presentes y sean coherentes para todos los documentos. This mechanism uses the `renderid` cookie.

El siguiente ejemplo define una conexión adhesiva a la carpeta /products:

```xml
/stickyConnectionsFor "/products"
```

When a page is composed of content from several content nodes, include the `/paths` property that lists the paths to the content. For example, a page contains content from `/content/image`, `/content/video`, and `/var/files/pdfs`. La siguiente configuración permite conexiones adhesivas para todo el contenido de la página:

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

When sticky connections are enabled, the dispatcher module sets the `renderid` cookie. This cookie doesn&#39;t have the `httponly` flag, which should be added in order to enhance security. You can do this by setting the `httpOnly` property in the `/stickyConnections` node of a `dispatcher.any` configuration file. The property&#39;s value (either 0 or 1) defines whether the `renderid` cookie has the `HttpOnly` attribute appended. El valor predeterminado es 0, lo que significa que no se agregará el atributo.

For additional information about the `httponly` flag, read [this page](https://www.owasp.org/index.php/HttpOnly).

### secure {#secure}

When sticky connections are enabled, the dispatcher module sets the `renderid` cookie. This cookie doesn&#39;t have the **secure** flag, which should be added in order to enhance security. You can do this by setting the `secure` property in the `/stickyConnections` node of a `dispatcher.any` configuration file. The property&#39;s value (either 0 or 1) defines whether the `renderid` cookie has the `secure` attribute appended. El valor predeterminado es 0, lo que significa que se agregará el atributo si * * la solicitud entrante es segura. Si el valor se establece en 1, se agregará el indicador seguro independientemente de si la solicitud entrante es segura o no.

## Handling Render Connection Errors {#handling-render-connection-errors}

Configure el comportamiento de Dispatcher cuando el servidor de procesamiento devuelve un error 500 o no está disponible.

### Specifying a Health Check Page {#specifying-a-health-check-page}

Use the `/health_check` property to specify a URL that is checked when a 500 status code occurs. If this page also returns a 500 status code the instance is considered to be unavailable and a configurable time penalty ( `/unavailablePenalty`) is applied to the render before retrying.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### Specifying the Page Retry Delay {#specifying-the-page-retry-delay}

The / `retryDelay` property sets the time (in seconds) that Dispatcher waits between rounds of connection attempts with the farm renders. Para cada ronda, el número máximo de veces que Dispatcher intenta establecer una conexión con un procesamiento es el número de procesadores en la granja.

Dispatcher uses a value of `"1"` if `/retryDelay` is not explicitly defined. El valor predeterminado es adecuado en la mayoría de los casos.

```xml
/retryDelay "1"
```

### Configuring the Number of Retries {#configuring-the-number-of-retries}

`/numberOfRetries` La propiedad define el número máximo de rondas de conexión que Dispatcher realiza con los procesadores. Si Dispatcher no se puede conectar correctamente a un procesamiento después de este número de intentos, Dispatcher devuelve una respuesta fallida.

Para cada ronda, el número máximo de veces que Dispatcher intenta establecer una conexión con un procesamiento es el número de procesadores en la granja. Therefore, the maximum number of times that Dispatcher attempts a connection is ( `/numberOfRetries`) x (the number of renders).

If the value is not explicitly defined, the default value is **5**.

```xml
/numberOfRetries "5"
```

### Using the Failover Mechanism {#using-the-failover-mechanism}

Habilitar el mecanismo de conmutación por error en la granja Dispatcher para volver a enviar solicitudes a diferentes procesamientos cuando falla la solicitud original. Cuando se habilita la conmutación por error, Dispatcher tiene el siguiente comportamiento:

* Cuando una solicitud a un procesamiento devuelve el estado HTTP 503 (UNAVAILABLE), Dispatcher envía la solicitud a un procesamiento diferente.
* When a request to a render returns HTTP status 50x (other than 503), Dispatcher sends a request for the page that is configured for the `health_check` property.

   * Si la comprobación de estado devuelve 500 (INTERNAL_ SERVER_ ERROR), Dispatcher envía la solicitud original a otro procesamiento.
   * Si la comprobación de santch devuelve el estado HTTP 200, Dispatcher devuelve el error HTTP 500 inicial al cliente.

Para habilitar la conmutación por error, agregue la siguiente línea a la granja (o sitio web):

```xml
/failover "1" 
```

>[!NOTE]
>
>To retry HTTP requests that contain a body, Dispatcher sends a `Expect: 100-continue` request header to the render before spooling the actual contents. CQ 5.5 con CQSE, y responde inmediatamente con 100 (CONTINUE) o con un código de error. Otros contenedores servlet también deben admitir esto.

## Ignoring Interruption Errors - /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Esta opción no suele ser necesaria. Solo debe utilizarlo cuando vea los siguientes mensajes de registro:
>
>`Error while reading response: Interrupted system call`

Any file system oriented system call can be interrupted `EINTR` if the object of the system call is located on a remote system accessed via NFS. Si estas llamadas del sistema pueden agotar el tiempo de espera o si se interrumpe se basa en cómo se montó el sistema de archivos subyacente en el equipo local.

Utilice el parámetro /ignoreEINTR si su instancia tiene dicha configuración y el registro contiene el siguiente mensaje:

`Error while reading response: Interrupted system call`

Dispatcher lee la respuesta del servidor remoto (es decir, AEM) utilizando un bucle que puede representarse como:

`while (response not finished) {  
read more data  
}`

Such messages can be generated when the `EINTR` occurs in the &quot; `read more data`&quot; section and are caused by the reception of a signal before any data was received.

To ignore such interrupts you can add the following parameter to `dispatcher.any` (before `/farms`):

`/ignoreEINTR "1"`

Setting `/ignoreEINTR` to `"1"` causes Dispatcher to continue to attempt to read data until the complete response is read. El valor predeterminado es 0 y desactiva la opción.

## Designing Patterns for glob Properties {#designing-patterns-for-glob-properties}

Several sections in the Dispatcher configuration file use `glob` properties as selection criteria for client requests. Los valores de las propiedades de resplandor son patrones que Dispatcher compara con un aspecto de la solicitud, como la ruta del recurso solicitado o la dirección IP del cliente. For example, the items in the `/filter` section use glob patterns to identify the paths of the pages that Dispatcher acts on or rejects.

Los valores de resplandor pueden incluir caracteres comodín y caracteres alfanuméricos para definir el patrón.

| Carácter comodín | Descripción | Ejemplos |
|--- |--- |--- |
| `*` | Coincide con cero o con más instancias contiguas de cualquier carácter de la cadena. The final character of the match is determined by either of the following situations: <br/>A character in the string matches the next character in the pattern, and the pattern character has the following characteristics:<br/><ul><li>No es *</li><li>Not a?</li><li>Carácter literal (incluyendo un espacio) o una clase de caracteres.</li><li>Se llega al final del patrón.</li></ul>Dentro de una clase de caracteres, el carácter se interpreta literalmente. | `*/geo*` Coincide con cualquier página debajo del `/content/geometrixx` nodo y `/content/geometrixx-outdoors` del nodo. The following HTTP requests match the glob pattern: <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*`<br/>Coincide con cualquier página debajo del `/content/geometrixx-outdoors` nodo. For example, the following HTTP request matches the glob pattern: <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Coincide con cualquier carácter individual. Utilice clases de caracteres externos. Dentro de una clase de caracteres, este carácter se interpreta literalmente. | `*outdoors/??/*`<br/> Coincide con las páginas de cualquier idioma del sitio geometrixx outdoors. For example, the following HTTP request matches the glob pattern: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>La siguiente solicitud no coincide con el patrón de resplandor: <br/><ul><li>&quot; GET /content/geometrixx-outdoors/en.html &quot;</li></ul> |
| `[ and ]` | Detiene el principio y el final de una clase de caracteres. Las clases de caracteres pueden incluir uno o más rangos de caracteres y caracteres únicos.<br/>Se produce una coincidencia si el carácter objetivo coincide con cualquiera de los caracteres de la clase de caracteres o dentro de un intervalo definido.<br/>Si no se incluye el corchete de cierre, el patrón no produce coincidencias. | `*[o]men.html*`<br/> Coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*`<br/>Coincide con las siguientes solicitudes HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Denota un rango de caracteres. Para utilizar en clases de caracteres. Fuera de una clase de caracteres, este carácter se interpreta literalmente. | `*[m-p]men.html*` Coincide con la siguiente solicitud HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>Does not match the following HTTP request:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Deniega la clase de caracteres o caracteres a continuación. Utilice solamente para negar caracteres y rangos de caracteres dentro de clases de caracteres. Equivalent to the `^ wildcard`. <br/>Fuera de una clase de caracteres, este carácter se interpreta literalmente. | `*[!o]men.html*`<br/> Coincide con la siguiente solicitud HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>No coincide con la siguiente solicitud HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> No coincide con la siguiente solicitud HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` o `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Deniega el carácter o el rango de caracteres a continuación. Se utiliza para negar solo caracteres y rangos de caracteres dentro de clases de caracteres. Equivalent to the `!` wildcard character. <br/>Fuera de una clase de caracteres, este carácter se interpreta literalmente. | The examples for the `!` wildcard character apply, substituting the `!` characters in the example patterns with `^` characters. |


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

## Logging {#logging}

En la configuración del servidor web, puede establecer:

* La ubicación del archivo de registro Dispatcher.
* Nivel de registro.

Consulte la documentación del servidor web y el archivo Léame de la instancia de Dispatcher para obtener más información.

**Apache Rotated/Named Logs**

If using an **Apache** web server you can use the standard functionality for rotated and/or piped logs. Por ejemplo, utilizando registros acoplados:

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Se rotará automáticamente:

* el archivo de registro de Dispatcher; con una marca de tiempo en la extensión (registros/dispatcher. log % Y % m % d).
* semanalmente (60 x 60 x 24 x 7 = 604800 segundos).

Please see the Apache web server documentation on Log Rotation and Piped Logs; for example [Apache 2.4](https://httpd.apache.org/docs/2.4/logs.html).

>[!NOTE]
>
>Tras la instalación, el nivel de registro predeterminado es alto (es decir, level 3 = Debug), de modo que Dispatcher registra todos los errores y advertencias. Esto es muy útil en las fases iniciales.
>
>However, this requires additional resources, so when the Dispatcher is working smoothly *according to your requirements*, you can(should) lower the log level.

### Trace Logging {#trace-logging}

Entre otras mejoras para Dispatcher, la versión 4.2.0 también introduce el registro de rastreo.

Este es un nivel superior que el registro de depuración, que muestra información adicional en los registros. Agrega registro para:

* Valores de los encabezados reenviados;
* Regla que se aplica para una acción determinada.

You can enable Trace Logging by setting the log level to `4` in your web server.

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

## Confirming Basic Operation {#confirming-basic-operation}

Para confirmar la operación básica y la interacción del servidor web, Dispatcher y la instancia de AEM, puede seguir estos pasos:

1. Set the `loglevel` to `3`.

1. Inicie el servidor Web; esto también inicia Dispatcher.
1. Inicie la instancia de AEM.
1. Compruebe los archivos de registro y error de su servidor web y Dispatcher.\
   Según el servidor web, debería ver mensajes como:\
   `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured`\
   y:\
   `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Navegar por el sitio web a través del servidor web. Confirme que el contenido se muestre según sea necesario.\
   For example, on a local installation where AEM runs on port `4502` and the web server on `80` access the Websites console using both:\
   ` https://localhost:4502/libs/wcm/core/content/siteadmin.html  
https://localhost:80/libs/wcm/core/content/siteadmin.html  
`Los resultados deberían ser idénticos. Confirme el acceso a otras páginas con el mismo mecanismo.

1. Compruebe que el directorio de la memoria caché se esté rellenando.
1. Active una página para comprobar que la caché se esté vaciando correctamente.
1. If everything is operating correctly you can reduce the `loglevel` to `0`.

## Using Multiple Dispatchers {#using-multiple-dispatchers}

En las configuraciones complejas, puede utilizar varios Dispatcher. Por ejemplo, puede utilizar:

* un despachante para publicar un sitio web en la intranet
* un segundo despachante, bajo una dirección diferente y con diferentes configuraciones de seguridad, para publicar el mismo contenido en Internet.

En este caso, asegúrese de que cada solicitud pasa sólo por un despachante. Dispatcher no gestiona las solicitudes procedentes de otro despachante. Por lo tanto, asegúrese de que ambos Dispatcher accedan directamente al sitio Web de AEM.

## Debugging {#debugging}

When adding the header `X-Dispatcher-Info` to a request, Dispatcher answers whether the target was cached, returned from cached or not cacheable at all. The response header `X-Cache-Info` contains this information in a readable form. Puede utilizar estos encabezados de respuesta para depurar problemas relacionados con respuestas almacenadas en caché por Dispatcher.

This functionality is not enabled by default, so in order for the response header `X-Cache-Info` to be included, the farm must contain the following entry:

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

Also, the `X-Dispatcher-Info` header does not need a value, but if you use `curl` for testing you must supply a value in order to send the header, such as:

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/we-retail/us/en.html
```

Below is a list containing the response headers that `X-Dispatcher-Info` will return:

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
   La plantilla de nombre de archivo temporal excede el nombre de archivo más largo posible en el sistema. El despachante crea primero un archivo temporal antes de crear o sobrescribir el archivo en caché. The temporary file name is the target file name with the characters `_YYYYXXXXXX` appended to it, where the `Y` and `X` will be replaced to create a unique name.
* **not cacheable: La dirección URL de solicitud no tiene extensión**\
   The request URL has no extension, or there is a path following the file extension, for example: `/test.html/a/path`.
* **not cacheable: request was&#39;t GET OR HEAD**
The HTTP method is ni a GET ni a HEAD. El despachante asume que el resultado contendrá datos dinámicos que no deberían almacenarse en caché.
* **not cacheable: la solicitud contiene una cadena de consulta**\
   La solicitud contenía una cadena de consulta. El despachante asume que la salida depende de la cadena de consulta proporcionada y, por lo tanto, no la almacena en caché.
* **not cacheable: el administrador de sesión no se autenticó**\
   The farm&#39;s cache is governed by a session manager (the configuration contains a `sessionmanagement` node) and the request didn&#39;t contain the appropriate authentication information.
* **not cacheable: solicitud contiene autorización**\
   The farm is not allowed to cache output ( `allowAuthorized 0`) and the request contains authentication information.
* **not cacheable: target es un directorio**\
   El archivo de destino es un directorio. This might point to some conceptual mistake, where a URL and some sub-URL both contain cacheable output, for example if a request to `/test.html/a/file.ext` comes first and contains cacheable output, the dispatcher will not be able to cache the output of a subsequent request to `/test.html`.
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
