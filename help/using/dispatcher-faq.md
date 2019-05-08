---
title: Principales problemas de Dispatcher
seo-title: Principales problemas de AEM Dispatcher
description: Principales problemas de AEM Dispatcher
seo-description: Principales problemas de Adobe AEM Dispatcher
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Preguntas frecuentes sobre problemas principales de AEM Dispatcher

![Configuración de Dispatcher](assets/CQDispatcher_workflow_v2.png)

## Introducción

### ¿Qué es Dispatcher?

Dispatcher es la herramienta de equilibrio de carga y almacenamiento en caché de Adobe Experience Manager que ayuda a lograr un entorno de creación Web rápida y dinámica. Para el almacenamiento en caché, Dispatcher funciona como parte de un servidor HTTP, como Apache, con el objetivo de almacenar (o «almacenar en caché») la mayor parte del contenido estático del sitio web y acceder al motor de presentación del sitio web con la menor frecuencia posible. En una función de equilibrio de carga, Dispatcher distribuye solicitudes de usuario (load) en diferentes instancias de AEM (procesamientos).

En el caso de la caché, el módulo Dispatcher utiliza la capacidad del servidor Web para proporcionar contenido estático. Dispatcher coloca los documentos en caché en la raíz del documento del servidor Web.

### ¿Cómo realiza Dispatcher el almacenamiento en caché?

Dispatcher utiliza la capacidad del servidor Web para proporcionar contenido estático. Dispatcher almacena documentos almacenados en caché en la raíz del documento del servidor web. Dispatcher tiene dos métodos principales para actualizar el contenido de la caché cuando se realizan cambios en el sitio web.

* **Las actualizaciones** de contenido eliminan las páginas que han cambiado, así como los archivos directamente asociados con ellos.
* **Invalidación automática** invalida automáticamente las partes de la caché que pueden quedar obsoletas después de una actualización. Por ejemplo, marca de forma eficaz las páginas relevantes como obsoletas, sin eliminar nada.

### ¿Cuáles son los beneficios del equilibrio de carga?

Equilibrio de carga distribuye las solicitudes de usuario (load) en varias instancias de AEM. La siguiente lista describe las ventajas para el equilibrio de carga:

* **Aumento de la potencia de procesamiento**: En la práctica, el despachante comparte solicitudes de documento entre varias instancias de AEM. Dado que cada instancia tiene menos documentos para procesar, tiene tiempos de respuesta más rápidos. Dispatcher mantiene las estadísticas internas de cada categoría de documento, de modo que puede calcular la carga y distribuir las consultas de forma eficaz.
* **Aumento de cobertura segura**: Si Dispatcher no recibe respuestas de una instancia, enviará solicitudes automáticamente a una de las demás instancias. Por lo tanto, si una instancia deja de estar disponible, el único efecto es una ralentización del sitio, proporcional a la potencia informática perdida.

>[!NOTE]
>
>Para obtener más información, consulte la página Información general [de Dispatcher](dispatcher.md)

## Instalar y configurar

### ¿De dónde puedo descargar el módulo Dispatcher?

Puede descargar el módulo Distribuidor más reciente desde la página [Notas](release-notes.md) de la versión de Dispatcher.

### ¿Cómo instalo el módulo Dispatcher?

Refer to the [Installing Dispatcher](dispatcher-install.md) page

### ¿Cómo configuro el módulo Dispatcher?

Consulte [la](dispatcher-configuration.md) página Configurar Dispatcher.

### ¿Cómo configuro Dispatcher para la instancia de autor?

