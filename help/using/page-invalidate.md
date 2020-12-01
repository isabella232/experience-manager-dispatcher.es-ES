---
title: Invalidación de páginas en la caché de AEM
seo-title: Invalidación de páginas en caché de AEM de Adobe
description: Obtenga información sobre cómo configurar la interacción entre Dispatcher y AEM para garantizar una administración eficaz de la caché.
seo-description: Obtenga información sobre cómo configurar la interacción entre Adobe AEM Dispatcher y AEM para garantizar una administración eficaz de la memoria caché.
uuid: 66533299-55c0-4864-9beb-77e281af9359
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 79cd94be-a6bc-4d34-bfe9-393b4107925c
translation-type: tm+mt
source-git-commit: 85497651ce29c8564da4b52c60819a48b776af7b
workflow-type: tm+mt
source-wordcount: '1427'
ht-degree: 0%

---


# Invalidación de páginas en la caché de AEM {#invalidating-cached-pages-from-aem}

Al utilizar Dispatcher con AEM, la interacción debe configurarse para garantizar una administración eficaz de la caché. Según el entorno, la configuración también puede aumentar el rendimiento.

## Configuración de cuentas de usuario AEM {#setting-up-aem-user-accounts}

La cuenta de usuario predeterminada `admin` se utiliza para autenticar los agentes de replicación instalados de forma predeterminada. Debe crear una cuenta de usuario dedicada para utilizarla con agentes de replicación.

Para obtener más información, consulte la sección [Configurar usuarios de replicación y transporte](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps) de la lista de comprobación de seguridad de AEM.

## Invalidación de la memoria caché de Dispatcher desde el Entorno de creación {#invalidating-dispatcher-cache-from-the-authoring-environment}

Un agente de replicación en la instancia de creación de AEM envía una solicitud de invalidación de caché a Dispatcher cuando se publica una página. La solicitud hace que Dispatcher actualice finalmente el archivo en la caché a medida que se publica contenido nuevo.

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

Siga el procedimiento siguiente para configurar un agente de replicación en la instancia de creación de AEM para invalidar la caché de Dispatcher tras la activación de página:

1. Abra la consola Herramientas de AEM. (`https://localhost:4502/miscadmin#/etc`)
1. Abra el agente de replicación requerido debajo de Herramientas/replicación/Agentes en el autor. Puede utilizar el agente Dispatcher Flush que está instalado de forma predeterminada.
1. Haga clic en Editar y, en la ficha Configuración, asegúrese de que **Habilitado** está seleccionado.

1. (opcional) Para habilitar solicitudes de invalidación de alias o de ruta de vanidad, seleccione la opción **Actualización de alias**.
1. En la ficha Transporte, introduzca el URI necesario para acceder a Dispatcher.\
   Si está utilizando el agente Dispatcher Flush estándar, probablemente necesite actualizar el nombre de host y el puerto; por ejemplo, https://&lt;*dispatcherHost*>:&lt;*portApache*>/dispatcher/invalidate.cache

   **Nota:** En el caso de los agentes de Dispatcher Flush, la propiedad URI solo se utiliza si se utilizan entradas de host virtual basadas en rutas para diferenciar entre granjas. Este campo se utiliza para el destinatario de la granja para invalidar. Por ejemplo, la granja #1 tiene un host virtual de `www.mysite.com/path1/*` y la granja #2 tiene un host virtual de `www.mysite.com/path2/*`. Puede utilizar una dirección URL de `/path1/invalidate.cache` para el destinatario del primer conjunto de servidores y `/path2/invalidate.cache` para el destinatario del segundo conjunto de servidores. Para obtener más información, consulte [Uso de Dispatcher con varios dominios](dispatcher-domains.md).

1. Configure otros parámetros según sea necesario.
1. Haga clic en Aceptar para activar el agente.

