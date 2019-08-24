---
title: Información general de Dispatcher
seo-title: Descripción general de Dispatcher de Adobe AEM
description: Este artículo proporciona una descripción general general de Dispatcher.
seo-description: Este artículo proporciona una descripción general general de Adobe Experience Manager Dispatcher.
uuid: 71766 f 86-5 e 91-446 b-a 078-061 b 179 d 090 d
pageversionid: '1193211344162'
topic-tags: dispatcher
content-type: referencia
discoiquuid: 1 d 449 ee 2-4 cdd -4 b 7 a -8 b 4 e -7 e 6 fc 0 a 1 d 7 ee
translation-type: tm+mt
source-git-commit: de6a513baf3e6b1a1463a442fa840e59f2196e8e

---


# Información general de Dispatcher {#dispatcher-overview}

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher incrustada en la documentación de una versión anterior de AEM.

Dispatcher es la herramienta de equilibrio de carga o almacenamiento en caché de Adobe Experience Manager. El uso de Dispatcher de AEM también ayuda a proteger su servidor AEM desde el ataque. Por lo tanto, puede aumentar la seguridad de su instancia de AEM utilizando Dispatcher junto con un servidor web de clase empresarial.

El proceso para implementar un despachante es independiente del servidor web y de la plataforma del sistema operativo elegida:

