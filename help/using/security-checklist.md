---
title: Lista Comprobación de seguridad de Dispatcher
seo-title: Lista Comprobación de seguridad de Dispatcher
description: Lista de comprobación de seguridad que debe completarse antes de proceder con la producción.
seo-description: Lista de comprobación de seguridad que debe completarse antes de proceder con la producción.
uuid: 7 bfa 3202-03 f 6-48 e 9-8 d 2 e -2 a 40 e 137 ecbe
contentOwner: Usuario
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referencia
discoiquuid: fbfafa 55-c 029-4 ed 7-ab 3 e -1 bebfde 18248
jcr-lastmodifiedby: remove-legacypath -6-1
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: 6d3ff696780ce55c077a1d14d01efeaebcb8db28

---


# The Dispatcher Security Checklist{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

El despachante como sistema front-end ofrece una capa adicional de seguridad a su infraestructura de Adobe Experience Manager. Adobe recomienda enfáticamente que complete la siguiente lista de control antes de proceder con la producción.

>[!CAUTION]
>
>También debe completar la lista de comprobación de seguridad de su versión de AEM antes de publicarla. Please refer to the corresponding [Adobe Experience Manager documentation](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html).

## Use the Latest Version of Dispatcher {#use-the-latest-version-of-dispatcher}

Debe instalar la versión disponible más reciente disponible para su plataforma. Debe actualizar la instancia de Dispatcher para utilizar la última versión para aprovechar las mejoras de producto y seguridad. See [Installing Dispatcher](dispatcher-install.md).

>[!NOTE]
>
>Para comprobar la versión actual de la instalación de su distribuidor, consulte el archivo de registro de Dispatcher.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>To find the log file, inspect the dispatcher configuration in your `httpd.conf`.

## Restrict Clients that Can Flush Your Cache {#restrict-clients-that-can-flush-your-cache}

Adobe recommends that you [limit the clients that can flush your cache.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## Enable HTTPS for transport layer security {#enable-https-for-transport-layer-security}

Adobe recomienda activar la capa de transporte HTTPS tanto en instancias de autor como de publicación.

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

## Restrict Access {#restrict-access}

Al configurar Dispatcher, debe restringir el acceso externo en la medida de lo posible. See [Example /filter Section](dispatcher-configuration.md#main-pars_184_1_title) in the Dispatcher documentation.

## Make Sure Access to Administrative URLs is Denied {#make-sure-access-to-administrative-urls-is-denied}

Asegúrese de utilizar filtros para bloquear el acceso externo a cualquier URL administrativa, como la Consola Web.

See [Testing Dispatcher Security](dispatcher-configuration.md#testing-dispatcher-security) for a list of URLs that need to be blocked.

## Use Whitelists Instead Of Blacklists {#use-whitelists-instead-of-blacklists}

Las listas blancas son una manera mejor de proporcionar control de acceso porque inherentemente, suponen que todas las solicitudes de acceso deben denegarse a menos que formen parte explícitamente de la lista blanca. Este modelo proporciona un control más restrictivo sobre las nuevas solicitudes que pueden no haberse revisado aún ni tener en cuenta durante una determinada fase de configuración.

## Run Dispatcher with a Dedicated System User {#run-dispatcher-with-a-dedicated-system-user}

Al configurar Dispatcher, debe asegurarse de que el servidor web esté ejecutándose por un usuario dedicado con menos privilegios. Se recomienda conceder sólo escritura de escritura a la carpeta de caché de Dispatcher.

Additionnaly, los usuarios de IIS necesitan configurar su sitio web de la siguiente manera:

1. In the physical path setting for your web site, select **Connect as specific user**.
1. Establezca el usuario.

## Prevent Denial of Service (DoS) Attacks {#prevent-denial-of-service-dos-attacks}

Un ataque de denegación de servicio (DoS) intenta hacer que un recurso informático no esté disponible para los usuarios previstos.

At the dispatcher level, there are two methods of configuring to prevent DoS attacks: [](https://docs.adobe.com/content/docs/en/dispatcher.html#/filter (Filters))

* Use the mod_rewrite module (for example, [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)) to perform URL validations (if the URL pattern rules are not too complex).

* Prevent the dispatcher from caching URLs with spurious extensions by using [filters](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
   Por ejemplo, cambie las reglas de caché para limitar el almacenamiento en caché a los tipos MIME esperados, como:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`
   An example configuration file can be seen for [restricting external access](#restrict-access), this includes restrictions for mime types.

Para activar de forma segura toda la funcionalidad en las instancias de publicación, configure filtros para impedir el acceso a los siguientes nodos:

* `/etc/`
* `/libs/`

A continuación, configure filtros para permitir el acceso a las siguientes rutas de nodo:

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

## Configure Dispatcher to prevent CSRF Attacks {#configure-dispatcher-to-prevent-csrf-attacks}

AEM provides a [framework](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps) aimed at preventing Cross-Site Request Forgery attacks. Para utilizar correctamente este marco, necesita la compatibilidad con tokens de CSRF en el despachante. Puede hacerlo por:

1. Creating a filter to allow the `/libs/granite/csrf/token.json` path;
1. Add the `CSRF-Token` header to the `clientheaders` section of the Dispatcher configuration.

## Prevent Clickjacking {#prevent-clickjacking}

To prevent clickjacking we recommend that you configure your webserver to provide the `X-FRAME-OPTIONS` HTTP header set to `SAMEORIGIN`.

For more [information on clickjacking please see the OWASP site](https://www.owasp.org/index.php/Clickjacking).

## Perform a Penetration Test {#perform-a-penetration-test}

Adobe recomienda encarecidamente realizar una prueba de penetración de la infraestructura de AEM antes de continuar con la producción.

