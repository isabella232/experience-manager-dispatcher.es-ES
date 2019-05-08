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
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Lista Comprobación de seguridad de Dispatcher{#the-dispatcher-security-checklist}

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
>También debe completar la lista de comprobación de seguridad de su versión de AEM antes de publicarla. Consulte la documentación correspondiente [de Adobe Experience Manager](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html).

## Utilizar la versión más reciente de Dispatcher {#use-the-latest-version-of-dispatcher}

Debe instalar la versión disponible más reciente disponible para su plataforma. Debe actualizar la instancia de Dispatcher para utilizar la última versión para aprovechar las mejoras de producto y seguridad. Consulte [Instalación de Dispatcher](dispatcher-install.md).

>[!NOTE]
>
>Para comprobar la versión actual de la instalación de su distribuidor, consulte el archivo de registro de Dispatcher.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Para encontrar el archivo de registro, inspeccione la configuración de Dispatcher en `httpd.conf`su.

## Restringir clientes que pueden vaciar la caché {#restrict-clients-that-can-flush-your-cache}

Adobe recomienda [limitar los clientes que pueden vaciar la caché.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## Activación de HTTPS para la seguridad de la capa de transporte {#enable-https-for-transport-layer-security}

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

## Restringir acceso {#restrict-access}

Al configurar Dispatcher, debe restringir el acceso externo en la medida de lo posible. Consulte [Ejemplo /filter Sección](dispatcher-configuration.md#main-pars_184_1_title) en la documentación de Dispatcher.

## Asegúrese de que el acceso a las URL administrativas se deniegue {#make-sure-access-to-administrative-urls-is-denied}

Asegúrese de utilizar filtros para bloquear el acceso externo a cualquier URL administrativa, como la Consola Web.

Consulte [Prueba de seguridad de Dispatcher](dispatcher-configuration.md#testing-dispatcher-security) para ver una lista de las direcciones URL que deben bloquearse.

## Utilizar listas blancas en lugar de listas negras {#use-whitelists-instead-of-blacklists}

Las listas blancas son una manera mejor de proporcionar control de acceso porque inherentemente, suponen que todas las solicitudes de acceso deben denegarse a menos que formen parte explícitamente de la lista blanca. Este modelo proporciona un control más restrictivo sobre las nuevas solicitudes que pueden no haberse revisado aún ni tener en cuenta durante una determinada fase de configuración.

## Ejecutar Dispatcher con un usuario de sistema dedicado {#run-dispatcher-with-a-dedicated-system-user}

Al configurar Dispatcher, debe asegurarse de que el servidor web esté ejecutándose por un usuario dedicado con menos privilegios. Se recomienda conceder sólo escritura de escritura a la carpeta de caché de Dispatcher.

Additionnaly, los usuarios de IIS necesitan configurar su sitio web de la siguiente manera:

1. En la configuración de ruta física del sitio Web, seleccione **Connect como usuario específico**.
1. Establezca el usuario.

## Impedir ataques de negación de servicio (DoS) {#prevent-denial-of-service-dos-attacks}

Un ataque de denegación de servicio (DoS) intenta hacer que un recurso informático no esté disponible para los usuarios previstos.

A nivel de Dispatcher, existen dos métodos para configurar para evitar ataques DOS: [](https://docs.adobe.com/content/docs/en/dispatcher.html#/filter (Filtros))

* Utilice el módulo mod_ rewrite (por ejemplo [, Apache 2.2](https://httpd.apache.org/docs/2.2/mod/mod_rewrite.html)) para realizar validaciones de URL (si las reglas de patrón de URL no son demasiado complejas).

* Evite el despachante de URL en caché con extensiones borrosas mediante [filtros](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
   Por ejemplo, cambie las reglas de caché para limitar el almacenamiento en caché a los tipos MIME esperados, como:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`
   Se puede ver un archivo de configuración de ejemplo [para restringir el acceso externo](#restrict-access); esto incluye restricciones para tipos MIME.

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

## Configure Dispatcher para evitar ataques CSRF {#configure-dispatcher-to-prevent-csrf-attacks}

AEM proporciona [un marco](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps) destinado a evitar ataques de falsificación de solicitudes entre sitios. Para utilizar correctamente este marco, necesita la compatibilidad con tokens de CSRF en el despachante. Puede hacerlo por:

1. Creación de un filtro para permitir `/libs/granite/csrf/token.json` la ruta;
1. Agregue `CSRF-Token` el encabezado a la `clientheaders` sección de la configuración de Dispatcher.

## Impedir clickjacking {#prevent-clickjacking}

Para evitar el fraude de clics, le recomendamos que configure su servidor web para que proporcione el encabezado `X-FRAME-OPTIONS` HTTP definido `SAMEORIGIN`.

Para obtener más [información sobre el secuestro de clics, consulte el sitio OWASP](https://www.owasp.org/index.php/Clickjacking).

## Realizar una prueba de penetración {#perform-a-penetration-test}

Adobe recomienda encarecidamente realizar una prueba de penetración de la infraestructura de AEM antes de continuar con la producción.