1. Obtenga información sobre Dispatcher (esta página). Consulte [también las preguntas más frecuentes sobre dispatcher](https://helpx.adobe.com/experience-manager/using/dispatcher-faq.html).
1. Instale un [servidor web compatible](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html) según la documentación del servidor web.

1. [Instale el módulo Dispatcher](dispatcher-install.md) en el servidor web y configure el servidor web en consecuencia.
1. [Configure Dispatcher](dispatcher-configuration.md) (el archivo dispatcher. any).

1. [Configure AEM](page-invalidate.md) para que las actualizaciones de contenido invaliden la caché.

>[!NOTE]
>
>Para obtener una mejor desvinculación de cómo funciona Dispatcher con AEM, consulte [Consulte a los expertos de la comunidad de AEM para julio de 2017](https://bit.ly/ATACE0717).

Utilice la siguiente información según sea necesario:

* [Lista Comprobación de seguridad de Dispatcher](security-checklist.md)
* [Base de conocimientos de Dispatcher](https://helpx.adobe.com/cq/kb/index/dispatcher.html)
* [Optimización de un sitio web para el rendimiento de caché](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html)
* [Uso de Dispatcher con varios dominios](dispatcher-domains.md)
* [Uso de SSL con Dispatcher](dispatcher-ssl.md)
* [Implementación de caché sensible a permisos](permissions-cache.md)
* [Solución de problemas de Dispatcher](dispatcher-troubleshooting.md)
* [Preguntas frecuentes sobre los problemas principales de Dispatcher](dispatcher-faq.md)

>[!NOTE]
>
>**El uso más común de Dispatcher** es la caché de las respuestas desde una instancia **** de publicación de AEM, para aumentar la capacidad de respuesta y seguridad del sitio web publicado externamente. La mayoría de los debates se centra en este caso.
>
>Sin embargo, Dispatcher también puede utilizarse para aumentar la respuesta de su instancia **de autor**, especialmente si tiene un gran número de usuarios editando y actualizando su sitio web. Para obtener más detalles sobre este caso, consulte [Uso de un Dispatcher con un servidor](#using-a-dispatcher-with-an-author-server)de autores, a continuación.

## ¿Por qué utilizar Dispatcher para implementar el almacenamiento en caché? {#why-use-dispatcher-to-implement-caching}

Existen dos métodos básicos para la publicación Web:

* **Servidores Web estáticos**: como Apache o IIS, son muy simples pero rápidos.
* **Servidores de administración de contenido**: que proporcionan contenido inteligente, en tiempo real y inteligente, pero requieren mucho más tiempo de computación y otros recursos.

Dispatcher ayuda a realizar un entorno que es rápido y dinámico. Funciona como parte de un servidor HTML estático, como Apache, con el objetivo de:

* almacenar (o «almacenar en caché») todo el contenido del sitio, como es posible, en forma de sitio web estático
* acceder al motor de diseño lo menos posible.

Lo que significa que:

* **el contenido** estático se administra con exactamente la misma velocidad y facilidad que en un servidor Web estático;*Además, puede utilizar las herramientas de administración y seguridad disponibles para los servidores web estáticos*.

* **El contenido** dinámico se genera según sea necesario, sin ralentizar el sistema más que absolutamente necesario.

Dispatcher contiene mecanismos para generar y actualizar HTML estático según el contenido del sitio dinámico. Puede especificar en detalle qué documentos se almacenan como archivos estáticos y cuáles siempre se generan dinámicamente.

Esta sección ilustra los principios que hay detrás de esto.

### Servidor web estático {#static-web-server}

![](assets/chlimage_1-3.png)

Un servidor web estático, como Apache o IIS, proporciona archivos HTML estáticos a los visitantes del sitio web. Las páginas estáticas se crean una vez, por lo que se enviará el mismo contenido para cada solicitud.

Este proceso es muy sencillo y, por lo tanto, extremadamente eficiente. Si un visitante solicita un archivo (p. ej. una página HTML), el archivo se suele tomar directamente de la memoria, en el peor de los casos se lee desde la unidad local. Los servidores Web estáticos han estado disponibles durante bastante tiempo, por lo que existe una gran variedad de herramientas para administrar y administrar la seguridad, y están muy bien integrados con las infraestructuras de red.

### Servidores de administración de contenido {#content-management-servers}

![](assets/chlimage_1-4.png)

Si utiliza un servidor de administración de contenido, como AEM, un motor de diseño avanzado procesa la solicitud de un visitante. El motor lee el contenido de un repositorio que, combinado con estilos, formatos y derechos de acceso, transforma el contenido en un documento adaptado a las necesidades y los derechos del visitante.

Esto le permite crear contenido dinámico enriquecido, que aumenta la flexibilidad y funcionalidad del sitio web. Sin embargo, el motor de diseño requiere más capacidad de procesamiento que un servidor estático, por lo que esta configuración puede ser susceptible de ralentización si muchos visitantes utilizan el sistema.

## Cómo realiza Dispatcher el almacenamiento en caché {#how-dispatcher-performs-caching}

![](assets/chlimage_1-5.png)

**El directorio de caché** para almacenamiento en caché, el módulo Dispatcher utiliza la capacidad del servidor web para proporcionar contenido estático. Dispatcher coloca los documentos en caché en la raíz del documento del servidor Web.

>[!NOTE]
>
>Si se carece de la configuración para el almacenamiento en caché de encabezado HTTP, Dispatcher almacena solo el código HTML de la página, no almacena los encabezados HTTP. Puede ser un problema si utiliza diferentes codificaciones dentro del sitio web, ya que esto puede perderse. Para habilitar el almacenamiento en caché de encabezado HTTP, consulte [Configuración de la caché de Dispatcher.](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html)

>[!NOTE]
>
>La localización de la raíz del documento del servidor Web en almacenamiento conectado a red (NAS) provoca degradación del rendimiento. Además, cuando una raíz de documento ubicada en NAS se comparte entre varios servidores Web, los bloqueos intermitentes pueden ocurrir cuando se realizan acciones de replicación.

>[!NOTE]
>
>Dispatcher almacena el documento almacenado en caché en una estructura igual a la URL solicitada.
>
>Puede haber limitaciones de nivel de sistema operativo para la longitud del nombre del archivo; Es decir, si tiene una URL con muchos selectores.

### Métodos de almacenamiento en caché

Dispatcher tiene dos métodos principales para actualizar el contenido de la caché cuando se realizan cambios en el sitio web.

* **Las actualizaciones** de contenido eliminan las páginas que han cambiado, así como los archivos directamente asociados con ellos.
* **Invalidación automática** invalida automáticamente las partes de la caché que pueden quedar obsoletas después de una actualización. Es decir, marca de forma eficaz las páginas relevantes como obsoletas, sin eliminar nada.

### Actualizaciones de contenido

En una actualización de contenido, se cambian uno o varios documentos AEM. AEM envía una solicitud de distribución a Dispatcher, que actualiza la caché en consecuencia:

1. Elimina los archivos modificados de la caché.
1. Elimina todos los archivos que comienzan con el mismo control desde la caché. Por ejemplo, si se actualiza el archivo /en/index.html, todos los archivos que empiecen por /en/index. se eliminan. Este mecanismo le permite diseñar sitios en caché, especialmente en lo que respecta a las exploraciones de imágenes.
1. *Toca* el llamado **statfile**; esto actualiza la marca de tiempo del archivo para indicar la fecha del último cambio.

Se deben anotar los siguientes puntos:

* Las actualizaciones de contenido se suelen utilizar junto con un sistema de creación que «conoce» lo que se debe reemplazar.
* Los archivos afectados por una actualización de contenido se eliminan, pero no se sustituyen inmediatamente. La próxima vez que se solicite un archivo, Dispatcher obtiene el nuevo archivo de la instancia de AEM y lo coloca en la caché, sobrescribiendo el contenido antiguo.
* Normalmente, las imágenes generadas automáticamente que incorporan texto desde una página se almacenan en archivos de imagen que comienzan con el mismo control, asegurándose de que la asociación existe para eliminarse. Por ejemplo, puede almacenar el texto del título de la página mypage.html como imagen mypage. titlepicture. gif en la misma carpeta. De esta forma, la imagen se elimina de forma automática de la caché cada vez que se actualiza la página, de modo que puede estar seguro de que la imagen siempre refleja la versión actual de la página.
* Puede tener varios estados, por ejemplo uno por carpeta de idioma. Si se actualiza una página, AEM busca la siguiente carpeta principal que contiene un archivo y *toca* dicho archivo.

### Invalidación automática

La invalidación automática invalida automáticamente las partes de la caché, sin eliminar físicamente ningún archivo. En cada actualización de contenido, se toca el llamado statfile, por lo que su marca de tiempo refleja la última actualización de contenido.

Dispatcher tiene una lista de archivos sujetos a invalidación automática. Cuando se solicita un documento de dicha lista, Dispatcher compara la fecha del documento en caché con la marca de hora del archivo:

* si el documento en caché es más reciente, Dispatcher la devuelve.
* Si es anterior, Dispatcher recupera la versión actual de la instancia de AEM.

Nuevamente, debe indicarse algunos puntos:

* La invalidación automática suele usarse cuando las interrelaciones son complejas, por ejemplo, para páginas HTML. Estas páginas contienen vínculos y entradas de navegación, por lo que suelen actualizarse después de una actualización de contenido. Si ha generado automáticamente archivos PDF o imágenes, también puede optar por invalidarlos automáticamente.
* La invalidación automática no implica ninguna acción por parte del despachante en el momento de la actualización, excepto para tocar el archivo. Sin embargo, al tocar el archivo automáticamente se procesa el contenido caché obsoleto, sin quitarlo físicamente de la caché.

## Cómo devuelve Dispatcher los documentos {#how-dispatcher-returns-documents}

![](assets/chlimage_1-6.png)

### Determinar si un documento está sujeto al almacenamiento en caché

[Puede definir qué documentos se almacenan en cachés en el archivo de configuración](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html). Dispatcher comprueba la solicitud en la lista de documentos almacenables. Si el documento no está en esta lista, Dispatcher solicita el documento desde la instancia de AEM.

Dispatcher *siempre solicita* el documento directamente desde la instancia de AEM en los siguientes casos:

* Si el URI de la solicitud contiene un signo de interrogación "?". Esto suele indicar una página dinámica, como un resultado de búsqueda, que no es necesario almacenar en caché.
* Falta la extensión del archivo. El servidor web necesita la extensión para determinar el tipo de documento (el tipo MIME).
* Se establece el encabezado de autenticación (esto puede configurarse)

>[!NOTE]
>
>Los métodos GET o HEAD (para los encabezados HTTP) son almacenables por Dispatcher. Para obtener más información sobre el almacenamiento en caché de encabezados de respuesta, consulte [la sección Caché de encabezados de respuesta](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html) HTTP.

### Determinación de si un documento se almacena en caché

Dispatcher almacena los archivos almacenados en caché en el servidor web como si formaran parte de un sitio web estático. Si un usuario solicita un documento almacenable, Dispatcher comprueba si existe ese documento en el sistema de archivos del servidor web:

* Si el documento se almacena en caché, Dispatcher devuelve el archivo.
* si no se almacena en caché, Dispatcher solicita el documento desde la instancia de AEM.

### Determinar si un documento está actualizado

Para averiguar si un documento está actualizado, Dispatcher realiza dos pasos:

1. Comprueba si el documento está sujeto a invalidación automática. Si no es así, el documento se considera actualizado.
1. Si el documento está configurado para invalidación automática, Dispatcher comprueba si es más antiguo o más nuevo que el último cambio disponible. Si es anterior, Dispatcher solicita la versión actual de la instancia de AEM y reemplaza la versión en la caché.

>[!NOTE]
>
>Los documentos sin **invalidación automática** permanecen en la caché hasta que se eliminan físicamente; Por ejemplo, mediante una actualización de contenido del sitio Web.

## Beneficios del equilibrio de carga {#the-benefits-of-load-balancing}

Equilibrio de cargas es la práctica de distribuir la carga computacional del sitio web en varias instancias de AEM.

![](assets/chlimage_1-7.png)

Obtiene lo siguiente:

* **potencia de procesamiento incrementada** en la práctica esto significa que Dispatcher comparte solicitudes de documento entre varias instancias de AEM. Dado que cada instancia tiene ahora menos documentos para procesar, tiene tiempos de respuesta más rápidos. Dispatcher mantiene las estadísticas internas de cada categoría de documento, de modo que puede calcular la carga y distribuir las consultas de forma eficaz.

* **Aumento de cobertura
segura** Si el despachante no recibe respuestas de una instancia, enviará solicitudes automáticamente a una de las demás instancias. Por lo tanto, si una instancia deja de estar disponible, el único efecto es una ralentización del sitio, proporcional a la potencia informática perdida. Sin embargo, todos los servicios continuarán.

* también puede administrar distintos sitios web en el mismo servidor web estático.

>[!NOTE]
>
>Mientras que el equilibrio de carga distribuye la carga de forma eficiente, el almacenamiento en caché ayuda a reducir la carga. Por lo tanto, intente optimizar el almacenamiento en caché y reducir la carga general antes de configurar el equilibrio de carga. Un buen almacenamiento en caché puede aumentar el rendimiento del equilibrador de carga o el equilibrio de carga de procesamiento innecesaria.

>[!CAUTION]
>
>Aunque un único Dispatcher pueda saturar la capacidad de las instancias de publicación disponibles, en algunas aplicaciones poco frecuentes puede tener sentido equilibrar adicionalmente la carga entre dos instancias de Dispatcher. Es preciso tener cuidado con las configuraciones con varios Dispatcher, ya que un despachante adicional aumenta la carga en las instancias de publicación disponibles y puede disminuir fácilmente el rendimiento en la mayoría de las aplicaciones.

## Funcionamiento del despachante de carga de carga {#how-the-dispatcher-performs-load-balancing}

### Estadísticas de rendimiento

Dispatcher mantiene las estadísticas internas sobre la rapidez con la que cada instancia de AEM procesa documentos. En base a estos datos, Dispatcher estima qué instancia proporciona el tiempo de respuesta más rápido al responder a una solicitud, por lo que se reserva el tiempo de computación necesario en esa instancia.

Los distintos tipos de solicitudes pueden tener tiempos de finalización promedio diferentes, por lo que Dispatcher permite especificar las categorías del documento. Se considera luego al calcular las estimaciones de tiempo. Por ejemplo, puede distinguir entre páginas HTML y imágenes, ya que los tiempos de respuesta típicos pueden diferir.

Si utiliza una función de búsqueda elaborada, puede crear una nueva categoría para consultas de búsqueda. Esto ayuda a Dispatcher a enviar consultas de búsqueda a la instancia que responde más rápidamente. Esto evita que una instancia lenta se detenga cuando recibe varias consultas de búsqueda "costosas", mientras que las demás obtienen las solicitudes "más costosas".

### Contenido personalizado (conexiones adhesivas)

Las conexiones adhesivas garantizan que todos los documentos de un usuario se comuniquen en la misma instancia de AEM. Esto es importante si utiliza páginas personalizadas y datos de sesión. Los datos se almacenan en la instancia, por lo que las solicitudes posteriores del mismo usuario deben volver a esa instancia o se pierden los datos.

Puesto que las conexiones adhesivas restringen la capacidad de Dispatcher para optimizar las solicitudes, debe utilizarlas solo cuando sea necesario. Puede especificar la carpeta que contiene los documentos "adhesivos", asegurándose de que todos los documentos de esa carpeta están compuestos en la misma instancia para cada usuario.

>[!NOTE]
>
>Para la mayoría de las páginas que utilizan conexiones adhesivas, debe desactivar el almacenamiento en caché; de lo contrario, la página tiene el mismo aspecto que todos los usuarios, independientemente del contenido de la sesión.
>
>Para *algunas* aplicaciones, puede ser posible utilizar conexiones adhesivas y almacenamiento en caché; por ejemplo, si muestra un formulario que escribe datos en la sesión.

## Uso de varios despachantes {#using-multiple-dispatchers}

En las configuraciones complejas, puede utilizar varios Dispatcher. Por ejemplo, puede utilizar:

* un despachante para publicar un sitio web en la intranet
* un segundo despachante, bajo una dirección diferente y con diferentes configuraciones de seguridad, para publicar el mismo contenido en Internet.

En este caso, asegúrese de que cada solicitud pasa sólo por un despachante. Dispatcher no gestiona las solicitudes procedentes de otro despachante. Por lo tanto, asegúrese de que ambos Dispatcher accedan directamente al sitio Web de AEM.

## Uso de Dispatcher con CDN {#using-dispatcher-with-a-cdn}

Una red de entrega de contenido (CDN), como Akamai Edge Delivery o Amazon Cloud Front, entrega contenido desde una ubicación cercana al usuario final. Por eso

* acelera los tiempos de respuesta para los usuarios finales
* se carga fuera de los servidores

Como componente de infraestructura HTTP, un CDN funciona como Dispatcher: Cuando un nodo CDN recibe una solicitud, sirve la solicitud desde su caché si es posible (el recurso está disponible en la caché y es válido). De lo contrario, llega al siguiente servidor más cercano para recuperar el recurso y la almacena en caché para solicitudes adicionales si es necesario.

El "servidor cercano cercano" depende de la configuración específica. Por ejemplo, en una configuración de Akamai, la solicitud puede llevar a cabo la siguiente ruta:

* El nodo de Akamai Edge
* Capa de medios medios Akamai
* Su cortafuegos
* Su equilibrador de carga
* Dispatcher
* AEM

En la mayoría de los casos, Dispatcher es el siguiente servidor que puede servir al documento desde una caché e influir en los encabezados de respuesta devueltos al servidor CDN.

## Control de caché CDN {#controlling-a-cdn-cache}

Hay un número de formas de controlar cuánto tiempo un CDN almacenará en caché un recurso antes de que vuelva a capturar es desde Dispatcher.

1. Configuración explícita\
   Configurar, cuánto tiempo se llevan determinados recursos en la caché de CDN, en función del tipo mime, la extensión, el tipo de solicitud, etc.

1. Encabezados de caducidad y control de caché\
   La mayoría de las CDN respetarán `Expires:` y `Cache-Control:` encabezan HTTP si el servidor superior lo envía. Esto puede conseguirse por ejemplo utilizando [Mod_ expires](https://httpd.apache.org/docs/2.4/mod/mod_expires.html) Módulo Apache.

1. Invalidación manual\
   Las CDN permiten eliminar recursos de la caché a través de interfaces web.
1. Invalidación basada en API\
   La mayoría de las CDN también ofrece una API de SOAP y REST que permite eliminar recursos de la caché.

En una configuración típica de AEM, la configuración por extensión y/o ruta, que se puede conseguir a través de los puntos 1 y 2 anteriores, ofrece posibilidades de establecer períodos de caché razonables para recursos utilizados con frecuencia que no cambian con frecuencia, como imágenes de diseño y bibliotecas de cliente. Cuando se implementan nuevas versiones, se requiere una invalidación manual.

Si este método se utiliza para almacenar en caché el contenido administrado, implica que los cambios de contenido solo son visibles para los usuarios finales cuando el período de almacenamiento en caché configurado ha caducado y el documento se recopila de nuevo desde Dispatcher.

Para un control más preciso, la invalidación basada en API le permite invalidar la caché de un CDN a medida que se invalida la caché de Dispatcher. En función de la API de CDN, puede implementar su propio [contentbuilder](https://docs.adobe.com/docs/en/cq/current/javadoc/com/day/cq/replication/ContentBuilder.html) y [transporthandler](https://docs.adobe.com/docs/en/cq/current/javadoc/com/day/cq/replication/TransportHandler.html) (si la API no está basada en REST) y configurar un agente de replicación que usará estos parámetros para invalidar la caché de CDN.

>[!NOTE]
>
>Consulte también [AEM (CQ) Dispatcher Security y CDN + Almacenamiento en caché](https://www.slideshare.net/andrewmkhoury/dispatcher-caching-aemgemspart2jan2015) y presentación grabada en [la caché Dispatcher](https://docs.adobe.com/content/ddc/en/gems/dispatcher-caching---new-features-and-optimizations.html).

## Uso de Dispatcher con un servidor de autores {#using-a-dispatcher-with-an-author-server}

>[!CAUTION]
>
>si utiliza [AEM con IU táctil](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/touch-ui-concepts.html) , **no debe almacenar en** caché contenido de instancia de autor. Si el almacenamiento en caché estaba activado para la instancia de autor, debe deshabilitarlo y eliminar el contenido del directorio de caché. Para deshabilitar el almacenamiento en caché, debe editar `author_dispatcher.any` el archivo y modificar `/rule` la propiedad de `/cache` la sección de la siguiente manera:

```xml
/rules
{
/0000
{ /type "deny" /glob "*"}
}
```

Un despachante se puede utilizar delante de una instancia de autor para mejorar el rendimiento de creación. Para configurar una Dispatcher Dispatcher, haga lo siguiente:

1. Instale un Dispatcher en un servidor web (puede ser Apache o servidor web IIS, consulte [Instalación de Dispatcher](dispatcher-install.md)).
1. Puede que desee probar el despachante recién instalado en una instancia de publicación de AEM que funcione para asegurarse de que se ha procesado una instalación correcta de línea de base.
1. Ahora asegúrese de que Dispatcher puede conectarse a través de TCP/IP a su instancia de autor.
1. Reemplace el archivo dispatcher. any de ejemplo por el archivo author_ dispatcher. any proporcionado con la descarga [Dispatcher](release-notes.md#downloads).
1. Abra `author_dispatcher.any` el en un editor de texto y realice los siguientes cambios:

   1. Cambie el `/hostname` y `/port` de `/renders` la sección para que señale a su instancia de autor.
   1. Cambie la `/docroot``/cache` sección para que señale a un directorio de caché. Si utiliza [AEM con IU táctil](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/touch-ui-concepts.html), consulte la advertencia anterior.
   1. Guarde los cambios.

1. Elimine todos los archivos existentes del directorio `/cache` &gt; `/docroot` que ha configurado arriba.
1. Reinicie el servidor Web.

>[!NOTE]
>
>Tenga en cuenta que, con `author_dispatcher.any` la configuración proporcionada, al instalar un paquete de funciones CQ 5, una revisión o un paquete de código de aplicación que afecta a cualquier contenido de `/libs` o `/apps` , a continuación, debe eliminar los archivos almacenados en caché bajo esos directorios en la caché de su despachante para asegurarse de que la próxima vez que se solicitan los archivos recién actualizados se recuperan y no las antiguas caché.

>[!CAUTION]
>
>Si ha utilizado el despachante de autor configurado anteriormente y ha habilitado un agente de vaciado *de Dispatcher* , haga lo siguiente:

1. Elimine o desactive **el agente** de vaciado del autor en su instancia de autor de AEM.
1. Vuelva a realizar la configuración del distribuidor de autor siguiendo las instrucciones anteriores.

<!--
[Author Dispatcher configuration file (Dispatcher 4.1.2 or later)](assets/author_dispatchernew.any)
-->
<!--[!NOTE]
>
>A related knowledge base article can be found here:  
>[How to configure the dispatcher in front of an authoring environment](https://helpx.adobe.com/cq/kb/HowToConfigureDispatcherForAuthoringEnvironment.html)
-->
