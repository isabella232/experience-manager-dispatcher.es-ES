---
title: Problemas principales de Dispatcher
seo-title: Top issues for AEM Dispatcher
description: Problemas principales de Dispatcher de AEM
seo-description: Top issues for Adobe AEM Dispatcher
exl-id: 4dcc7318-aba5-4b17-8cf4-190ffefbba75
source-git-commit: f83b02d74a22e055b486305dfe5420e152efb452
workflow-type: tm+mt
source-wordcount: '1578'
ht-degree: 100%

---

# Preguntas más frecuentes sobre los problemas principales de Dispatcher de AEM

![Configurar Dispatcher](assets/CQDispatcher_workflow_v2.png)

## Introducción

### ¿Qué es Dispatcher?

Dispatcher es una herramienta de equilibrio de carga o almacenamiento en caché de Adobe Experience Manager que ayuda a crear un entorno de creación web dinámico y rápido. Para el almacenamiento en caché, Dispatcher funciona como parte de un servidor HTTP, como Apache. Tiene el objetivo de almacenar (o &quot;almacenar en caché&quot;) la mayor cantidad posible de contenido estático del sitio web y acceder al motor de diseño del sitio web con la menor frecuencia posible. En una función de equilibrio de carga, Dispatcher distribuye las solicitudes de usuario (carga) entre diferentes instancias de AEM (procesamientos).

Para almacenar en la caché, el módulo de Dispatcher utiliza la capacidad del servidor web para proporcionar contenido estático. Así pues, Dispatcher coloca los documentos guardados en la caché en la raíz del documento del servidor web.

### ¿Cómo realiza Dispatcher el almacenamiento en caché?

Dispatcher utiliza la capacidad del servidor web para proporcionar contenido estático. Dispatcher almacena documentos en caché en la raíz del documento del servidor web. Dispatcher tiene dos métodos principales para actualizar el contenido de la caché cuando se realizan cambios en el sitio web.

* **Las actualizaciones de contenido** quitan las páginas que han cambiado, así como los archivos que están directamente asociados a ellas.
* **La invalidación automática** invalida automáticamente las partes de la caché que pueden estar desactualizadas tras una actualización. Es decir, marca las páginas relevantes como desactualizadas, sin eliminar nada.

### ¿Cuáles son los beneficios del equilibrio de carga?

El equilibrio de carga distribuye las solicitudes del usuario (carga) entre varias instancias de AEM. La siguiente lista describe las ventajas del equilibrio de carga:

* **Mayor potencia de procesamiento**: en la práctica, esto significa que Dispatcher comparte solicitudes de documentos entre varias instancias de AEM. Dado que cada instancia tiene menos documentos para procesar, los tiempos de respuesta son más rápidos. Dispatcher guarda estadísticas internas de cada categoría de documento, de modo que puede estimar la carga y distribuir las consultas de forma eficaz.
* **Aumento de la cobertura de seguridad contra fallos**: si Dispatcher no recibe respuestas de una instancia, reenviará automáticamente las solicitudes a una de las otras instancias. Por lo tanto, si una instancia deja de estar disponible, el único efecto es una ralentización del sitio, proporcional a la potencia de cálculo perdida.

>[!NOTE]
>
>Para obtener más información, consulte la página [Información general de Dispatcher](dispatcher.md).

## Instalación y configuración

### ¿Dónde puedo descargar el módulo de Dispatcher?

Puede descargar el módulo de Dispatcher más reciente desde la página [Notas de la versión de Dispatcher](release-notes.md).

### ¿Cómo instalo el módulo de Dispatcher?

Consulte la página [Instalar Dispatcher](dispatcher-install.md).

### ¿Cómo configuro el módulo de Dispatcher?

Consulte la página [Configurar Dispatcher](dispatcher-configuration.md).

### ¿Cómo configuro Dispatcher para la instancia de autor?

