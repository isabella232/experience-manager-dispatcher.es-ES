---
title: Invalidar páginas en la caché de AEM
seo-title: Invalidating Cached Pages From Adobe AEM
description: Aprenda a configurar la interacción entre Dispatcher y AEM para garantizar una administración eficaz de la caché.
seo-description: Learn how to configure the interaction between Adobe AEM Dispatcher and AEM to ensure effective cache management.
uuid: 66533299-55c0-4864-9beb-77e281af9359
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 79cd94be-a6bc-4d34-bfe9-393b4107925c
exl-id: 90eb6a78-e867-456d-b1cf-f62f49c91851
source-git-commit: f447ff9b3785248a4906c1c9abdcbd18576aa36d
workflow-type: tm+mt
source-wordcount: '1404'
ht-degree: 100%

---

# Invalidar páginas en la caché de AEM {#invalidating-cached-pages-from-aem}

Al utilizar Dispatcher con AEM, la interacción debe configurarse para garantizar una administración eficaz de la caché. Según el entorno, la configuración también podría aumentar el rendimiento.

## Configurar cuentas de usuario de AEM {#setting-up-aem-user-accounts}

La cuenta de usuario predeterminada `admin` se utiliza para autenticar los agentes de replicación instalados de forma predeterminada. Debe crear una cuenta de usuario dedicada para utilizarla con agentes de replicación.

