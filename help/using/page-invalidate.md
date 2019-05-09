---
title: Ininvalidación de páginas caché desde AEM
seo-title: Inutilización de páginas caché de Adobe AEM
description: Aprenda a configurar la interacción entre Dispatcher y AEM para garantizar una administración eficaz de la caché.
seo-description: Aprenda a configurar la interacción entre Adobe AEM Dispatcher y AEM para garantizar una administración eficaz de la caché.
uuid: 66533299-55 c 0-4864-9 beb -77 e 281 af 9359
cmgrlastmodified: 01.11.2007 08 22 29 [ahedisoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: Usuario
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referencia
discoiquuid: 79 cd 94 be-a 6 bc -4 d 34-bfe 9-393 b 4107925 c
translation-type: tm+mt
source-git-commit: 76cffbfb616cd5601aed36b7076f67a2faf3ed3b

---


# Ininvalidación de páginas caché desde AEM {#invalidating-cached-pages-from-aem}

Al utilizar Dispatcher con AEM, la interacción debe estar configurada para garantizar una administración eficaz de la caché. Según el entorno, la configuración también puede aumentar el rendimiento.

## Configuración de cuentas de usuario de AEM {#setting-up-aem-user-accounts}

La cuenta `admin` de usuario predeterminada se utiliza para autenticar los agentes de replicación instalados de forma predeterminada. Debe crear una cuenta de usuario dedicada para utilizarla con agentes de replicación. [](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps)

Para obtener más información, consulte la sección [Configurar replicación y usuarios](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps) de transporte de la lista Comprobación de seguridad de AEM.

## Invalidating Dispatcher Cache from the Authoring Environment {#invalidating-dispatcher-cache-from-the-authoring-environment}

Un agente de replicación en la instancia de autor de AEM envía una solicitud de invalidación de caché a Dispatcher cuando se publica una página. La solicitud hace que Dispatcher actualice el archivo en la caché a medida que se publique contenido nuevo.

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-0436B4A35714BFF67F000101@AdobeID)
Last Modified Date: 2017-05-25T10:37:23.679-0400

<p>Hiding this information due to <a href="https://jira.corp.adobe.com/browse/CQDOC-5805">CQDOC-5805</a>.</p>

 -->

Utilice el procedimiento siguiente para configurar un agente de replicación en la instancia de autor de AEM para invalidar la caché despachante al activarla:

1. Abra la consola Herramientas de AEM. (`https://localhost:4502/miscadmin#/etc`)
1. Abra el agente de replicación requerido debajo de Herramientas/replicación/agentes en el autor. Puede utilizar el agente Despush Despachante que está instalado de forma predeterminada.
1. Haga clic en Editar y, en la ficha Configuración, asegúrese de que **Habilitado está** seleccionado.

1. (opcional) Para activar las solicitudes de invalidación de ruta de alias o alias seleccione la opción **de actualización** Alias.
1. En la ficha Transporte, introduzca el URI necesario para acceder a Dispatcher.\
   Si utiliza el agente de Vaciador Despachante estándar, probablemente necesite actualizar el nombre de host y el puerto; por ejemplo, https:// &lt;*dispatcherhost*&gt;: &lt;*Portapache*&gt;/dispatcher/invalidate. cache

   **Nota:** En el caso de agentes de vaciar despachante, la propiedad URI se utiliza únicamente si se utilizan entradas de virtualhost virtualhost basadas en rutas para diferenciar entre granjas. Utilice este campo para dirigir la granja a invalidar. Por ejemplo, granja # 1 tiene un host virtual de `www.mysite.com/path1/*` y granja # 2 tiene un host virtual de `www.mysite.com/path2/*`. Puede utilizar una URL para `/path1/invalidate.cache` dirigir la primera granja y `/path2/invalidate.cache` para dirigir la segunda granja. Para obtener más información, consulte [Uso de Dispatcher con varios dominios](dispatcher-domains.md).

1. Configure otros parámetros según sea necesario.
1. Haga clic en Aceptar para activar el agente.