Consulte [Uso de Dispatcher con una instancia de autor](dispatcher.md#using-a-dispatcher-with-an-author-server) para ver los pasos detallados.

### ¿Cómo configuro Dispatcher con varios dominios?

Puede configurar CQ Dispatcher con varios dominios, siempre que los dominios cumplan las condiciones siguientes:

* El contenido web para ambos dominios se almacena en un solo repositorio de AEM
* Los archivos de la caché de Dispatcher se pueden invalidar por separado para cada dominio

Leer [con Dispatcher con varios dominios](dispatcher-domains.md) para obtener más información.

### ¿Cómo configuro Dispatcher, de modo que todas las solicitudes de un usuario se dirigen a la misma instancia de publicación?

Puede utilizar [la función de conexiones](dispatcher-configuration.md#identifying-a-sticky-connection-folder-stickyconnectionsfor) adhesivas, que garantiza que todos los documentos de un usuario se procesen en la misma instancia de AEM. Esta característica es importante si utiliza páginas personalizadas y datos de sesión. Los datos se almacenan en la instancia. Por lo tanto, las solicitudes posteriores del mismo usuario deben volver a esa instancia o se pierden los datos.

Puesto que las conexiones adhesivas restringen la capacidad de Dispatcher para optimizar las solicitudes, debe utilizar este enfoque solo cuando sea necesario. Puede especificar la carpeta que contiene los documentos &quot;adhesivos&quot;, asegurándose de que todos los documentos de esa carpeta se procesan en la misma instancia para un usuario.

### ¿Puedo utilizar conexiones adhesivas y almacenamiento en caché en conjunto?

Para la mayoría de las páginas que utilizan conexiones adhesivas, debe desactivar el almacenamiento en caché. De lo contrario, se muestra la misma instancia de la página a todos los usuarios, independientemente del contenido de la sesión.

Para algunas aplicaciones, puede ser posible utilizar conexiones adhesivas y almacenamiento en caché. Por ejemplo, si muestra un formulario que escribe datos en una sesión, puede utilizar conexiones adhesivas y almacenamiento en caché en conjunto.

### ¿Puede un despachante y una instancia de AEM Publish residir en el mismo equipo físico?

Sí, si el ordenador es lo suficientemente potente. Sin embargo, se recomienda configurar Dispatcher y la instancia de AEM Publish en diferentes equipos.

Normalmente, la instancia de Publish reside dentro del servidor de seguridad y Dispatcher reside en DMZ. Si decide tener la instancia de publicación y Dispatcher en el mismo equipo físico, asegúrese de que la configuración del cortafuegos prohíba el acceso directo a la instancia de publicación de las redes externas.

### ¿Solo puedo almacenar en caché archivos con extensiones específicas?

Sí. Por ejemplo, si desea almacenar en caché únicamente archivos GIF, especifique *. gif en la sección caché del archivo de configuración Dispatcher. any.

### ¿Cómo elimino archivos de la caché?

Puede eliminar archivos de la caché utilizando una solicitud HTTP. Cuando se recibe la solicitud HTTP, Dispatcher elimina los archivos de la caché. Dispatcher almacena en caché los archivos de nuevo solo cuando recibe una solicitud de cliente de la página. La eliminación de archivos almacenados en caché de esta manera es adecuada para los sitios Web que no tienen probabilidad de recibir solicitudes simultáneas para la misma página.

La solicitud HTTP tiene la siguiente sintaxis:

```
POST /dispatcher/invalidate.cache HTTP/1.1
CQ-Action: Activate
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher elimina los archivos en caché y las carpetas que tienen nombres que coinciden con el valor del encabezado CQ-Handle. Por ejemplo, un control CQ de `/content/geomtrixx-outdoors/en` coincide con los siguientes elementos:

Todos los archivos (de cualquier extensión de archivo) nombrados en el directorio
geometrixx-outdoors Cualquier directorio llamado `_jcr_content` debajo del directorio en (que, si existe, contiene representaciones almacenadas en caché de subnodos de la página)
El directorio en se eliminará únicamente si es `CQ-Action``Delete` o `Deactivate`.

Para obtener más información sobre este tema, consulte [Inutilización manual de la caché de Dispatcher](page-invalidate.md).

### ¿Cómo implemento almacenamiento en caché sensible a permisos?

Consulte [la página Caché de contenido](permissions-cache.md) seguro.

### ¿Cómo puedo proteger las comunicaciones entre las instancias de Dispatcher y CQ?

Consulte la lista Comprobación de seguridad [de Dispatcher](security-checklist.md) y las páginas [de Comprobación](https://helpx.adobe.com/experience-manager/6-4/sites/administering/using/security-checklist.html) de seguridad de AEM.

### Se `jcr:content` cambió el número de Dispatcher a `jcr%3acontent`

**Pregunta**: Recientemente, se ha detectado un problema en el nivel de dispatcher, en el que una de las llamadas de ajax que tenía un repositorio de datos de formulario de CQ `jcr:content` tenía en él y que se codificaba para `jcr%3acontent` generar un resultado incorrecto.

**Respuesta**: Utilice `ResourceResolver.map()` el método para obtener una URL&#39;práctica&#39;que se utilice o solicite obtener solicitudes y también para resolver el problema de caché con Dispatcher. El método map () codifica `:` los dos puntos a guiones bajos y el método resolve () los devuelve al formato legible SLING JCR. Debe utilizar el método map () para generar la URL que se utiliza en la llamada de Ajax.

Más información: [https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling](https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling)

## Vaciar el despachante

### ¿Cómo configuro los agentes vaciados de Dispatcher en una instancia de publicación?

Consulte la página [Replicación](https://helpx.adobe.com/content/help/en/experience-manager/6-4/sites/deploying/using/replication.html#ConfiguringyourReplicationAgents) .

### ¿Cómo puedo solucionar problemas de despachante Dispatcher?

[Consulte este artículo de solución de problemas](https://helpx.adobe.com/content/help/en/experience-manager/kb/troubleshooting-dispatcher-flushing-issues.html) que responde a las preguntas siguientes:

* ¿Cómo debo depurar una situación en la que no se guarde contenido en la caché de Dispatcher?
* ¿Cómo depuro una situación en la que los archivos de caché no se actualizan?
* ¿Cómo depuro una situación en la que no funciona nada relacionado con la vaciación de despachante?

Si las operaciones de Eliminar hacen que despachen Dispatcher [, utilice la solución en este anuncio de blog de la comunidad de Sensei Martin](https://mkalugin-cq.blogspot.in/2012/04/i-have-been-working-on-following.html).

### ¿Cómo vacio los recursos DAM desde la caché de Dispatcher?

Puede utilizar la función «replicación de cadena». Cuando esta función está habilitada, el agente de vaciado despachante envía una solicitud de vaciado cuando se recibe una replicación del autor.

Para habilitar:

1. [Siga los pasos aquí](page-invalidate.md#invalidating-dispatcher-cache-from-a-publishing-instance) descritos para crear agentes de vaciado en la publicación
1. Vaya a cada una de las configuraciones de agente y, en la ficha **Triggers** , marque el cuadro **En recepción** .

## Varios

¿Cómo determina Dispatcher si un documento está actualizado?
Para determinar si un documento está actualizado, Dispatcher realiza estas acciones:

Comprueba si el documento está sujeto a invalidación automática. Si no es así, el documento se considera actualizado.
Si el documento está configurado para invalidación automática, Dispatcher comprueba si es más antiguo o más nuevo que el último cambio disponible. Si es anterior, Dispatcher solicita la versión actual de la instancia de AEM y reemplaza la versión en la caché.

### ¿Cómo devuelve Dispatcher los documentos?

You can define whether the Dispatcher caches a document by using the [Dispatcher configuration](dispatcher-configuration.md) file, `dispatcher.any`. Dispatcher comprueba la solicitud en la lista de documentos almacenables. Si el documento no está en esta lista, Dispatcher solicita el documento desde la instancia de AEM.

La `/rules` propiedad controla qué documentos se almacenan en caché según la ruta del documento. Independientemente `/rules` de la propiedad, Dispatcher nunca almacena en caché un documento en las siguientes circunstancias:

* Si el URI de la solicitud contiene un signo `(?)`de interrogación.
* Esto suele indicar una página dinámica, como un resultado de búsqueda que no es necesario almacenar en caché.
* Falta la extensión del archivo.
* El servidor web necesita la extensión para determinar el tipo de documento (el tipo MIME).
* Se establece el encabezado de autenticación (esto puede configurarse)
* Si la instancia de AEM responde con los encabezados siguientes:
   * no-cache
   * no store
   * must-revalidate

Dispatcher almacena archivos almacenados en caché en el servidor web como si formaran parte de un sitio web estático. Si un usuario solicita un documento en caché, Dispatcher comprueba si el documento existe en el sistema de archivos del servidor web. Si es así, Dispatcher devuelve los documentos. De lo contrario, Dispatcher solicita el documento desde la instancia de AEM.

>[!NOTE]
>
>Los métodos GET o HEAD (para los encabezados HTTP) son almacenables por Dispatcher. Para obtener más información sobre el almacenamiento en caché de encabezados de respuesta, consulte [la sección Caché de encabezados de respuesta](dispatcher-configuration.md#caching-http-response-headers) HTTP.

### ¿Puedo implementar varios Dispatcher en una configuración?

Sí. En estos casos, asegúrese de que ambos usuarios pueden acceder directamente al sitio Web de AEM. Dispatcher no puede gestionar solicitudes procedentes de otro despachante.

