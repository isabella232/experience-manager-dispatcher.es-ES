---
title: Almacenamiento en caché de contenido seguro
seo-title: Almacenamiento en caché de contenido seguro en AEM Dispatcher
description: Descubra cómo funciona el almacenamiento en caché con distinción de permisos en Dispatcher.
seo-description: Descubra cómo funciona el almacenamiento en caché con distinción de permisos en AEM Dispatcher.
uuid: abfeed68a-2efe-45f6-bdf7-2284931629d6
contentOwner: Usuario
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referencia
discoiquuid: 4f9b2bc8-a309-47bc-b70d-a1c0da78d464
translation-type: tm+mt
source-git-commit: 8dd56f8b90331f0da43852e25893bc6f3e606a97

---


# Almacenamiento en caché de contenido seguro {#caching-secured-content}

El almacenamiento en caché con distinción de permisos permite almacenar en caché las páginas seguras. Dispatcher comprueba los permisos de acceso del usuario para una página antes de entregar la página en caché.

Dispatcher incluye el módulo AuthChecker que implementa almacenamiento en caché que distingue permisos. Cuando se activa el módulo, el procesamiento llama a un servlet de AEM para realizar la autenticación del usuario y la autorización del contenido solicitado. La respuesta del servlet determina si el contenido se envía al explorador Web.

Dado que los métodos de autenticación y autorización son específicos de la implementación de AEM, es necesario crear el servlet.

>[!NOTE]
>
>Utilice `deny` filtros para aplicar restricciones de seguridad generales. Utilice el almacenamiento en caché con distinción de permisos para páginas configuradas para permitir el acceso a un subconjunto de usuarios o grupos.

Los siguientes diagramas ilustran el orden de los eventos que se producen cuando un explorador Web solicita una página para la que se utiliza el almacenamiento en caché que distingue permisos.

## La página se almacena en caché y el usuario está autorizado {#page-is-cached-and-user-is-authorized}

![](assets/chlimage_1.png)

1. Dispatcher determina que el contenido solicitado se almacena en caché y es válido.
1. Dispatcher envía un mensaje de solicitud al procesamiento. La sección HEAD incluye todas las líneas de encabezado de la solicitud del explorador.
1. El procesamiento llama al autorizador para realizar la comprobación de seguridad y responde a Dispatcher. El mensaje de respuesta incluye un código de estado HTTP de 200 para indicar que el usuario está autorizado.
1. Dispatcher envía un mensaje de respuesta al navegador que consta de las líneas de encabezado de la respuesta de procesamiento y del contenido almacenado en caché del cuerpo.

## La página no se almacena en caché y el usuario está autorizado {#page-is-not-cached-and-user-is-authorized}

![](assets/chlimage_1-1.png)

1. Dispatcher determina que el contenido no se almacena en caché o que requiere actualización.
1. Dispatcher reenvía la solicitud original al procesamiento.
1. El procesamiento llama al servlet del autorizador para realizar una comprobación de seguridad. Cuando el usuario está autorizado, el procesamiento incluye la página representada en el cuerpo del mensaje de respuesta.
1. Dispatcher reenvía la respuesta al explorador. Dispatcher agrega el cuerpo del mensaje de respuesta del procesamiento a la caché.

## El usuario no está autorizado {#user-is-not-authorized}

![](assets/chlimage_1-2.png)

1. Dispatcher comprueba la caché.
1. Dispatcher envía un mensaje de solicitud al procesamiento que incluye todas las líneas de encabezado de la solicitud del explorador.
1. El procesamiento llama al servlet del autorizador para realizar una comprobación de seguridad que falla y el procesamiento reenvía la solicitud original al despachante.

## Implementación de almacenamiento en caché que distingue permisos {#implementing-permission-sensitive-caching}

Para implementar el almacenamiento en caché que distingue permisos, realice las siguientes tareas:

* Desarrollar un servlet que realice autenticación y autorización
* Configuración del despachante

>[!NOTE]
>
>Normalmente, los recursos seguros se almacenan en una carpeta independiente que los archivos no seguros. Por ejemplo, /content/secure/


## Creación del servlet de autorización {#create-the-authorization-servlet}

Cree e implemente un servlet que realice la autenticación y autorización del usuario que solicita el contenido web. El servlet puede utilizar cualquier método de autenticación y autorización, como la cuenta de usuario de AEM y las ACL del repositorio, o un servicio de búsqueda LDAP. El servlet se implementa en la instancia de AEM que Dispatcher utiliza como procesamiento.

