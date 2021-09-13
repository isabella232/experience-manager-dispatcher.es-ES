---
title: Optimizar un sitio web para el rendimiento de la caché
seo-title: Optimizar un sitio web para el rendimiento de la caché
description: Aprenda a diseñar un sitio web para maximizar las ventajas del almacenamiento en caché.
seo-description: Dispatcher ofrece una serie de mecanismos integrados que puede utilizar para optimizar el rendimiento. Aprenda a diseñar un sitio web para maximizar las ventajas del almacenamiento en caché.
uuid: 2d4114d1-f464-4e10-b25c-a1b9a9c715d1
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: ba323503-1494-4048-941d-c1d14f2e85b2
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
source-git-commit: 2ca816ac0776d72be651b76ff4f45e0c3ed1450f
workflow-type: ht
source-wordcount: '1167'
ht-degree: 100%

---


# Optimizar un sitio web para el rendimiento de la caché {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher insertado en la documentación de una versión anterior de AEM.

Dispatcher ofrece una serie de mecanismos integrados que puede utilizar para optimizar el rendimiento. Esta sección le explica cómo diseñar su sitio web para maximizar los beneficios del almacenamiento en caché.

>[!NOTE]
>
>Puede ayudarle a recordar que Dispatcher almacena la caché en un servidor web estándar. Esto significa que:
>
>* puede almacenar en caché todo lo que se pueda almacenar como página y solicitar mediante una dirección URL
>* no puede almacenar otros elementos, como encabezados HTTP, cookies, datos de sesión y datos de formularios.
>
>En general, muchas estrategias de almacenamiento en caché implican la selección de buenas direcciones URL y no depender de estos datos adicionales.

## Utilizar una codificación de página coherente {#using-consistent-page-encoding}

Los encabezados de solicitud HTTP no se almacenan en caché, por lo que pueden producirse problemas si almacena información de codificación de páginas en el encabezado. En este caso, cuando Dispatcher sirve una página desde la caché, se utiliza la codificación predeterminada del servidor web para esa página. Existen dos formas de evitar este problema:

* Si solo utiliza una codificación, asegúrese de que la utilizada en el servidor web sea la misma que la predeterminada del sitio web de AEM.
* Utilice una etiqueta `<META>` en la sección HTML `head` para configurar la codificación, como en el siguiente ejemplo:

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## Evitar parámetros de URL {#avoid-url-parameters}