Para conocer los pasos detallados, consulte [Utilizar Dispatcher con una instancia de autor](dispatcher.md#using-a-dispatcher-with-an-author-server).

### ¿Cómo configuro Dispatcher con varios dominios?

Puede configurar CQ Dispatcher con varios dominios, siempre que estos cumplan las siguientes condiciones:

* El contenido web de ambos dominios debe almacenarse en un único repositorio de AEM
* Los archivos de la caché de Dispatcher se pueden invalidar por separado para cada dominio

Consulte [Utilizar Dispatcher con varios dominios](dispatcher-domains.md) para obtener más información.

### ¿Cómo configuro Dispatcher, de modo que todas las solicitudes de un usuario se enruten a la misma instancia de publicación?

Puede utilizar la función [conexiones fijas](dispatcher-configuration.md#identifying-a-sticky-connection-folder-stickyconnectionsfor), que garantiza que todos los documentos de un usuario se procesen en la misma instancia de AEM. Esta función es importante si utiliza páginas personalizadas y datos de sesión. Los datos se almacenan en la instancia. Por lo tanto, las solicitudes posteriores del mismo usuario deben volver a esa instancia o se perderán los datos.

Como las conexiones fijas restringen la capacidad de Dispatcher para optimizar las solicitudes, debe utilizarlas únicamente cuando sea necesario. Puede especificar la carpeta que contiene los documentos &quot;duraderos&quot;, asegurándose así de que todos los documentos de esa carpeta se procesen en la misma instancia para un usuario.

### ¿Puedo utilizar conexiones fijas y almacenamiento en caché en tándem?

Para la mayoría de las páginas que utilizan conexiones fijas, debe desactivar el almacenamiento en caché. De lo contrario, se mostrará la misma instancia de la página a todos los usuarios, independientemente del contenido de la sesión.

En algunas aplicaciones, puede ser posible utilizar tanto conexiones fijas como almacenamiento en caché. Por ejemplo, si muestra un formulario que escribe datos en una sesión, puede utilizar conexiones fijas y el almacenamiento en caché juntos.

### ¿Puede una instancia de Dispatcher y otra de AEM Publish residir en el mismo equipo físico?

Sí, si la máquina es suficientemente potente. Sin embargo, se recomienda configurar la instancia de Dispatcher y AEM Publish en diferentes equipos.

Normalmente, la instancia de publicación reside dentro del cortafuegos y Dispatcher en la DMZ. Si decide que la instancia de publicación y Dispatcher estén en el mismo equipo físico, asegúrese de que la configuración del cortafuegos prohíba el acceso directo a la instancia de publicación desde redes externas.

### ¿Puedo almacenar en caché solo archivos con extensiones específicas?

Sí. Por ejemplo, si desea almacenar en caché solo archivos GIF, especifique *.gif en la sección de caché del archivo de configuración dispatcher.any.

### ¿Cómo elimino archivos de la caché?

Puede eliminar archivos de la caché utilizando una solicitud HTTP. Cuando reciba la solicitud HTTP, Dispatcher eliminará los archivos de la caché. Dispatcher volverá a almacenar en caché los archivos solo cuando reciba una solicitud de cliente para la página. La eliminación de archivos en caché de esta manera es idónea para sitios web que no reciban solicitudes simultáneas para la misma página.

La solicitud HTTP tiene la siguiente sintaxis:

```
POST /dispatcher/invalidate.cache HTTP/1.1
CQ-Action: Activate
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher eliminará los archivos en caché y las carpetas que tengan nombres que coincidan con el valor del encabezado CQ-Handle. Por ejemplo, un CQ-Handle de `/content/geomtrixx-outdoors/en` coincide con los siguientes elementos:

Todos los archivos (de cualquier extensión de archivo) denominados en el directorio geometrixx-outdoors. 
Cualquier directorio llamado `_jcr_content` debajo del directorio en (que, si existe, contiene procesamientos en caché de subnodos de la página).
El directorio `en` solo se elimina si la variable `CQ-Action` es `Delete` o `Deactivate`.

Para obtener más información sobre este tema, consulte [Invalidar manualmente la caché de Dispatcher](page-invalidate.md).

### ¿Cómo implemento el almacenamiento en caché que distingue permisos?

Consulte la página [Almacenar contenido seguro en caché](permissions-cache.md).

### ¿Cómo puedo asegurar las comunicaciones entre las instancias de Dispatcher y CQ?

Consulte las páginas [Lista de comprobación de seguridad de Dispatcher](security-checklist.md) y [Lista de comprobación de seguridad de AEM](https://experienceleague.adobe.com/docs/experience-manager-64/administering/security/security-checklist.html?lang=es).

### El problema `jcr:content` de Dispatcher cambió a `jcr%3acontent`

**Pregunta**: el negocio se ha enfrentado recientemente a un problema a nivel de Dispatcher. Una de las llamadas AJAX que obtenía algunos datos del repositorio de CQ tenía `jcr:content` en ella. Esto se codificó en `jcr%3acontent` dando como resultado ese conjunto de resultados incorrecto.

**Respuesta**: utilice el método `ResourceResolver.map()` para obtener una URL &quot;favorable&quot; para ser utilizada / desde la que se emitirán peticiones GET y también para resolver el problema de almacenamiento en caché con Dispatcher. El método map() codifica los `:` dos puntos para guiones bajos y el método resolve() los descodifica de nuevo en el formato legible SLING JCR. Utilice el método map() para generar la URL que se utiliza en la llamada de Ajax.

Más información: [https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling](https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling)

## Vaciar Dispatcher

### ¿Cómo configuro los agentes de vaciado de Dispatcher en una instancia de publicación?

Consulte la página [Replicación](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/configuring/replication.html?lang=es#configuring-your-replication-agents).

### ¿Cómo puedo solucionar problemas de vaciado de Dispatcher?

[Consulte estos artículos de solución de problemas](https://experienceleague.adobe.com/search.html?lang=es#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]).

Si las operaciones de eliminación están causando que Dispatcher se vacíe, [use la solución de esta publicación de blog de la comunidad de Sensei Martin](https://mkalugin-cq.blogspot.com/2012/04/i-have-been-working-on-following.html).

### ¿Cómo vacío los recursos DAM de la caché de Dispatcher?

Puede utilizar la funcionalidad &quot;replicación en cadena&quot;. Con esta funcionalidad activada, el agente de vaciado de Dispatcher enviará una solicitud de vaciado cuando reciba una replicación del autor.

Para habilitarlo:

1. [Siga los siguientes pasos](page-invalidate.md#invalidating-dispatcher-cache-from-a-publishing-instance) para crear agentes de vaciado al publicar.
1. Vaya a la configuración de cada uno de estos agentes y, en la pestaña **Activadores**, marque el cuadro **Al recibir**.

## Varios

¿Cómo determina Dispatcher si un documento está actualizado?
Para determinar si un documento está actualizado, Dispatcher realiza estas acciones:

Comprueba si el documento depende de la invalidación automática. Si no depende de ello, considera que el documento está actualizado.
Si el documento está configurado para la invalidación automática, Dispatcher comprueba si es anterior o posterior a la fecha del último cambio disponible. Si es anterior, Dispatcher solicita la versión actual a la instancia de AEM y reemplaza la versión en la caché.

### ¿Cómo devuelve documentos Dispatcher?

Puede definir si Dispatcher almacena en caché un documento utilizando el archivo [Configuración de Dispatcher](dispatcher-configuration.md), `dispatcher.any`. Dispatcher comprueba la solicitud con la lista de documentos que se pueden almacenar en caché. Si el documento no está en esta lista, Dispatcher solicita el documento a la instancia de AEM.

La propiedad `/rules` controla qué documentos se almacenan en caché según la ruta del documento. Independientemente de la propiedad `/rules`, Dispatcher nunca almacena en caché un documento en las siguientes circunstancias:

* Si el URI de la solicitud contiene el signo de interrogación.`(?)`
* Esto indica una página dinámica, como un resultado de búsqueda, que no necesita almacenarse en la caché.
* Si falta la extensión del archivo.
* El servidor web necesita la extensión para determinar el tipo de documento (el tipo MIME).
* El encabezado de autenticación está establecido (configurable).
* Si la instancia de AEM responde con los siguientes encabezados:
   * sin caché
   * sin almacenamiento
   * se debe revalidar

Dispatcher almacena los archivos en la caché del servidor web como si formasen parte de un sitio web estático. Si un usuario solicita un documento almacenable en la caché, Dispatcher comprobará si el documento existe en el archivo de sistema del servidor web. Si es así, Dispatcher devolverá los documentos. Si no, Dispatcher solicitará el documento a la instancia de AEM.

>[!NOTE]
>
>Dispatcher puede almacenar en caché los métodos GET o HEAD (para el encabezado HTTP). Para obtener información adicional sobre el almacenamiento en caché de encabezados de respuesta, consulte la sección [Almacenamiento en caché de encabezados de respuesta HTTP](dispatcher-configuration.md#caching-http-response-headers).

### ¿Puedo implementar varios Dispatcher en una configuración?

Sí. En estos casos, asegúrese de que ambos Dispatcher puedan acceder al sitio web de AEM directamente. Un Dispatcher no puede administrar solicitudes procedentes de otro Dispatcher.