Para obtener más información, consulte la sección [Configurar usuarios de replicación y transporte](https://helpx.adobe.com/es/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps) de la lista de comprobación de seguridad de AEM.

## Invalidar la caché de Dispatcher desde el entorno de creación {#invalidating-dispatcher-cache-from-the-authoring-environment}

Un agente de replicación en la instancia de autor de AEM envía una solicitud de invalidación de la caché a Dispatcher cuando se publica una página. La solicitud hace que Dispatcher actualice finalmente el archivo en la caché a medida que se publique el contenido nuevo.

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

Utilice el siguiente procedimiento para configurar un agente de replicación en la instancia de autor de AEM para invalidar la caché de Dispatcher al activar la página:

1. Abra la consola Herramientas de AEM. (`https://localhost:4502/miscadmin#/etc`)
1. Abra el agente de replicación necesario debajo de Herramientas/replicación/agentes en autor. Puede utilizar el agente de vaciado de Dispatcher instalado de forma predeterminada.
1. Haga clic en Editar y, en la pestaña Configuración, asegúrese de que esté seleccionado **Habilitado**.

1. (opcional) Para habilitar las solicitudes de invalidación de alias o de rutas de vanidad, seleccione la opción **Actualización de alias**.
1. En la pestaña Transporte, introduzca el URI necesario para acceder a Dispatcher.\
   Si está utilizando un agente de vaciado de Dispatcher estándar, probablemente necesite actualizar el nombre de host y el puerto; por ejemplo, https://&lt;*dispatcherHost*>:&lt;*portApache*>/dispatcher/invalidate.cache

   **Nota:** Para los agentes de vaciado de Dispatcher, la propiedad URI solo se utiliza si utiliza entradas de host virtual basadas en rutas para diferenciar entre granjas. Utilice este campo para dirigirse a la granja para invalidarla. Por ejemplo, la granja n.º 1 tiene un host virtual de `www.mysite.com/path1/*` y la granja n.º 2 lo tiene de `www.mysite.com/path2/*`. Puede utilizar una URL de `/path1/invalidate.cache` para dirigirse a la primera granja de servidores y `/path2/invalidate.cache` para dirigirse a la segunda. Para obtener más información, consulte [Utilizar Dispatcher con varios dominios](dispatcher-domains.md).

1. Configure otros parámetros según sea necesario.
1. Haga clic en Aceptar para activar el agente.

También puede acceder y configurar el agente de vaciado de Dispatcher desde la [interfaz de usuario táctil de AEM](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/configuring/replication.html?lang=es#configuring-a-dispatcher-flush-agent).

Para obtener más información sobre cómo habilitar el acceso a las URL personales, consulte [Habilitar el acceso a las URL de vanidad](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls).

>[!NOTE]
>
>El agente para vaciar la caché de Dispatcher no tiene que tener un nombre de usuario y una contraseña, pero si se configura, se enviarán con una autenticación básica.

Hay dos problemas potenciales con este enfoque:

* Se debe poder acceder a Dispatcher desde la instancia de creación. Si la red (por ejemplo, el cortafuegos) está configurada de tal manera que el acceso entre los dos está restringido, puede que no sea el caso.

* La publicación y la invalidación de la caché se realizan al mismo tiempo. Dependiendo del momento, un usuario puede solicitar una página justo después de haberla eliminado de la caché y justo antes de que se publique la nueva página. AEM devolverá la página antigua y Dispatcher la almacenará en la caché de nuevo. Esto es un problema más bien para los sitios grandes.

## Invalidar la caché de Dispatcher desde una instancia de publicación {#invalidating-dispatcher-cache-from-a-publishing-instance}

En determinadas circunstancias, se pueden conseguir mejoras en el rendimiento transfiriendo la administración de la caché del entorno de creación a una instancia de publicación. A continuación, será el entorno de publicación (no el entorno de creación de AEM) el que enviará una solicitud de invalidación de la caché a Dispatcher cuando se reciba una página publicada.

Estas circunstancias incluyen:

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Prevenir posibles conflictos de sincronización entre Dispatcher y la instancia de publicación (consulte [Invalidación de la caché de Dispatcher desde el entorno de creación](#invalidating-dispatcher-cache-from-the-authoring-environment)).
* El sistema incluye varias instancias de publicación que residen en servidores de alto rendimiento y solo una instancia de creación.

>[!NOTE]
>
>La decisión de utilizar este método debe tomarla un administrador AEM experimentado.

La descarga de Dispatcher está controlada por un agente de replicación que opera en la instancia de publicación. Sin embargo, la configuración se realiza en el entorno de creación y luego se transfiere activando el agente:

1. Abra la consola Herramientas de AEM.
1. Abra el agente de replicación necesario debajo de Herramientas/replicación/agentes en publicación. Puede utilizar el agente de vaciado de Dispatcher instalado de forma predeterminada.
1. Haga clic en Editar y, en la pestaña Configuración, asegúrese de que esté seleccionado **Habilitado**.
1. (opcional) Para habilitar las solicitudes de invalidación de alias o de rutas de vanidad, seleccione la opción **Actualización de alias**.
1. En la pestaña Transporte, introduzca el URI necesario para acceder a Dispatcher.\
   Si está utilizando un agente de vaciado de Dispatcher estándar, probablemente necesite actualizar el nombre de host y el puerto; por ejemplo, `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **Nota:** Para los agentes de vaciado de Dispatcher, la propiedad URI solo se utiliza si utiliza entradas de host virtual basadas en rutas para diferenciar entre granjas. Utilice este campo para dirigirse a la granja para invalidarla. Por ejemplo, la granja n.º 1 tiene un host virtual de `www.mysite.com/path1/*` y la granja n.º 2 lo tiene de `www.mysite.com/path2/*`. Puede utilizar una URL de `/path1/invalidate.cache` para dirigirse a la primera granja de servidores y `/path2/invalidate.cache` para dirigirse a la segunda. Para obtener más información, consulte [Utilizar Dispatcher con varios dominios](dispatcher-domains.md).

1. Configure otros parámetros según sea necesario.
1. Repita el proceso para cada instancia de publicación afectada.

Después de configurar, cuando active una página de autor a publicación, este agente iniciará una replicación estándar. El registro incluye mensajes que indican solicitudes procedentes del servidor de publicación, de forma similar al siguiente ejemplo:

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Invalidar manualmente la caché de Dispatcher {#manually-invalidating-the-dispatcher-cache}

Para invalidar (o vaciar) la caché de Dispatcher sin activar una página, puede enviar una solicitud HTTP a Dispatcher. Por ejemplo, puede crear una aplicación AEM que permita a los administradores u otras aplicaciones vaciar la caché.

La solicitud HTTP hace que Dispatcher elimine archivos específicos de la caché. De forma opcional, Dispatcher actualiza la caché con una copia nueva.

### Eliminar archivos en caché {#delete-cached-files}

Ejecute una solicitud HTTP que haga que Dispatcher elimine archivos de la caché. Dispatcher volverá a almacenar en caché los archivos solo cuando reciba una solicitud de cliente para la página. La eliminación de archivos en caché de esta manera es idónea para sitios web que no reciban solicitudes simultáneas para la misma página.

La solicitud HTTP tiene el siguiente formulario:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher descarga (elimina) los archivos en caché y las carpetas que tienen nombres que coinciden con el valor del encabezado `CQ-Handler`. Por ejemplo, un `CQ-Handle` de `/content/geomtrixx-outdoors/en` coincide con los siguientes elementos:

* Todos los archivos (de cualquier extensión) llamados `en` en el directorio `geometrixx-outdoors`

* Cualquier directorio llamado &quot;`_jcr_content`&quot; debajo del directorio en (que, si existe, contiene procesamientos en caché de subnodos de la página)

Todos los demás archivos de la caché de Dispatcher (o hasta un nivel en particular, dependiendo de la configuración `/statfileslevel`) se invalidarán tocando el archivo `.stat`. La fecha de la última modificación de este archivo se comparará con la fecha de la última modificación de un documento almacenado en caché y el documento se recuperará si el archivo `.stat` es más reciente. Consulte [Invalidación de archivos por nivel de carpeta](dispatcher-configuration.md#main-pars_title_26) para obtener más información.

La invalidación (es decir, el contacto de archivos .stat) se puede evitar enviando un encabezado adicional `CQ-Action-Scope: ResourceOnly`. Esto se puede utilizar para vaciar recursos concretos sin invalidar otras partes de la caché, como los datos JSON creados dinámicamente y que requieren vaciado regular independientemente de la caché (por ejemplo, representar datos obtenidos de un sistema de terceros para mostrar noticias, lectores de stock, etc.).

### Eliminar archivos y volver a almacenarlos en la caché {#delete-and-recache-files}

Ejecute una solicitud HTTP que haga que Dispatcher elimine los archivos en caché y que recupere y vuelva a almacenar inmediatamente el archivo. Elimine y vuelva a almacenar en caché inmediatamente los archivos cuando los sitios web reciban solicitudes simultáneas del cliente para la misma página. El almacenamiento inmediato garantiza que Dispatcher recupere y almacene en caché la página solo una vez, en lugar de una vez por cada una de las solicitudes simultáneas del cliente.

**Nota:** Eliminar y volver a almacenar archivos en la caché solo debe realizarse en la instancia de publicación. Cuando se realiza desde la instancia de autor, las condiciones de carrera se producen cuando se intenta recuperar los recursos antes de publicarlos.

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

Las rutas de página para volver a almacenar archivos en la caché inmediatamente se enumeran en líneas independientes en el cuerpo del mensaje. El valor de `CQ-Handle` es la ruta de una página que invalida las páginas que se van a almacenar de nuevo. (Consulte el parámetro `/statfileslevel` del elemento de configuración [Caché](dispatcher-configuration.md#main-pars_146_44_0010)). El siguiente ejemplo de mensaje de solicitud HTTP elimina y vuelve a almacenar en la caché el `/content/geometrixx-outdoors/en.html page`:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### Ejemplo de vaciado del servlet {#example-flush-servlet}

El siguiente código implementa un servlet que envía una solicitud de invalidación a Dispatcher. El servlet recibe un mensaje de solicitud que contiene parámetros `handle` y `page`. Estos parámetros proporcionan el valor del encabezado `CQ-Handle` y la ruta de acceso de la página a recuperar, respectivamente. El servlet utiliza los valores para construir la solicitud HTTP para Dispatcher.

Cuando se implementa el servlet en la instancia de publicación, la siguiente URL hace que Dispatcher elimine la página /content/geometrixx-outdoors/en.html y, a continuación, almacene en caché una copia nueva.

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
