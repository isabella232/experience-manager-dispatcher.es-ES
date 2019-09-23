---
title: Optimización de un sitio web para el rendimiento de la caché
seo-title: Optimización de un sitio web para el rendimiento de la caché
description: Descubra cómo diseñar su sitio Web para maximizar los beneficios del almacenamiento en caché.
seo-description: Dispatcher ofrece una serie de mecanismos integrados que puede utilizar para optimizar el rendimiento. Descubra cómo diseñar su sitio Web para maximizar los beneficios del almacenamiento en caché.
uuid: 2d4114d1-f464-4e10-b25c-a1b9a9c715d1
contentOwner: Usuario
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referencia
discoiquuid: ba323503-1494-4048-941d-c1d14f2e85b2
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: 2ca816ac0776d72be651b76ff4f45e0c3ed1450f

---


# Optimización de un sitio web para el rendimiento de la caché {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher incrustada en la documentación de una versión anterior de AEM.

Dispatcher ofrece una serie de mecanismos integrados que puede utilizar para optimizar el rendimiento. Esta sección le explica cómo diseñar su sitio Web para maximizar los beneficios del almacenamiento en caché.

>[!NOTE]
>
>Puede ayudarle a recordar que Dispatcher almacena la caché en un servidor web estándar. Esto significa que:
>
>* puede almacenar en caché todo lo que pueda almacenar como página y solicitar mediante una URL
>* no puede almacenar otros elementos, como encabezados HTTP, cookies, datos de sesión y datos de formulario.
>
>
En general, muchas estrategias de almacenamiento en caché implican seleccionar buenas direcciones URL y no depender de estos datos adicionales.

## Uso de la codificación de página coherente {#using-consistent-page-encoding}

HTTP request headers are not cached and so problems can occur if you store page encoding information in the header. En este caso, cuando Dispatcher envía una página desde la caché, se utiliza la codificación predeterminada del servidor web para la página. There are two ways to avoid this problem:

* Si solo utiliza una codificación, asegúrese de que la codificación utilizada en el servidor web es la misma que la codificación predeterminada del sitio web de AEM.
* Utilice una `<META>` etiqueta en la sección HTML `head` para definir la codificación, como en el ejemplo siguiente:

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## Evitar parámetros de URL {#avoid-url-parameters}

