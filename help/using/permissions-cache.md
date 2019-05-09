---
title: Almacenamiento en caché de contenido protegido
seo-title: Almacenamiento en caché de contenido seguro en AEM Dispatcher
description: Descubra cómo funciona el almacenamiento en caché sensible a los permisos en Dispatcher.
seo-description: Descubra cómo funciona el almacenamiento en caché sensible a los permisos en AEM Dispatcher.
uuid: abfed 68 a -2 efe -45 f 6-bdf 7-2284931629 d 6
contentOwner: Usuario
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referencia
discoiquuid: 4 f 9 b 2 bc 8-a 309-47 bc-b 70 d-a 1 c 0 da 78 d 464
translation-type: tm+mt
source-git-commit: 8dd56f8b90331f0da43852e25893bc6f3e606a97

---


# Almacenamiento en caché de contenido protegido {#caching-secured-content}

El almacenamiento en caché sensible a los permisos permite almacenar en caché las páginas seguras. Dispatcher comprueba los permisos de acceso de los usuarios para una página antes de enviar la página en caché.

Dispatcher incluye el módulo authchecker que implementa almacenamiento en caché sensible a permisos. Cuando se activa el módulo, el procesamiento llama a un servlet AEM para realizar la autenticación de usuario y la autorización del contenido solicitado. La respuesta servlet determina si el contenido se entrega al explorador Web.

Dado que los métodos de autenticación y autorización son específicos de la implementación de AEM, es necesario crear el servlet.

>[!NOTE]
>
>Use `deny` filtros para aplicar restricciones de seguridad en blanco. Utilice el almacenamiento en caché sensible a los permisos para las páginas configuradas para permitir el acceso a un subconjunto de usuarios o grupos.

Los siguientes diagramas ilustran el orden de los eventos que se producen cuando un explorador Web solicita una página para la cual se utiliza el almacenamiento en caché sensible a los permisos.

## La página se almacena en caché y el usuario está autorizado {#page-is-cached-and-user-is-authorized}

![](assets/chlimage_1.png)

1. Dispatcher determina que el contenido solicitado se almacena en caché y es válido.
1. Dispatcher envía un mensaje de solicitud al procesamiento. La sección HEAD incluye todas las líneas de encabezado de la solicitud del navegador.
1. El procesamiento llama al autorizador para realizar la comprobación de seguridad y responde a Dispatcher. El mensaje de respuesta incluye un código de estado HTTP de 200 para indicar que el usuario está autorizado.
1. Dispatcher envía un mensaje de respuesta al explorador que consiste en líneas de encabezado de la respuesta de procesamiento y el contenido almacenado en caché en el cuerpo.

## La página no se almacena en caché y el usuario está autorizado {#page-is-not-cached-and-user-is-authorized}

![](assets/chlimage_1-1.png)

1. Dispatcher determina que el contenido no se almacena en caché o requiere actualización.
1. Dispatcher envía la solicitud original al procesamiento.
1. El procesamiento llama al servlet del autorizador para realizar una comprobación de seguridad. Cuando el usuario está autorizado, el procesamiento incluye la página representada en el cuerpo del mensaje de respuesta.
1. Dispatcher envía la respuesta al explorador. Dispatcher agrega el cuerpo del mensaje de respuesta del procesamiento a la caché.

## El usuario no está autorizado {#user-is-not-authorized}

![](assets/chlimage_1-2.png)

1. Dispatcher comprueba la caché.
1. Dispatcher envía un mensaje de solicitud al procesamiento que incluye todas las líneas de encabezado de la solicitud del navegador.
1. El procesamiento llama al servlet del autorizador para realizar una comprobación de seguridad que falla y el procesamiento envía la solicitud original a Dispatcher.

## Implementación de caché sensible a los permisos {#implementing-permission-sensitive-caching}

Para implementar el almacenamiento en caché sensible a los permisos, realice las tareas siguientes:

* Desarrollar un servlet que lleve a cabo la autenticación y autorización
* Configurar Dispatcher

>[!NOTE]
>
>Normalmente, los recursos seguros se almacenan en una carpeta distinta a los archivos no seguros. Por ejemplo, /content/secure/


## Creación del servlet de autorización {#create-the-authorization-servlet}

Cree e implemente un servlet que lleve a cabo la autenticación y autorización del usuario que solicita el contenido web. El servlet puede utilizar cualquier método de autenticación y autorización, como la cuenta de usuario y el repositorio de AEM, o un servicio de búsqueda LDAP. Implemente el servlet en la instancia de AEM que Dispatcher usa como representación.

El servlet debe ser accesible para todos los usuarios. Por lo tanto, el servlet debería ampliar `org.apache.sling.api.servlets.SlingSafeMethodsServlet` la clase, que proporciona acceso de solo lectura al sistema.

El servlet solo recibe solicitudes HEAD del procesamiento, por lo que solo es necesario implementar `doHead` el método.

El procesamiento incluye el URI del recurso solicitado como parámetro de la solicitud HTTP. Por ejemplo, se accede a un servlet de autorización mediante `/bin/permissioncheck`. Para realizar una comprobación de seguridad en la página /content/geometrixx-outdoors/en.html, el procesamiento incluye la siguiente URL en la solicitud HTTP:

`/bin/permissioncheck?uri=/content/geometrixx-outdoors/en.html`

El mensaje de respuesta servlet debe contener los siguientes códigos de estado HTTP:

* 200: Se ha superado la autenticación y la autorización.

El siguiente servlet de ejemplo obtiene la URL del recurso solicitado de la solicitud HTTP. El código utiliza la `Property` anotación Felix SCR para definir el valor de `sling.servlet.paths` la propiedad en /bin/permissioncheck. En `doHead` el método, el servlet obtiene el objeto session y utiliza `checkPermission` el método para determinar el código de respuesta adecuado.

>[!NOTE]
>
>El valor de la propiedad sling. servlet. paths debe estar habilitado en el servicio Sling Servlet Resolver (org. apache. sling. servlets. resolver. slingservletresolver).

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

## Configurar Dispatcher para almacenar en caché los permisos {#configure-dispatcher-for-permission-sensitive-caching}

La sección auth_ checker del despachante. any controla el comportamiento del almacenamiento en caché sensible a los permisos. La sección auth_ checker incluye las siguientes subsecciones:

* `url`: Valor de `sling.servlet.paths` la propiedad del servlet que realiza la comprobación de seguridad.

* `filter`: Filtros que especifican las carpetas en las que se aplica el almacenamiento en caché sensible a los permisos. Normalmente, se `deny` aplica un filtro a todas las carpetas y `allow` los filtros se aplican a las carpetas seguras.

* `headers`: Especifica los encabezados HTTP que incluye el servlet de autorización en la respuesta.

Cuando se inicia Dispatcher, el archivo de registro Dispatcher incluye el siguiente mensaje de nivel de depuración:

`AuthChecker: initialized with URL 'configured_url'.`

La siguiente sección auth_ checker configura Dispatcher para utilizar el servlet del tema prevoius. La sección de filtro hace que las comprobaciones de permisos se realicen únicamente en recursos HTML seguros.

### Configuración de ejemplo {#example-configuration}

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