Todos los usuarios deben tener acceso al servlet. Por lo tanto, el servlet debe extender la `org.apache.sling.api.servlets.SlingSafeMethodsServlet` clase, que proporciona acceso de sólo lectura al sistema.

El servlet sólo recibe solicitudes HEAD del procesamiento, por lo que solo necesita implementar el `doHead` método.

El procesamiento incluye el URI del recurso solicitado como parámetro de la solicitud HTTP. Por ejemplo, se accede a un servlet de autorización mediante `/bin/permissioncheck`. Para realizar una comprobación de seguridad en la página /content/geometrixx-outdoors/en.html, el procesamiento incluye la siguiente URL en la solicitud HTTP:

`/bin/permissioncheck?uri=/content/geometrixx-outdoors/en.html`

El mensaje de respuesta servlet debe contener los siguientes códigos de estado HTTP:

* 200: Autenticación y autorización pasadas.

El siguiente servlet de ejemplo obtiene la dirección URL del recurso solicitado de la solicitud HTTP. El código utiliza la anotación Félix SCR `Property` para establecer el valor de la `sling.servlet.paths` propiedad en /bin/permission check. En el `doHead` método, el servlet obtiene el objeto session y utiliza el `checkPermission` método para determinar el código de respuesta adecuado.

>[!NOTE]
>
>El valor de la propiedad sling.servlet.paths debe habilitarse en el servicio Sling Servlet Resolver (org.apache.sling.servlets.resolver.SlingServletResolver).

### Servlet de ejemplo {#example-servlet}

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

import javax.jcr.Session;

@Component(metatype=false)
@Service
public class AuthcheckerServlet extends SlingSafeMethodsServlet {
 
    @Property(value="/bin/permissioncheck")
    static final String SERVLET_PATH="sling.servlet.paths";
    
    private Logger logger = LoggerFactory.getLogger(this.getClass());
    
    public void doHead(SlingHttpServletRequest request, SlingHttpServletResponse response) {
     try{ 
      //retrieve the requested URL
      String uri = request.getParameter("uri");
      //obtain the session from the request
      Session session = request.getResourceResolver().adaptTo(javax.jcr.Session.class);     
      //perform the permissions check
      try {
       session.checkPermission(uri, Session.ACTION_READ);
       logger.info("authchecker says OK");
       response.setStatus(SlingHttpServletResponse.SC_OK);
      } catch(Exception e) {
       logger.info("authchecker says READ access DENIED!");
       response.setStatus(SlingHttpServletResponse.SC_FORBIDDEN);
      }
     }catch(Exception e){
      logger.error("authchecker servlet exception: " + e.getMessage());
     }
    }
}
```

## Configuración de Dispatcher para almacenamiento en caché con distinción de permisos {#configure-dispatcher-for-permission-sensitive-caching}

La sección auth_checker del archivo dispatcher.any controla el comportamiento del almacenamiento en caché que distingue permisos. La sección auth_checker incluye las siguientes subsecciones:

* `url`:: El valor de la `sling.servlet.paths` propiedad del servlet que realiza la comprobación de seguridad.

* `filter`:: Filtros que especifican las carpetas a las que se aplica la caché que distingue permisos. Normalmente, se aplica un `deny` filtro a todas las carpetas y `allow` los filtros a las carpetas seguras.

* `headers`:: Especifica los encabezados HTTP que incluye el servlet de autorización en la respuesta.

Cuando se inicia Dispatcher, el archivo de registro de Dispatcher incluye el siguiente mensaje de nivel de depuración:

`AuthChecker: initialized with URL 'configured_url'.`

En el siguiente ejemplo, la sección auth_checker configura Dispatcher para que utilice el servlet del tema anterior. La sección de filtros hace que las comprobaciones de permisos se realicen únicamente en recursos HTML seguros.

### Ejemplo de configuración {#example-configuration}

```xml
/auth_checker
  {
  # request is sent to this URL with '?uri=<page>' appended
  /url "/bin/permissioncheck"
      
  # only the requested pages matching the filter section below are checked,
  # all other pages get delivered unchecked
  /filter
    {
    /0000
      {
      /glob "*"
      /type "deny"
      }
    /0001
      {
      /glob "/content/secure/*.html"
      /type "allow"
      }
    }
  # any header line returned from the auth_checker's HEAD request matching
  # the section below will be returned as well
  /headers
    {
    /0000
      {
      /glob "*"
      /type "deny"
      }
    /0001
      {
      /glob "Set-Cookie:*"
      /type "allow"
      }
    }
  }
```