También puede acceder al agente Dispatcher Flush y configurarlo desde la [IU táctil AEM](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/replication.html#ConfiguringaDispatcherFlushagent).

Para obtener más información sobre cómo habilitar el acceso a las direcciones URL personales, consulte [Habilitación del acceso a direcciones URL de vanidad](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls).

>[!NOTE]
>
>El agente para vaciar la caché del despachante no tiene que tener un nombre de usuario y una contraseña, pero si se configura, se enviarán con autenticación básica.

Hay dos problemas potenciales con este enfoque:

* El despachante debe estar disponible desde la instancia de creación. Si la red (por ejemplo, el servidor de seguridad) está configurada de manera que el acceso entre los dos esté restringido, puede que no sea el caso.

* La invalidación de publicación y caché se produce al mismo tiempo. Según la temporización, un usuario puede solicitar una página inmediatamente después de eliminarla de la caché y justo antes de que se publique la nueva página. AEM ahora devuelve la página antigua y el despachante la vuelve a almacenar en caché. Esto es más un problema para los sitios grandes.

## Invalidación de la caché de despachantes desde una instancia de publicación {#invalidating-dispatcher-cache-from-a-publishing-instance}

En determinadas circunstancias, se pueden obtener mejoras en el rendimiento mediante la transferencia de la administración de la caché desde el entorno de creación a una instancia de publicación. A continuación, será el entorno de publicación (no el entorno de creación de AEM) el que envía una solicitud de invalidación de caché a Dispatcher cuando se recibe una página publicada.

Esas circunstancias incluyen:

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Evitar posibles conflictos de temporización entre Dispatcher y la instancia de publicación (consulte [Invalidación de la caché de Dispatcher desde el Entorno de creación](#invalidating-dispatcher-cache-from-the-authoring-environment)).
* El sistema incluye varias instancias de publicación que residen en servidores de alto rendimiento y solo una instancia de creación.

>[!NOTE]
>
>La decisión de utilizar este método debe tomarla un administrador AEM experimentado.

El vaciado del despachante está controlado por un agente de replicación que opera en la instancia de publicación. Sin embargo, la configuración se realiza en el entorno de creación y luego se transfiere mediante la activación del agente:

1. Abra la consola Herramientas de AEM.
1. Abra el agente de replicación requerido debajo de Herramientas/replicación/Agentes al publicar. Puede utilizar el agente Dispatcher Flush que está instalado de forma predeterminada.
1. Haga clic en Editar y, en la ficha Configuración, asegúrese de que **Habilitado** está seleccionado.
1. (opcional) Para habilitar solicitudes de invalidación de alias o de ruta de vanidad, seleccione la opción **Actualización de alias**.
1. En la ficha Transporte, introduzca el URI necesario para acceder a Dispatcher.\
   Si está utilizando el agente Dispatcher Flush estándar, probablemente necesite actualizar el nombre de host y el puerto; por ejemplo, `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **Nota:** En el caso de los agentes de Dispatcher Flush, la propiedad URI solo se utiliza si se utilizan entradas de host virtual basadas en rutas para diferenciar entre granjas. Este campo se utiliza para el destinatario de la granja para invalidar. Por ejemplo, la granja #1 tiene un host virtual de `www.mysite.com/path1/*` y la granja #2 tiene un host virtual de `www.mysite.com/path2/*`. Puede utilizar una dirección URL de `/path1/invalidate.cache` para el destinatario del primer conjunto de servidores y `/path2/invalidate.cache` para el destinatario del segundo conjunto de servidores. Para obtener más información, consulte [Uso de Dispatcher con varios dominios](dispatcher-domains.md).

1. Configure otros parámetros según sea necesario.
1. Repita el procedimiento para cada instancia de publicación afectada.

Después de configurar, cuando se activa una página desde el autor hasta la publicación, este agente inicia una replicación estándar. El registro incluye mensajes que indican solicitudes procedentes del servidor de publicación, de forma similar al siguiente ejemplo:

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Invalidación manual de la caché de despachantes {#manually-invalidating-the-dispatcher-cache}

Para invalidar (o vaciar) la caché de Dispatcher sin activar una página, puede enviar una solicitud HTTP al distribuidor. Por ejemplo, puede crear una aplicación AEM que permita a los administradores u otras aplicaciones vaciar la caché.

La solicitud HTTP hace que Dispatcher elimine archivos específicos de la caché. De forma opcional, Dispatcher actualiza la caché con una nueva copia.

### Eliminar archivos en caché {#delete-cached-files}

Ejecute una solicitud HTTP que haga que Dispatcher elimine archivos de la caché. Dispatcher vuelve a almacenar en caché los archivos solo cuando recibe una solicitud de cliente para la página. La eliminación de archivos en caché de este modo es adecuada para los sitios Web que no tienen probabilidad de recibir solicitudes simultáneas para la misma página.

La solicitud HTTP tiene el siguiente formulario:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher vacia (elimina) los archivos en caché y las carpetas que tienen nombres que coinciden con el valor del encabezado `CQ-Handler`. Por ejemplo, un `CQ-Handle` de `/content/geomtrixx-outdoors/en` coincide con los siguientes elementos:

* Todos los archivos (de cualquier extensión de archivo) con el nombre `en` en el directorio `geometrixx-outdoors`

* Cualquier directorio llamado &quot; `_jcr_content`&quot; debajo del directorio en (que, si existe, contiene representaciones en caché de subnodos de la página)

Todos los demás archivos de la caché del despachante (o hasta un nivel determinado, según la configuración `/statfileslevel`) se invalidan al tocar el archivo `.stat`. La fecha de la última modificación de este archivo se compara con la fecha de la última modificación de un documento en caché y el documento se recupera si el archivo `.stat` es más reciente. Consulte [Invalidación de archivos por nivel de carpeta](dispatcher-configuration.md#main-pars_title_26) para obtener más información.

La invalidación (es decir, tocar archivos .stat) se puede evitar enviando un encabezado adicional `CQ-Action-Scope: ResourceOnly`. Esto se puede utilizar para vaciar recursos específicos sin invalidar otras partes de la caché, como los datos JSON creados dinámicamente y que requieren vaciado regular independiente de la caché (por ejemplo, representar datos obtenidos de un sistema de terceros para mostrar noticias, tickets de acciones, etc.).

### Eliminar y recuperar archivos {#delete-and-recache-files}

Ejecute una solicitud HTTP que haga que Dispatcher elimine los archivos en caché y recupere y recupere inmediatamente el archivo. Elimine los archivos y vuelva a almacenarlos en caché inmediatamente cuando sea probable que los sitios web reciban solicitudes de cliente simultáneas para la misma página. El registro inmediato garantiza que Dispatcher recupera y almacena en caché la página una sola vez, en lugar de una sola vez para cada una de las solicitudes simultáneas del cliente.

**Nota:** La eliminación y el registro de archivos solo se deben realizar en la instancia de publicación. Cuando se realiza desde la instancia de autor, las condiciones de carrera se dan cuando se intenta recuperar los recursos antes de que se publiquen.

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

Las rutas de página para acceder inmediatamente se enumeran en líneas independientes en el cuerpo del mensaje. El valor de `CQ-Handle` es la ruta de una página que invalida las páginas que se van a recuperar. (Consulte el parámetro `/statfileslevel` del elemento de configuración [Cache](dispatcher-configuration.md#main-pars_146_44_0010)). El siguiente ejemplo de mensaje de solicitud HTTP elimina y llega a `/content/geometrixx-outdoors/en.html page`:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### Ejemplo de servlet de vaciado {#example-flush-servlet}

El siguiente código implementa un servlet que envía una solicitud no válida a Dispatcher. El servlet recibe un mensaje de solicitud que contiene parámetros `handle` y `page`. Estos parámetros proporcionan el valor del encabezado `CQ-Handle` y la ruta de la página para recuperar, respectivamente. El servlet utiliza los valores para construir la solicitud HTTP para Dispatcher.

Cuando se implementa el servlet en la instancia de publicación, la siguiente URL hace que Dispatcher elimine la página /content/geometrixx-outdoors/en.html y, a continuación, almacene en caché una nueva copia.

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>Este servlet de ejemplo no es seguro y solo muestra el uso del mensaje de solicitud HTTP Post. Su solución debe asegurar el acceso al servlet.


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