Si es posible, evite los parámetros de URL para las páginas que desee almacenar en caché. Por ejemplo, si tiene una galería de imágenes, la siguiente dirección URL nunca se almacena en caché (a menos que Dispatcher se [configure en consecuencia](dispatcher-configuration.md#main-pars_title_24)):

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

Sin embargo, puede colocar estos parámetros en la dirección URL de la página, como se indica a continuación:

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Esta URL llama a la misma página y a la misma plantilla que Gallery.html. En la definición de plantilla, puede especificar qué secuencia de comandos procesa la página o puede utilizar la misma secuencia de comandos para todas las páginas.

## Personalizar por dirección URL {#customize-by-url}

Si permite que los usuarios cambien el tamaño de fuente (o cualquier otra personalización del diseño), asegúrese de que las diferentes personalizaciones se reflejan en la dirección URL.

For example, cookies are not cached, so if you store the font size in a cookie (or similar mechanism), the font size is not preserved for the cached page. As a result, Dispatcher returns documents of any font size at random.

Si se incluye el tamaño de fuente en la URL como selector, se evita este problema:

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>En la mayoría de los aspectos del diseño, también es posible utilizar hojas de estilo y/o secuencias de comandos del lado del cliente. Normalmente funcionan muy bien con el almacenamiento en caché.
>
>Esto también es útil para una versión de impresión, donde puede utilizar una URL como: "
>
>`www.myCompany.com/news/main.print.html`
>
>Al utilizar la secuencia de comandos de la definición de plantilla, puede especificar una secuencia de comandos independiente que procese las páginas de impresión.

## Invalidación De Archivos De Imagen Utilizados Como Títulos {#invalidating-image-files-used-as-titles}

Si procesa títulos de página u otro texto como imágenes, se recomienda almacenar los archivos para que se eliminen al actualizar el contenido de la página:

1. Coloque el archivo de imagen en la misma carpeta que la página.
1. Utilice el siguiente formato de nombre para el archivo de imagen:

   `<page file name>.<image file name>`

Por ejemplo, puede almacenar el título de la página myPage.html en el archivo myPage.title.gif. Este archivo se elimina automáticamente si se actualiza la página, por lo que cualquier cambio en el título de la página se refleja automáticamente en la caché.

>[!NOTE]
>
>El archivo de imagen no existe necesariamente físicamente en la instancia de AEM. Puede utilizar una secuencia de comandos que cree dinámicamente el archivo de imagen. A continuación, Dispatcher almacena el archivo en el servidor web.

## Invalidación De Archivos De Imagen Utilizados Para La Navegación {#invalidating-image-files-used-for-navigation}

Si utiliza imágenes para las entradas de navegación, el método es básicamente el mismo que con los títulos, un poco más complejo. Almacene todas las imágenes de navegación con las páginas de destino. Si utiliza dos imágenes para normal y activo, puede utilizar los siguientes scripts:

* Secuencia de comandos que muestra la página de forma normal.
* Una secuencia de comandos que procesa solicitudes ".normal" y devuelve la imagen normal.
* Una secuencia de comandos que procesa solicitudes ".active" y devuelve la imagen activada.

Es importante crear estas imágenes con el mismo nombre que la página para asegurarse de que una actualización de contenido elimina estas imágenes, así como la página.

En el caso de las páginas que no se modifican, las imágenes permanecen en la caché, aunque las páginas mismas suelen invalidarse automáticamente.

## Personalización {#personalization}

El despachante no puede almacenar en caché datos personalizados, por lo que se recomienda limitar la personalización a donde sea necesaria. Para ilustrar por qué:

* Si utiliza una página de inicio personalizable libremente, esa página debe estar compuesta cada vez que un usuario la solicite.
* Si, por el contrario, ofrece una selección de 10 páginas de inicio diferentes, puede almacenar en caché cada una de ellas, lo que mejora el rendimiento.

>[!NOTE]
>
>Si personaliza cada página (por ejemplo, colocando el nombre del usuario en la barra de título), no podrá almacenarla en caché, lo que puede causar un gran impacto en el rendimiento.
>
>Sin embargo, si tiene que hacer esto, puede:
>
>* utilice iFrames para dividir la página en una parte que sea la misma para todos los usuarios y una parte que sea la misma para todas las páginas del usuario. A continuación, puede almacenar en caché ambas partes.
>* utilice JavaScript del lado del cliente para mostrar información personalizada. Sin embargo, debe asegurarse de que la página se muestra correctamente si un usuario desactiva JavaScript.
>



## Conexiones adhesivas {#sticky-connections}

[Las conexiones](dispatcher.md#TheBenefitsofLoadBalancing) adhesivas garantizan que los documentos de un usuario están compuestos en el mismo servidor. Si un usuario abandona esta carpeta y luego regresa a ella, la conexión se mantendrá. Defina una carpeta para guardar todos los documentos que requieran conexiones adhesivas para el sitio web. Trata de no tener otros documentos. Esto afecta al equilibrio de carga si utiliza páginas personalizadas y datos de sesión.

## MIME Types {#mime-types}

Existen dos maneras en que un explorador puede determinar el tipo de archivo:

1. Por su extensión (p. ej. .html, .gif, .jpg, etc.)
1. Por el tipo MIME que el servidor envía con el archivo.

Para la mayoría de los archivos, el tipo MIME está implícito en la extensión del archivo. es decir:

1. Por su extensión (p. ej. .html, .gif, .jpg, etc.)
1. Por el tipo MIME que el servidor envía con el archivo.

Si el nombre del archivo no tiene extensión, se muestra como texto sin formato.

El tipo MIME forma parte del encabezado HTTP y, como tal, Dispatcher no lo almacena en la caché. Si la aplicación de AEM devuelve archivos que no tienen un final de archivo reconocido, pero dependen del tipo MIME en su lugar, estos archivos pueden mostrarse incorrectamente.

Para asegurarse de que los archivos se almacenan correctamente en la caché, siga estas directrices:

* Asegúrese de que los archivos siempre tengan la extensión adecuada.
* Evite los scripts genéricos del servidor de archivos, que tienen direcciones URL como download.jsp?file=2214. Vuelva a escribir la secuencia de comandos para utilizar las direcciones URL que contengan la especificación del archivo; para el ejemplo anterior, sería download.2214.pdf.

