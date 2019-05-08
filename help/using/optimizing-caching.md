---
title: Optimización de un sitio web para el rendimiento de caché
seo-title: Optimización de un sitio web para el rendimiento de caché
description: Descubra cómo diseñar su sitio Web para maximizar los beneficios de la caché.
seo-description: Dispatcher ofrece una serie de mecanismos integrados que puede utilizar para optimizar el rendimiento. Descubra cómo diseñar su sitio Web para maximizar los beneficios de la caché.
uuid: 2 d 4114 d 1-f 464-4 e 10-b 25 c-a 1 b 9 a 9 c 715 d 1
contentOwner: Usuario
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referencia
discoiquuid: ba 323503-1494-4048-941 d-c 1 d 14 f 2 e 85 b 2
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Optimización de un sitio web para el rendimiento de caché {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM. Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher incrustada en la documentación de una versión anterior de AEM.

Dispatcher ofrece una serie de mecanismos integrados que puede utilizar para optimizar el rendimiento. Esta sección explica cómo diseñar el sitio Web para maximizar los beneficios de la caché.

>[!NOTE]
>
>Puede ayudarle a recordar que Dispatcher almacena la caché en un servidor web estándar. Esto significa que:
>
>* puede almacenar en caché todo lo que puede almacenar como página y solicitud mediante una URL
>* no puede almacenar otros elementos, como encabezados HTTP, cookies, datos de sesiones y datos de formulario.
>
>
En general, muchas estrategias de caché permiten seleccionar direcciones URL correctas y no confiar en estos datos adicionales.

## Uso de la codificación de página coherente {#using-consistent-page-encoding}

Los encabezados de solicitud HTTP no se almacenan en caché, por lo que se pueden producir problemas si se almacena información de codificación de página en el encabezado. En este caso, cuando Dispatcher proporciona una página desde la caché, se utiliza la codificación predeterminada del servidor web para la página. Existen dos maneras de evitar este problema:

* Si utiliza una sola codificación, asegúrese de que la codificación utilizada en el servidor web es la misma que la codificación predeterminada del sitio web de AEM.
* Utilice una `<META>` etiqueta en `head` la sección HTML para definir la codificación, como en el ejemplo siguiente:

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## Evitar parámetros de URL {#avoid-url-parameters}

Si es posible, evite los parámetros de URL de las páginas que desee almacenar en caché. Por ejemplo, si tiene una galería de imágenes, la siguiente URL nunca se almacena en caché (a menos que Dispatcher esté [configurada en consecuencia](dispatcher-configuration.md#main-pars_title_24)):

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

Sin embargo, puede colocar estos parámetros en la dirección URL de la página, de la siguiente manera:

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Esta URL llama a la misma página y a la misma plantilla que gallery. html. En la definición de la plantilla, puede especificar qué secuencia de comandos procesa la página o puede utilizar la misma secuencia de comandos para todas las páginas.

## Personalizar por dirección URL {#customize-by-url}

Si permite que los usuarios cambien el tamaño de la fuente (o cualquier otra personalización de diseño), asegúrese de que las distintas personalizaciones se reflejan en la dirección URL.

Por ejemplo, las cookies no se almacenan en caché, por lo que si almacena el tamaño de fuente en una cookie (o mecanismo similar), el tamaño de fuente no se conserva para la página en caché. Como resultado, Dispatcher devuelve documentos de cualquier tamaño de fuente al azar.

Al incluir el tamaño de fuente en la URL como selector, se evita este problema:

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>Para la mayoría de los aspectos de diseño, también es posible utilizar las hojas de estilo o las secuencias de comandos del lado del cliente. Normalmente funcionan muy bien con el almacenamiento en caché.
>
>Esto también es útil para una versión impresa, donde puede utilizar una URL como: «
>
>`www.myCompany.com/news/main.print.html`
>
>Mediante la globalización de secuencias de comandos de la definición de plantilla, puede especificar una secuencia de comandos independiente que represente las páginas de impresión.

## Invalidar archivos de imagen utilizados como títulos {#invalidating-image-files-used-as-titles}

Si procesa títulos de páginas u otro texto como imágenes, se recomienda almacenar los archivos para que se eliminen tras una actualización de contenido en la página:

1. Coloque el archivo de imagen en la misma carpeta que la página.
1. Utilice el siguiente formato de nombre para el archivo de imagen:

   `<page file name>.<image file name>`

Por ejemplo, puede almacenar el título de la página myPage.html en el archivo mypage. title. gif. Este archivo se elimina automáticamente si se actualiza la página, por lo que cualquier cambio en el título de página se refleja automáticamente en la caché.

>[!NOTE]
>
>El archivo de imagen no existe necesariamente en la instancia de AEM. Puede utilizar una secuencia de comandos que cree dinámicamente el archivo de imagen. Dispatcher almacena el archivo en el servidor Web.

## Invalidar archivos de imagen utilizados para navegación {#invalidating-image-files-used-for-navigation}

Si utiliza imágenes para las entradas de navegación, el método es básicamente el mismo que con los títulos, simplemente es algo más complejo. Almacene todas las imágenes de navegación con las páginas de destino. Si utiliza dos imágenes para normal y activo, puede utilizar las siguientes secuencias de comandos:

* Una secuencia de comandos que muestra la página como normal.
* Secuencia de comandos que procesa solicitudes &quot;. normal&quot; y devuelve la imagen normal.
* Secuencia de comandos que procesa solicitudes &quot;. active&quot; y devuelve la imagen activa.

Es importante crear estas imágenes con el mismo tirador de nombres que la página para garantizar que una actualización de contenido elimine estas imágenes así como la página.

Para las páginas que no se modifican, las imágenes permanecen en la caché, aunque las propias páginas suelen invalidarse automáticamente.

## Personalización {#personalization}

Dispatcher no puede almacenar en caché datos personalizados, por lo que se recomienda limitar la personalización donde sea necesario. Para ilustrar por qué:

* Si utiliza una página de inicio personalizable libremente, esa página debe componerse cada vez que un usuario lo solicite.
* Por el contrario, si ofrece una opción de 10 páginas de inicio diferentes, puede almacenar en caché cada una de ellas, mejorando así el rendimiento.

>[!NOTE]
>
>Si personaliza cada página (por ejemplo, si coloca el nombre del usuario en la barra de título), no podrá almacenarlo en caché, lo que puede provocar un gran impacto en el rendimiento.
>
>Sin embargo, si tiene que hacerlo, puede:
>
>* utilice iframes para dividir la página en una parte que sea la misma para todos los usuarios y una parte que sea igual para todas las páginas del usuario. A continuación, puede almacenar en caché ambas partes.
>* utilice JavaScript del lado del cliente para mostrar información personalizada. Sin embargo, debe asegurarse de que la página se muestre correctamente si un usuario desactiva JavaScript.
>



## Conexiones adhesivas {#sticky-connections}

[Las conexiones adhesivas](dispatcher.md#TheBenefitsofLoadBalancing) garantizan que los documentos de un usuario se comuniquen en el mismo servidor. Si un usuario deja esta carpeta y después vuelve a ella, la conexión sigue fija. Defina una carpeta para incluir todos los documentos que requieran conexiones adhesivas para el sitio web. Intente no tener otros documentos. Esto afecta al equilibrio de cargas si utiliza páginas personalizadas y datos de sesión.

## Tipos MIME {#mime-types}

Hay dos maneras en que un navegador puede determinar el tipo de archivo:

1. Por su extensión (por ejemplo:.html. gif.jpg, etc.)
1. Mediante el tipo MIME que el servidor envía con el archivo.

Para la mayoría de los archivos, el tipo MIME está implícito en la extensión de archivo. es decir:

1. Por su extensión (por ejemplo:.html. gif.jpg, etc.)
1. Mediante el tipo MIME que el servidor envía con el archivo.

Si el nombre del archivo no tiene extensión, se muestra como texto sin formato.

El tipo MIME forma parte del encabezado HTTP y, como tal, Dispatcher no la almacena en caché. Si la aplicación AEM devuelve archivos que no tienen un archivo reconocido, pero que dependen del tipo MIME, puede que estos archivos se muestren incorrectamente.

Para asegurarse de que los archivos se almacenan en caché correctamente, siga estas instrucciones:

* Asegúrese de que los archivos siempre tengan la extensión adecuada.
* Evite las secuencias de comandos genéricas de servicio de archivos, que tienen direcciones URL como download. jsp? archivo = 2214. Vuelva a escribir la secuencia de comandos para utilizar direcciones URL que contengan la especificación del archivo; para el ejemplo anterior, se descarga .2214. pdf.