Si es posible, evite los parámetros de URL para las páginas que desee almacenar en caché. Por ejemplo, si tiene una galería de imágenes, la siguiente URL nunca se almacenará en caché (a menos que Dispatcher esté [configurado así](dispatcher-configuration.md#main-pars_title_24)):

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

Sin embargo, puede colocar estos parámetros en la dirección URL de la página de la siguiente manera:

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Esta URL llama a la misma página y a la misma plantilla que gallery.html. En la definición de la plantilla, puede especificar qué secuencia de comandos procesa la página o puede utilizar la misma secuencia de comandos para todas las páginas.

## Personalizar por dirección URL {#customize-by-url}

Si permite a los usuarios cambiar el tamaño de la fuente (o cualquier otra personalización del diseño), asegúrese de que las diferentes personalizaciones se reflejen en la dirección URL.

Por ejemplo, las cookies no se almacenan en caché, por lo que si almacena el tamaño de la fuente en una cookie (o mecanismo similar), no se conservará para la página en caché. Como resultado, Dispatcher devuelve aleatoriamente documentos con cualquier tamaño de la fuente.

Incluir el tamaño de la fuente en la URL como selector evita este problema:

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>Para la mayoría de los aspectos del diseño, también es posible utilizar hojas de estilo o secuencias de comandos del lado del cliente. Normalmente funcionan muy bien con el almacenamiento en caché.
>
>Esto también es útil en la versión impresa, donde puede usar una URL como: &grave;&grave;
>
>`www.myCompany.com/news/main.print.html`
>
>Utilizando el script globbing de la definición de la plantilla, puede especificar un script separado que procese las páginas imprimidas.

## Invalidar archivos de imagen utilizados como títulos {#invalidating-image-files-used-as-titles}

Si procesa títulos de páginas u otros textos como imágenes, se recomienda almacenar los archivos para que se eliminen tras actualizar el contenido de la página:

1. Coloque el archivo de imagen en la misma carpeta que la página.
1. Utilice el siguiente formato de nomenclatura para el archivo de imagen:

   `<page file name>.<image file name>`

Por ejemplo, puede almacenar el título de la página myPage.html en el archivo myPage.title.gif. Este archivo se eliminará automáticamente si se actualiza la página, por lo que cualquier cambio en el título de la página se reflejará automáticamente en la caché.

>[!NOTE]
>
>El archivo de imagen no existe necesariamente físicamente en la instancia de AEM. Puede utilizar un script que cree dinámicamente el archivo de imagen. A continuación, Dispatcher almacenará el archivo en el servidor web.

## Invalidar archivos de imagen utilizados para la navegación {#invalidating-image-files-used-for-navigation}

Si utiliza imágenes para las entradas de navegación, el método es básicamente el mismo que con los títulos, un poco más complejo. Almacene todas las imágenes de navegación con las páginas de destino. Si utiliza dos imágenes para la normal y la activa, puede utilizar los siguientes scripts:

* Scripts que muestra la página normal.
* Scripts que procesa las solicitudes &quot;.normal&quot; y devuelve la imagen normal.
* Scripts que procesa las solicitudes &quot;.active&quot; y devuelve la imagen activa.

Es importante crear estas imágenes con el mismo nombre que la página, para garantizar que una actualización de contenido elimine estas imágenes, así como la página.

En las páginas que no se modifiquen, las imágenes permanecerán en la caché, aunque las páginas en sí suelen invalidarse automáticamente.

## Personalización {#personalization}

Dispatcher no puede almacenar en caché los datos personalizados, por lo que se recomienda limitar la personalización a donde sea necesario. Para explicar por qué:

* Si utiliza una página de inicio personalizable libremente, esa página deberá estar compuesta cada vez que un usuario la solicite.
* Si, por el contrario, ofrece una opción de 10 páginas de inicio diferentes, puede almacenar en caché cada una de ellas, mejorando el rendimiento.

>[!NOTE]
>
>Si personaliza cada página (por ejemplo, colocando el nombre del usuario en la barra de título), no puede almacenarla en caché, lo que afectar de manera importante al rendimiento.
>
>Sin embargo, si tiene que hacer esto, puede:
>
>* utilizar iFrames para dividir la página en una parte que sea la misma para todos los usuarios y otra que sea la misma para todas las páginas del usuario. A continuación, puede almacenar en caché ambas partes.
>* utilice JavaScript del lado del cliente para mostrar información personalizada. Sin embargo, debe asegurarse de que la página se muestre correctamente si un usuario desactiva JavaScript.
>



## Conexiones fijas {#sticky-connections}

[Las conexiones fijas](dispatcher.md#TheBenefitsofLoadBalancing) garantizan que todos los documentos de un usuario se compongan en el mismo servidor. Si un usuario abandona esta carpeta y más tarde vuelve a ella, la conexión se mantiene. Defina una carpeta para guardar todos los documentos que requieran conexiones fijas para el sitio web. Intente no meter otros documentos en ella. Esto es importante si utiliza páginas personalizadas y datos de sesión.

## Tipos MIME {#mime-types}

Existen dos maneras en las que un explorador puede determinar el tipo de archivo:

1. Por su extensión (p. ej. .html, .gif, .jpg, etc.)
1. Por el tipo MIME que el servidor envía con el archivo.

Para la mayoría de los archivos, el tipo MIME está implícito en la extensión del archivo. es decir:

1. Por su extensión (p. ej. .html, .gif, .jpg, etc.)
1. Por el tipo MIME que el servidor envía con el archivo.

Si el nombre del archivo no tiene extensión, se mostrará como texto sin formato.

El tipo MIME forma parte del encabezado HTTP y, como tal, Dispatcher no lo almacena en caché. Si la aplicación de AEM devuelve archivos que no tienen un final de archivo reconocido, pero dependen del tipo MIME, estos archivos podrían mostrarse incorrectamente.

Para asegurarse de que los archivos se almacenan en caché correctamente, siga estas directrices:

* Asegúrese de que los archivos siempre tengan la extensión adecuada.
* Evite los scripts genéricos del servidor de archivos, que tienen direcciones URL como download.jsp?file=2214. Vuelva a escribir el script para utilizar las direcciones URL que contengan la especificación del archivo; para el ejemplo anterior esto sería download.2214.pdf.

