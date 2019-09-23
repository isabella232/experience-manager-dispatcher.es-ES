---
title: Información general de Dispatcher
seo-title: Información general de Adobe AEM Dispatcher
description: Este artículo proporciona una visión general de Dispatcher.
seo-description: Este artículo proporciona una descripción general de Adobe Experience Manager Dispatcher.
uuid: 71766f86-5e91-446b-a078-061b179d090d
pageversionid: '1193211344162'
topic-tags: dispatcher
content-type: referencia
discoiquuid: 1d449ee2-4cdd-4b7a-8b4e-7e6fc0a1d7ee
translation-type: tm+mt
source-git-commit: de6a513baf3e6b1a1463a442fa840e59f2196e8e

---


# Información general de Dispatcher {#dispatcher-overview}

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher incrustada en la documentación de una versión anterior de AEM.

Dispatcher es la herramienta de almacenamiento en caché o equilibrio de carga de Adobe Experience Manager. El uso de Dispatcher de AEM también ayuda a proteger el servidor AEM de ataques. Por lo tanto, puede aumentar la seguridad de su instancia de AEM mediante Dispatcher junto con un servidor web de clase empresarial.

El proceso de implementación de Dispatcher es independiente del servidor web y de la plataforma del sistema operativo elegida:

1. Obtenga información sobre Dispatcher (esta página). Consulte también las preguntas [más frecuentes sobre el despachante](https://helpx.adobe.com/experience-manager/using/dispatcher-faq.html).
1. Instale un servidor [web](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html) compatible según la documentación del servidor web.

1. [Instale el módulo](dispatcher-install.md) Dispatcher en el servidor web y configure el servidor web en consecuencia.
1. [Configure Dispatcher](dispatcher-configuration.md) (el archivo dispatcher.any).

1. [Configure AEM](page-invalidate.md) para que las actualizaciones de contenido invaliden la caché.

>[!NOTE]
>
>Para comprender mejor cómo funciona Dispatcher con AEM, consulte [Preguntar a los expertos de la comunidad de AEM para julio de 2017](https://bit.ly/ATACE0717).

Utilice la siguiente información según sea necesario:

* [Lista de comprobación de seguridad del despachante](security-checklist.md)
* [Base de conocimiento del despachante](https://helpx.adobe.com/cq/kb/index/dispatcher.html)
* [Optimización de un sitio web para el rendimiento de la caché](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html)
* [Uso de Dispatcher con varios dominios](dispatcher-domains.md)
* [Uso de SSL con Dispatcher](dispatcher-ssl.md)
* [Implementación de almacenamiento en caché con permisos sensibles](permissions-cache.md)
* [Resolución de problemas de Dispatcher](dispatcher-troubleshooting.md)
* [Preguntas más frecuentes sobre los problemas principales de Dispatcher](dispatcher-faq.md)

>[!NOTE]
>
>**El uso más común de Dispatcher** es almacenar en caché las respuestas de una instancia **de** publicación de AEM, para aumentar la capacidad de respuesta y la seguridad del sitio web publicado con cara externa. La mayor parte de la discusión se centra en este caso.
>
>Sin embargo, Dispatcher también puede utilizarse para aumentar la capacidad de respuesta de la instancia **de** autor, especialmente si tiene un gran número de usuarios editando y actualizando el sitio web. Para obtener información específica sobre este caso, consulte [Uso de Dispatcher con un servidor](#using-a-dispatcher-with-an-author-server)de creación más abajo.

## ¿Por qué utilizar Dispatcher para implementar el almacenamiento en caché? {#why-use-dispatcher-to-implement-caching}

Existen dos enfoques básicos para la publicación web:

* **Servidores** Web estáticos: como Apache o IIS, son muy simples, pero rápidos.
* **Servidores** de administración de contenido: que proporcionan contenido dinámico, en tiempo real e inteligente, pero requieren mucho más tiempo de cálculo y otros recursos.

Dispatcher ayuda a crear un entorno rápido y dinámico. Funciona como parte de un servidor HTML estático, como Apache, con el objetivo de:

* almacenar (o "almacenar en caché") la mayor parte del contenido del sitio posible, en forma de sitio web estático
* acceder al motor de diseño lo menos posible.

Lo que significa que:

* **el contenido** estático se gestiona con la misma velocidad y facilidad que en un servidor web estático;*además, puede utilizar las herramientas de administración y seguridad disponibles para los servidores web estáticos*.

* **el contenido** dinámico se genera según sea necesario, sin ralentizar el sistema más de lo absolutamente necesario.

Dispatcher contiene mecanismos para generar y actualizar HTML estático basado en el contenido del sitio dinámico. Puede especificar detalladamente qué documentos se almacenan como archivos estáticos y cuáles se generan siempre dinámicamente.

Esta sección ilustra los principios que subyacen a esto.

### Servidor Web estático {#static-web-server}

![](assets/chlimage_1-3.png)

Un servidor web estático, como Apache o IIS, sirve archivos HTML estáticos a los visitantes del sitio web. Las páginas estáticas se crean una vez, por lo que se enviará el mismo contenido para cada solicitud.

Este proceso es muy sencillo y, por lo tanto, extremadamente eficiente. Si un visitante solicita un archivo (por ejemplo, una página HTML), el archivo se toma generalmente directamente de la memoria, en el peor de los casos se lee desde la unidad local. Los servidores web estáticos han estado disponibles durante bastante tiempo, por lo que existe una amplia gama de herramientas para la administración y la gestión de la seguridad, y están muy bien integrados con las infraestructuras de red.

### Servidores de administración de contenido {#content-management-servers}

![](assets/chlimage_1-4.png)

Si utiliza un servidor de administración de contenido, como AEM, un motor de diseño avanzado procesa la solicitud de un visitante. El motor lee el contenido de un repositorio que, combinado con estilos, formatos y derechos de acceso, transforma el contenido en un documento adaptado a las necesidades y derechos del visitante.

Esto le permite crear contenido dinámico más rico, lo que aumenta la flexibilidad y funcionalidad del sitio web. Sin embargo, el motor de diseño requiere más potencia de procesamiento que un servidor estático, por lo que esta configuración puede resultar más lenta si muchos visitantes utilizan el sistema.

## Funcionamiento de Dispatcher en caché {#how-dispatcher-performs-caching}

![](assets/chlimage_1-5.png)

**Directorio** de caché Para almacenamiento en caché, el módulo Dispatcher utiliza la capacidad del servidor web para ofrecer contenido estático. El despachante coloca los documentos en caché en la raíz del documento del servidor web.

>[!NOTE]
>
>Cuando no hay configuración para el almacenamiento en caché de encabezados HTTP, Dispatcher almacena únicamente el código HTML de la página, no almacena los encabezados HTTP. Esto puede suponer un problema si utiliza diferentes codificaciones en el sitio web, ya que pueden perderse. Para habilitar el almacenamiento en caché de encabezados HTTP, consulte [Configuración de la caché del despachante.](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html)

>[!NOTE]
>
>La ubicación de la raíz del documento de su servidor Web en el almacenamiento de información conectado en red (NAS) provoca una degradación del performance. Además, cuando se comparte una raíz de documento ubicada en NAS entre varios servidores Web, pueden producirse bloqueos intermitentes cuando se realizan acciones de replicación.

>[!NOTE]
>
>El despachante almacena el documento en caché en una estructura igual a la dirección URL solicitada.
>
>Puede haber limitaciones de nivel de sistema operativo para la longitud del nombre del archivo; es decir, si tiene una dirección URL con muchos selectores.

### Métodos de almacenamiento en caché

Dispatcher tiene dos métodos principales para actualizar el contenido de la caché cuando se realizan cambios en el sitio web.

* **Las actualizaciones** de contenido eliminan las páginas que han cambiado, así como los archivos que están directamente asociados a ellas.
* **La invalidación** automática invalida automáticamente las partes de la caché que pueden estar desactualizadas tras una actualización. es decir, marca efectivamente las páginas relevantes como obsoletas, sin eliminar nada.

### Actualizaciones de contenido

En una actualización de contenido, cambian uno o varios documentos de AEM. AEM envía una solicitud de distribución al despachante, que actualiza la caché en consecuencia:

1. Elimina los archivos modificados de la caché.
1. Elimina todos los archivos que comienzan con el mismo controlador de la caché. Por ejemplo, si se actualiza el archivo /en/index.html, todos los archivos empezarán con /es/index. se eliminan. Este mecanismo le permite diseñar sitios eficientes en caché, especialmente en lo que respecta a la navegación de imágenes.
1. Se *toca* al llamado **statfile**; esto actualiza la marca de tiempo del archivo de estado para indicar la fecha del último cambio.

Cabe señalar los siguientes puntos:

* Las actualizaciones de contenido se utilizan generalmente junto con un sistema de creación que "sabe" qué se debe reemplazar.
* Los archivos afectados por una actualización de contenido se eliminan, pero no se reemplazan inmediatamente. La próxima vez que se solicite un archivo de este tipo, Dispatcher obtiene el nuevo archivo de la instancia de AEM y lo coloca en la caché, con lo que se sobrescribe el contenido antiguo.
* Normalmente, las imágenes generadas automáticamente que incorporan texto de una página se almacenan en archivos de imagen que comienzan con el mismo control, lo que garantiza que la asociación existe para la eliminación. Por ejemplo, puede almacenar el texto del título de la página mypage.html como imagen mypage.titlePicture.gif en la misma carpeta. De este modo, la imagen se elimina automáticamente de la caché cada vez que se actualiza la página, por lo que puede estar seguro de que la imagen siempre refleja la versión actual de la página.
* Puede tener varios archivos de estado, por ejemplo, uno por carpeta de idioma. Si se actualiza una página, AEM busca la siguiente carpeta principal que contenga un archivo de estado y *toca* dicho archivo.

### Invalidez automática

La invalidación automática invalida automáticamente partes de la caché, sin eliminar físicamente ningún archivo. En cada actualización de contenido, se modifica el llamado archivo de estado, por lo que su marca de tiempo refleja la última actualización de contenido.

Dispatcher tiene una lista de archivos que están sujetos a invalidación automática. Cuando se solicita un documento de esa lista, el despachante compara la fecha del documento en caché con la marca de tiempo del archivo de estado:

* si el documento en caché es más reciente, el despachante lo devuelve.
* si es anterior, Dispatcher recupera la versión actual de la instancia de AEM.

Cabe señalar también algunos puntos:

* La invalidación automática se suele utilizar cuando las interrelaciones son complejas, por ejemplo, para páginas HTML. Estas páginas contienen vínculos y entradas de navegación, por lo que normalmente deben actualizarse después de actualizar el contenido. Si ha generado automáticamente archivos PDF o de imagen, puede optar por invalidarlos automáticamente también.
* La invalidación automática no implica ninguna acción por parte del despachante en el momento de la actualización, excepto por tocar el archivo de estado. Sin embargo, al tocar el archivo de estado automáticamente, el contenido de la caché queda obsoleto, sin eliminarlo físicamente de la caché.

## Cómo devuelve documentos Dispatcher {#how-dispatcher-returns-documents}

![](assets/chlimage_1-6.png)

### Determinación de si un documento está sujeto a almacenamiento en caché

Puede [definir qué documentos almacena en caché el despachante en el archivo](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html)de configuración. El despachante comprueba la solicitud con la lista de documentos que se pueden almacenar en caché. Si el documento no está en esta lista, Dispatcher solicita el documento a la instancia de AEM.

El despachante *siempre* solicita el documento directamente desde la instancia de AEM en los siguientes casos:

* Si el URI de la solicitud contiene el signo de interrogación "?". Esto generalmente indica una página dinámica, como un resultado de búsqueda, que no necesita almacenarse en caché.
* Falta la extensión del archivo. El servidor web necesita la extensión para determinar el tipo de documento (el tipo MIME).
* El encabezado de autenticación está establecido (esto se puede configurar)

>[!NOTE]
>
>El despachante puede almacenar en caché los métodos GET o HEAD (para el encabezado HTTP). Para obtener información adicional sobre la caché de encabezados de respuesta, consulte la sección Encabezados [de respuesta HTTP en](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html) caché.

### Determinación de si un documento está en caché

Dispatcher almacena los archivos en caché en el servidor web como si formaran parte de un sitio web estático. Si un usuario solicita un documento procesable, Dispatcher comprueba si dicho documento existe en el sistema de archivos del servidor web:

* si el documento está en caché, Dispatcher devuelve el archivo.
* si no se almacena en caché, Dispatcher solicita el documento a la instancia de AEM.

### Determinación de si un documento está actualizado

Para averiguar si un documento está actualizado, el despachante realiza dos pasos:

1. Comprueba si el documento está sujeto a invalidación automática. En caso contrario, el documento se considera actualizado.
1. Si el documento está configurado para la invalidación automática, Dispatcher comprueba si es anterior o posterior al último cambio disponible. Si es anterior, Dispatcher solicita la versión actual desde la instancia de AEM y reemplaza la versión en la caché.

>[!NOTE]
>
>Los documentos sin invalidación **** automática permanecen en la caché hasta que se eliminan físicamente; Por ejemplo, mediante una actualización de contenido en el sitio web.

## Las ventajas del equilibrio de carga {#the-benefits-of-load-balancing}

Equilibrio de carga es la práctica de distribuir la carga computacional del sitio web en varias instancias de AEM.

![](assets/chlimage_1-7.png)

Usted gana:

* **mayor potencia** de procesamiento En la práctica, esto significa que Dispatcher comparte solicitudes de documento entre varias instancias de AEM. Dado que cada instancia tiene ahora menos documentos para procesar, los tiempos de respuesta son más rápidos. El despachante mantiene estadísticas internas para cada categoría de documento, de modo que puede estimar la carga y distribuir las consultas de forma eficaz.

* **aumento de la cobertura** de seguridad contra fallos Si el despachante no recibe respuestas de una instancia, reenviará automáticamente las solicitudes a una de las otras instancias. Por lo tanto, si una instancia deja de estar disponible, el único efecto es una desaceleración del sitio, proporcional a la potencia computacional perdida. Sin embargo, todos los servicios continuarán.

* también puede administrar distintos sitios web en el mismo servidor web estático.

>[!NOTE]
>
>Mientras que el equilibrio de carga distribuye la carga de forma eficaz, el almacenamiento en caché ayuda a reducir la carga. Por lo tanto, intente optimizar el almacenamiento en caché y reducir la carga total antes de configurar el equilibrio de carga. Un buen almacenamiento en caché puede aumentar el rendimiento del equilibrador de carga o hacer innecesario el equilibrio de carga.

>[!CAUTION]
>
>Aunque un único despachante normalmente podrá saturar la capacidad de las instancias de publicación disponibles, para algunas aplicaciones excepcionales puede ser recomendable equilibrar la carga entre dos instancias de Dispatcher. Las configuraciones con varios despachantes deben considerarse cuidadosamente, ya que un despachante adicional aumentará la carga en las instancias de publicación disponibles y puede reducir fácilmente el rendimiento en la mayoría de las aplicaciones.

## Cómo realiza el despachante el equilibrio de carga {#how-the-dispatcher-performs-load-balancing}

### Estadísticas de rendimiento

Dispatcher mantiene estadísticas internas sobre la rapidez con la que cada instancia de AEM procesa los documentos. En base a estos datos, Dispatcher calcula qué instancia proporcionará el tiempo de respuesta más rápido al responder una solicitud y, por lo tanto, se reserva el tiempo de cálculo necesario en ese caso.

Los distintos tipos de solicitudes pueden tener diferentes tiempos de finalización promedio, por lo que el despachante permite especificar categorías de documentos. Estos se tienen en cuenta al calcular las estimaciones de tiempo. Por ejemplo, puede hacer una distinción entre páginas HTML e imágenes, ya que los tiempos de respuesta típicos bien pueden diferir.

Si utiliza una función de búsqueda detallada, puede crear una nueva categoría para las consultas de búsqueda. Esto ayuda al despachante a enviar consultas de búsqueda a la instancia que responde con mayor rapidez. Esto evita que una instancia más lenta se detenga cuando recibe varias consultas de búsqueda "costosas", mientras que las demás reciben las solicitudes "más baratas".

### Contenido personalizado (conexiones adhesivas)

Las conexiones fijas garantizan que todos los documentos de un usuario se componen en la misma instancia de AEM. Esto es importante si utiliza páginas personalizadas y datos de sesión. Los datos se almacenan en la instancia, por lo que las solicitudes posteriores del mismo usuario deben volver a esa instancia o se pierden los datos.

Debido a que las conexiones adhesivas restringen la capacidad del despachante para optimizar las solicitudes, debe utilizarlas únicamente cuando sea necesario. Puede especificar la carpeta que contiene los documentos "adhesivos", asegurándose así de que todos los documentos de esa carpeta se componen en la misma instancia para cada usuario.

>[!NOTE]
>
>Para la mayoría de las páginas que utilizan conexiones adhesivas, debe desactivar el almacenamiento en caché; de lo contrario, la página tendrá el mismo aspecto para todos los usuarios, independientemente del contenido de la sesión.
>
>Para *algunas* aplicaciones, puede ser posible utilizar tanto conexiones adhesivas como almacenamiento en caché; por ejemplo, si se muestra un formulario que escribe datos en la sesión.

## Uso de varios despachantes {#using-multiple-dispatchers}

En configuraciones complejas, puede utilizar varios despachantes. Por ejemplo, puede utilizar:

* un despachante para publicar un sitio web en la Intranet
* un segundo despachante, en una dirección diferente y con una configuración de seguridad diferente, para publicar el mismo contenido en Internet.

En ese caso, asegúrese de que cada solicitud pasa por un único despachante. Un despachante no gestiona solicitudes procedentes de otro despachante. Por lo tanto, asegúrese de que ambos despachantes acceden directamente al sitio web de AEM.

## Uso de Dispatcher con una CDN {#using-dispatcher-with-a-cdn}

Una red de entrega de contenido (CDN), como Akamai Edge Delivery o Amazon Cloud Front, ofrece contenido desde una ubicación cercana al usuario final. Por eso

* acelera los tiempos de respuesta para los usuarios finales
* descarga los servidores

Como componente de infraestructura HTTP, una CDN funciona como Dispatcher: cuando un nodo CDN recibe una solicitud, la suministra de su caché si es posible (el recurso está disponible en la caché y es válido). De lo contrario, se dirige al siguiente servidor más cercano para recuperar el recurso y almacenarlo en caché para solicitudes adicionales, si procede.

El "siguiente servidor más cercano" depende de la configuración específica. Por ejemplo, en una configuración de Akamai, la solicitud puede seguir la siguiente ruta:

* El nodo perimetral Akamai
* La capa Akamai Midgres
* Su cortafuegos
* El equilibrador de carga
* Dispatcher
* AEM

En la mayoría de los casos, Dispatcher es el siguiente servidor que puede servir el documento desde una caché e influir en los encabezados de respuesta devueltos al servidor CDN.

## Control de una caché de CDN {#controlling-a-cdn-cache}

Existen varias formas de controlar durante cuánto tiempo un CDN almacenará en caché un recurso antes de que éste se recupere de Dispatcher.

1. Configuración explícita\
   Configure el tiempo durante el que se retienen recursos concretos en la caché de CDN, según el tipo de MIME, la extensión, el tipo de solicitud, etc.

1. Caducidad y encabezados de control de caché\
   La mayoría de las CDN respetarán `Expires:` y `Cache-Control:` los encabezados HTTP si son enviados por el servidor de flujo ascendente. Esto se puede lograr, por ejemplo, utilizando el módulo [mod_expires](https://httpd.apache.org/docs/2.4/mod/mod_expires.html) Apache.

1. Invalidez manual\
   Las CDN permiten que los recursos se eliminen de la caché a través de interfaces web.
1. invalidación basada en API\
   La mayoría de los CDN también ofrecen una API REST y/o SOAP que permite eliminar recursos de la caché.

En una configuración típica de AEM, la configuración por extensión y/o ruta, que se puede lograr a través de los puntos 1 y 2 anteriores, ofrece posibilidades de establecer períodos razonables de almacenamiento en caché para recursos que se utilizan con frecuencia y que no cambian con frecuencia, como imágenes de diseño y bibliotecas de clientes. Cuando se implementan nuevas versiones, generalmente se requiere una invalidación manual.

Si este método se utiliza para almacenar en caché el contenido administrado, implica que los cambios de contenido solo son visibles para los usuarios finales una vez que el período de almacenamiento en caché configurado ha caducado y se recupera el documento de Dispatcher.

Para un control más preciso, la invalidación basada en API permite invalidar la caché de una CDN, ya que la caché de Dispatcher se invalida. En función de la API de CDN, puede implementar su propio [ContentBuilder](https://docs.adobe.com/docs/en/cq/current/javadoc/com/day/cq/replication/ContentBuilder.html) y [TransportHandler](https://docs.adobe.com/docs/en/cq/current/javadoc/com/day/cq/replication/TransportHandler.html) (si la API no está basada en REST) y configurar un agente de replicación que lo usará para invalidar la caché de CDN.

>[!NOTE]
>
>Consulte también Seguridad del despachante de [AEM (CQ) y Almacenamiento en caché](https://www.slideshare.net/andrewmkhoury/dispatcher-caching-aemgemspart2jan2015) de CDN+Explorador y presentación grabada en caché de [Dispatcher](https://docs.adobe.com/content/ddc/en/gems/dispatcher-caching---new-features-and-optimizations.html).

## Uso de Dispatcher con un servidor de creación {#using-a-dispatcher-with-an-author-server}

>[!CAUTION]
>
>si utiliza [AEM con la IU](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/touch-ui-concepts.html) táctil, **no debe** almacenar en caché el contenido de la instancia de creación. Si el almacenamiento en caché estaba habilitado para la instancia de creación, debe deshabilitarlo y eliminar el contenido del directorio de caché. Para desactivar el almacenamiento en caché, debe editar el `author_dispatcher.any` archivo y modificar la `/rule` propiedad de la `/cache` sección de la siguiente manera:

```xml
/rules
{
/0000
{ /type "deny" /glob "*"}
}
```

Un despachante se puede usar delante de una instancia de autor para mejorar el rendimiento de creación. Para configurar un despachante de creación, haga lo siguiente:

1. Instale un Dispatcher en un servidor web (puede ser Apache o IIS, consulte [Instalación de Dispatcher](dispatcher-install.md)).
1. Es posible que desee probar el despachante recién instalado con una instancia de publicación de AEM en funcionamiento para asegurarse de que se ha realizado una instalación correcta de línea de base.
1. Ahora asegúrese de que Dispatcher pueda conectarse mediante TCP/IP a la instancia de autor.
1. Reemplace el archivo de muestra dispatcher.any por el archivo author_dispatcher.any que se proporciona con la descarga [de](release-notes.md#downloads)Dispatcher.
1. Abra el `author_dispatcher.any` en un editor de texto y realice los siguientes cambios:

   1. Cambie el `/hostname` y `/port` de la sección `/renders` para que señale a la instancia de autor.
   1. Cambie el `/docroot` `/cache` de la sección para que apunte a un directorio de caché. Si utiliza [AEM con la IU](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/touch-ui-concepts.html)táctil, consulte la advertencia anterior.
   1. Guarde los cambios.

1. Elimine todos los archivos existentes en el directorio `/cache` &gt; `/docroot` que ha configurado anteriormente.
1. Reinicie el servidor web.

>[!NOTE]
>
>Tenga en cuenta que con la configuración proporcionada `author_dispatcher.any` , al instalar un paquete de código de aplicación, revisión o paquete de paquete de funciones CQ5 que afecte a cualquier contenido debajo `/libs` o `/apps` , debe eliminar los archivos en caché bajo esos directorios en la caché del despachante para asegurarse de que la próxima vez que se soliciten los archivos recién actualizados se recuperen y no los antiguos en caché.

>[!CAUTION]
>
>Si ha utilizado el despachante de autor previamente configurado y ha habilitado un agente *de vaciado de* despachante, haga lo siguiente:

1. Elimine o deshabilite el agente de vaciado del **despachante del** autor en la instancia de creación de AEM.
1. Vuelva a hacer la configuración del despachante de autor siguiendo las nuevas instrucciones anteriores.

<!--
[Author Dispatcher configuration file (Dispatcher 4.1.2 or later)](assets/author_dispatchernew.any)
-->
<!--[!NOTE]
>
>A related knowledge base article can be found here:  
>[How to configure the dispatcher in front of an authoring environment](https://helpx.adobe.com/cq/kb/HowToConfigureDispatcherForAuthoringEnvironment.html)
-->