También puede acceder y configurar el agente Despush Despachante desde la IU táctil [de AEM](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/replication.html#ConfiguringaDispatcherFlushagent).

Para obtener más información sobre cómo activar el acceso a URL personales, consulte [Activación de acceso a URL personales](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls).

>[!NOTE]
>
>El agente para vaciar la caché de Dispatcher no tiene que tener un nombre de usuario y una contraseña, pero si se ha configurado se enviarán con autenticación básica.

Este enfoque presenta dos posibles problemas:

* El despachante debe ser accesible desde la instancia de creación. Si la red (p. ej. el servidor de seguridad) está configurada de manera que el acceso entre ambos está restringido, puede que no sea éste el caso.

* La publicación y la invalidación de caché se realizan al mismo tiempo. Según la hora, un usuario puede solicitar una página justo después de eliminarla de la caché, y justo antes de publicar la nueva página. AEM ahora devuelve la página antigua y Dispatcher vuelve a almacenarla en caché. Esto es más un problema para sitios grandes.

## Invalidating Dispatcher Cache from a Publishing Instance {#invalidating-dispatcher-cache-from-a-publishing-instance}

En determinadas circunstancias, las mejoras de rendimiento se pueden realizar transfiriendo administración de caché desde el entorno de creación a una instancia de publicación. Será el entorno de publicación (no el entorno de creación AEM) que envía una solicitud de invalidación de caché a Dispatcher cuando se reciba una página publicada.

Estas circunstancias incluyen:

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Evitar posibles conflictos de temporización entre Dispatcher y la instancia de publicación (consulte [Invalidating Dispatcher Cache desde el entorno de creación](#invalidating-dispatcher-cache-from-the-authoring-environment)).
* El sistema incluye varias instancias de publicación que residen en servidores de alto rendimiento y una única instancia de creación.

>[!NOTE]
>
>La decisión de utilizar este método debe ser llevada a cabo por un administrador experimentado de AEM.

El despachante despachante está controlado por un agente de replicación que opera en la instancia de publicación. Sin embargo, la configuración se realiza en el entorno de creación y, a continuación, se transfiere activando el agente:

1. Abra la consola Herramientas de AEM.
1. Abra el agente de replicación requerido debajo de Herramientas/replicación/agentes durante la publicación. Puede utilizar el agente Despush Despachante que está instalado de forma predeterminada.
1. Haga clic en Editar y, en la ficha Configuración, asegúrese de que **Habilitado está** seleccionado.
1. (opcional) Para activar las solicitudes de invalidación de ruta de alias o alias seleccione la opción **de actualización** Alias.
1. En la ficha Transporte, introduzca el URI necesario para acceder a Dispatcher.\
   Si utiliza el agente de Vaciador Despachante estándar, probablemente necesite actualizar el nombre de host y el puerto; por ejemplo, `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **Nota:** En el caso de agentes de vaciar despachante, la propiedad URI se utiliza únicamente si se utilizan entradas de virtualhost virtualhost basadas en rutas para diferenciar entre granjas. Utilice este campo para dirigir la granja a invalidar. Por ejemplo, granja # 1 tiene un host virtual de `www.mysite.com/path1/*` y granja # 2 tiene un host virtual de `www.mysite.com/path2/*`. Puede utilizar una URL para `/path1/invalidate.cache` dirigir la primera granja y `/path2/invalidate.cache` para dirigir la segunda granja. Para obtener más información, consulte [Uso de Dispatcher con varios dominios](dispatcher-domains.md).

1. Configure otros parámetros según sea necesario.
1. Repita la operación para cada instancia de publicación afectada.

Después de configurar una página del autor que se va a publicar, este agente inicia una replicación estándar. El registro incluye mensajes que indican solicitudes procedentes del servidor de publicación, similar al siguiente ejemplo:

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Invalidar manualmente la caché de despachante {#manually-invalidating-the-dispatcher-cache}

Para invalidar (o vaciar) la caché de Dispatcher sin activar una página, puede enviar una solicitud HTTP al despachante. Por ejemplo, puede crear una aplicación AEM que permita a los administradores u otras aplicaciones vaciar la caché.

La solicitud HTTP hace que Dispatcher elimine archivos específicos de la caché. De forma opcional, Dispatcher actualiza la caché con una copia nueva.

### Eliminar archivos en caché {#delete-cached-files}

Emite una solicitud HTTP que hace que Dispatcher elimine archivos de la caché. Dispatcher almacena en caché los archivos de nuevo solo cuando recibe una solicitud de cliente de la página. La eliminación de los archivos almacenados en caché es adecuada para los sitios Web que no tengan probabilidad de recibir solicitudes simultáneas para la misma página.

La solicitud HTTP tiene el siguiente formulario:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher flusca (elimina) los archivos en caché y las carpetas que tienen nombres que coinciden con el valor del `CQ-Handler` encabezado. Por ejemplo, una `CQ-Handle` de `/content/geomtrixx-outdoors/en` las coincidencias de los siguientes elementos:

* Todos los archivos (de cualquier extensión de archivo) nombrados `en` en `geometrixx-outdoors` el directorio

* Cualquier directorio llamado &quot; `_jcr_content`&quot; debajo del directorio en (que, si existe, contiene representaciones en caché de subnodos de la página)

Todos los demás archivos de la caché de Dispatcher (o hasta un nivel concreto, según `/statfileslevel` la configuración) se invalidarán al tocar el `.stat` archivo. La última fecha de modificación de este archivo se compara con la fecha de la última modificación de un documento en caché y se vuelve a recuperar si `.stat` el archivo es más nuevo. Consulte [Invalidating Files by Folder Level](dispatcher-configuration.md#main-pars_title_26) para obtener más información.

La invalidación (es decir, el toques de archivos. stat) se puede evitar enviando un encabezado `CQ-Action-Scope: ResourceOnly`adicional. Esto se puede utilizar para vaciar recursos concretos sin invalidar otras partes de la caché, como los datos JSON que se crean de forma dinámica y requiere vaciar el contenido independiente del caché (por ejemplo, representar datos que se obtienen de un sistema externo para mostrar noticias, lectores de bolsa, etc.).

### Eliminar y recargar archivos {#delete-and-recache-files}

Emite una solicitud HTTP que hace que Dispatcher elimine archivos almacenados en caché e inmediatamente recupere y recargue el archivo. Elimine e inmediatamente vuelva a almacenar archivos cuando los sitios Web deban recibir solicitudes de cliente simultáneas para la misma página. El rectángulo inmediato garantiza que Dispatcher recupera y almacena en caché la página una sola vez, en vez de una sola para cada solicitud simultánea del cliente.

**Nota:** Solo se deben eliminar y volver a guardar los archivos en la instancia de publicación. Cuando se realiza desde la instancia de autor, se producen condiciones de carrera cuando se producen intentos de recache antes de publicarse.

La solicitud HTTP tiene el siguiente formulario:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate   
`Content-Type: text/plain  
CQ-Handle: path-pattern  
Content-Length: numchars in bodypage_path0

page_path1 
...  
page_pathn
```

Las rutas de página para el recache inmediatamente se enumeran en líneas separadas del cuerpo del mensaje. El valor de `CQ-Handle` es la ruta de una página que invalida las páginas a recache. (Consulte `/statfileslevel` el parámetro del [elemento](dispatcher-configuration.md#main-pars_146_44_0010) de configuración de caché). El siguiente mensaje de solicitud HTTP elimina y recargue lo `/content/geometrixx-outdoors/en.html page`siguiente:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### Ejemplo de servlet vaciado {#example-flush-servlet}

El siguiente código implementa un servlet que envía una solicitud de invalidación a Dispatcher. El servlet recibe un mensaje de solicitud que contiene `handle` parámetros y `page` parámetros. Estos parámetros proporcionan el valor del `CQ-Handle` encabezado y la ruta de la página para recache, respectivamente. El servlet utiliza los valores para construir la solicitud HTTP para Dispatcher.

Cuando el servlet se implementa en la instancia de publicación, la siguiente URL hace que Dispatcher elimine la página /content/geometrixx-outdoors/en.html y luego almacene en caché una nueva copia.

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>Este servlet de ejemplo no es seguro y solo muestra el uso del mensaje de solicitud HTTP Post. Su solución debe proteger el acceso al servlet.


```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.api.SlingHttpServletResponse;
import org.apache.sling.api.servlets.SlingSafeMethodsServlet;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.apache.commons.httpclient.*;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;

@Component(metatype=true)
@Service
public class Flushcache extends SlingSafeMethodsServlet {

 @Property(value="/bin/flushcache")
 static final String SERVLET_PATH="sling.servlet.paths";

 private Logger logger = LoggerFactory.getLogger(this.getClass());

 public void doGet(SlingHttpServletRequest request, SlingHttpServletResponse response) {
  try{ 
      //retrieve the request parameters
      String handle = request.getParameter("handle");
      String page = request.getParameter("page");

      //hard-coding connection properties is a bad practice, but is done here to simplify the example
      String server = "localhost"; 
      String uri = "/dispatcher/invalidate.cache";

      HttpClient client = new HttpClient();

      PostMethod post = new PostMethod("https://"+host+uri);
      post.setRequestHeader("CQ-Action", "Activate");
      post.setRequestHeader("CQ-Handle",handle);
   
      StringRequestEntity body = new StringRequestEntity(page,null,null);
      post.setRequestEntity(body);
      post.setRequestHeader("Content-length", String.valueOf(body.getContentLength()));
      client.executeMethod(post);
      post.releaseConnection();
      //log the results
      logger.info("result: " + post.getResponseBodyAsString());
      }
  }catch(Exception e){
      logger.error("Flushcache servlet exception: " + e.getMessage());
  }
 }
}
```

