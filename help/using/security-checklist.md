---
title: Lista de comprobación de seguridad de Dispatcher
seo-title: Lista de comprobación de seguridad de Dispatcher
description: Una lista de comprobación de seguridad que debe completarse antes de continuar con la producción.
seo-description: Una lista de comprobación de seguridad que debe completarse antes de continuar con la producción.
uuid: 7bfa3202-03f6-48e9-8d2e-2a40e137ecbe
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: fbfafa55-c029-4ed7-ab3e-1bebfde18248
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: 7889c025fb8fb29e6f11ea01c5248470556d3160
workflow-type: tm+mt
source-wordcount: '653'
ht-degree: 1%

---


# Lista de comprobación de seguridad de Dispatcher{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Adobe recomienda encarecidamente que complete la siguiente lista de comprobación antes de continuar con la producción.

>[!CAUTION]
>
>También debe completar la lista de comprobación de seguridad de su versión de AEM antes de activarse. Consulte la [documentación correspondiente de Adobe Experience Manager](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html).

## Usar la versión más reciente de Dispatcher {#use-the-latest-version-of-dispatcher}

Debe instalar la versión más reciente disponible para su plataforma. Debe actualizar la instancia de Dispatcher para utilizar la versión más reciente y aprovechar las mejoras de producto y seguridad. Consulte [Instalación de Dispatcher](dispatcher-install.md).

>[!NOTE]
>
>Puede comprobar la versión actual de la instalación del despachante consultando el archivo de registro del despachante.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Para encontrar el archivo de registro, inspeccione la configuración del despachante en su `httpd.conf`.

## Restringir clientes que pueden vaciar su caché {#restrict-clients-that-can-flush-your-cache}

Adobe recomienda que [limite los clientes que pueden vaciar la caché.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## Habilitar HTTPS para la seguridad de capa de transporte {#enable-https-for-transport-layer-security}

Adobe recomienda activar la capa de transporte HTTPS en las instancias de creación y publicación.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## Restringir acceso {#restrict-access}

Al configurar Dispatcher, debe restringir el acceso externo tanto como sea posible. Consulte [Ejemplo de sección /filter](dispatcher-configuration.md#main-pars_184_1_title) en la documentación de Dispatcher.

## Asegúrese de denegar el acceso a direcciones URL administrativas {#make-sure-access-to-administrative-urls-is-denied}

Asegúrese de utilizar filtros para bloquear el acceso externo a cualquier dirección URL administrativa, como la consola web.

Consulte [Prueba de seguridad del despachante](dispatcher-configuration.md#testing-dispatcher-security) para obtener una lista de las direcciones URL que deben bloquearse.

## Usar Listas de permitidos en lugar de Listas de bloqueados {#use-allowlists-instead-of-blocklists}

Las listas de permitidos son una mejor manera de proporcionar controles de acceso ya que, de manera inherente, suponen que todas las solicitudes de acceso deben denegarse a menos que formen parte explícita de la lista de permitidos. Este modelo proporciona un control más restrictivo sobre las nuevas solicitudes que podrían no haberse revisado aún o no haberse tenido en cuenta durante una determinada etapa de configuración.

## Ejecutar Dispatcher con un usuario del sistema dedicado {#run-dispatcher-with-a-dedicated-system-user}

Al configurar Dispatcher, debe asegurarse de que el servidor web lo ejecute un usuario dedicado con menos privilegios. Se recomienda otorgar acceso de escritura únicamente a la carpeta de caché del despachante.

Además, los usuarios de IIS deben configurar su sitio web de la siguiente manera:

1. En la configuración de ruta física del sitio web, seleccione **Conectar como usuario específico**.
1. Establezca el usuario.

## Evitar ataques de denegación de servicio (DoS) {#prevent-denial-of-service-dos-attacks}

Un ataque de denegación de servicio (DoS) es un intento de hacer que un recurso de equipo no esté disponible para los usuarios previstos.

A nivel de distribuidor, existen dos métodos de configuración para evitar ataques DoS: [](https://docs.adobe.com/content/docs/en/dispatcher.html#/filter (Filtros))

* Utilice el módulo mod_rewrite (por ejemplo, [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)) para realizar validaciones de URL (si las reglas de patrón de URL no son demasiado complejas).

* Impida que el despachante almacene en caché las direcciones URL con extensiones falsas mediante [filtros](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
   Por ejemplo, cambie las reglas de almacenamiento en caché para limitar el almacenamiento en caché a los tipos de MIME esperados, como:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

   Se puede ver un archivo de configuración de ejemplo para [restringir el acceso externo](#restrict-access), que incluye restricciones para tipos de MIME.

Para habilitar de forma segura toda la funcionalidad en las instancias de publicación, configure filtros para evitar el acceso a los nodos siguientes:

* `/etc/`
* `/libs/`

A continuación, configure filtros para permitir el acceso a las rutas de nodo siguientes:

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS y JSON)
* `/libs/cq/security/userinfo.json` (Información de usuario de CQ)
* `/libs/granite/security/currentuser.json` (**los datos no deben almacenarse en caché**)

* `/libs/cq/i18n/*` (Internalización)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## Configure Dispatcher para evitar los ataques CSRF {#configure-dispatcher-to-prevent-csrf-attacks}

AEM proporciona un [marco](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps) destinado a prevenir los ataques de falsificación de solicitudes entre sitios. Para poder utilizar este esquema correctamente, debe lista de permitidos de la compatibilidad de tokens CSRF en el despachante. Puede hacerlo mediante:

1. Creación de un filtro para permitir la ruta `/libs/granite/csrf/token.json`;
1. Añada el encabezado `CSRF-Token` a la sección `clientheaders` de la configuración de Dispatcher.

## Evitar el rastreo de clics {#prevent-clickjacking}

Para evitar el &quot;clickjacking&quot; recomendamos configurar el servidor web para proporcionar el encabezado HTTP `X-FRAME-OPTIONS` establecido en `SAMEORIGIN`.

Para obtener más [información sobre el rastreo de clics, consulte el sitio OWASP](https://www.owasp.org/index.php/Clickjacking).

## Realizar una prueba de penetración {#perform-a-penetration-test}

Adobe recomienda encarecidamente realizar una prueba de penetración de su infraestructura AEM antes de continuar con la producción.

