---
title: Solución de problemas de Dispatcher
seo-title: Solución de problemas relacionados con el despachante de AEM
description: Aprenda a solucionar problemas de Dispatcher.
seo-description: Aprenda a solucionar problemas de Dispatcher de AEM.
uuid: 9 c 109 a 48-d 921-4 b 6 e -9626-1158 cebc 41 e 7
cmgrlastmodified: 01.11.2007 08 22 29 [ahedisoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: Usuario
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referencia
discoiquuid: a 612 e 745-f 1 e 6-43 de-b 25 a -9 adcaadab 5 cf
translation-type: tm+mt
source-git-commit: 76cffbfb616cd5601aed36b7076f67a2faf3ed3b

---


# Solución de problemas de Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM, pero la documentación de Dispatcher está incrustada en la documentación de AEM. Utilice siempre la documentación de Dispatcher incrustada en la documentación para obtener la versión más reciente de AEM.
>
>Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher incrustada en la documentación de una versión anterior de AEM.

>[!NOTE]
>
>Consulte también la [base de conocimientos de Dispatcher](https://helpx.adobe.com/cq/kb/index/dispatcher.html), [Solución de problemas de vaciado de Dispatcher y](https://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) preguntas frecuentes sobre los problemas principales [de Dispatcher](dispatcher-faq.md) para obtener más información.

## Comprobación de la configuración básica {#check-the-basic-configuration}

Como siempre, los primeros pasos son verificar los conceptos básicos:

* [Confirmar operación básica](#ConfirmBasicOperation)
* Compruebe todos los archivos de registro del servidor web y del despachante. Si es necesario, aumente la `loglevel` utilizada para [el registro de Dispatcher](#Logging).

* [Compruebe su configuración](#ConfiguringtheDispatcher):

   * ¿Tiene varios Dispatcher?

      * ¿Ha determinado qué Dispatcher está administrando el sitio web o la página que está investigando?
   * ¿Ha implementado filtros?

      * ¿Esto afecta al asunto que está investigando?


## Herramientas de diagnóstico IIS {#iis-diagnostic-tools}

IIS proporciona varias herramientas de rastreo, según la versión actual:

* IIS 6 - Las herramientas de diagnóstico IIS se pueden descargar y configurar
* IIS 7 - El rastreo está completamente integrado

Esto puede ayudarle a supervisar la actividad.

## IIS y 404 no encontrado {#iis-and-not-found}

Al utilizar IIS, puede que se le `404 Not Found` devuelva en distintos escenarios. Si es así, consulte los siguientes artículos de la Base de conocimiento.

* [IIS 6/7: El método POST devuelve 404](https://helpx.adobe.com/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS 6: Solicitudes a direcciones URL que contienen la ruta `/bin` base `404 Not Found`](https://helpx.adobe.com/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

También debe comprobar que la raíz de la caché de Dispatcher y la raíz del documento IIS están configuradas en el mismo directorio.

## Problemas al eliminar modelos de flujo de trabajo {#problems-deleting-workflow-models}

**Síntomas**

Problemas al intentar eliminar modelos de flujo de trabajo al acceder a una instancia de autor de AEM a través de Dispatcher.

**Pasos para reproducir:**

1. Inicie sesión en su instancia de autor (confirme que las solicitudes se dirigen a través del despachante).
1. Crear un nuevo flujo de trabajo; Por ejemplo, con el título definido como workflowtodelete.
1. Confirme que el flujo de trabajo se ha creado correctamente.
1. Seleccione y haga clic con el botón derecho en el flujo de trabajo y, a continuación, haga clic **en Eliminar**.

1. Haga clic en **Sí** para confirmar.
1. Aparecerá un cuadro de mensaje de error que muestra:\
   &quot; `ERROR 'Could not delete workflow model!!`&quot;.

**Resolución**

Agregue los encabezados siguientes a la `/clientheaders` sección del `dispatcher.any` archivo:

* `x-http-method-override`
* `x-requested-with`

`{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}`

## Interferencia con mod_ dir (Apache) {#interference-with-mod-dir-apache}

Describe cómo interactúa el despachante `mod_dir` con el servidor Web Apache, ya que esto puede llevar a varios efectos potencialmente inesperados:

### Apache 1.3 {#apache}

En Apache 1.3 `mod_dir` , se gestionan todas las solicitudes donde la URL se asigna a un directorio del sistema de archivos.

Será:

* redirigir la solicitud a `index.html` un archivo existente
* generar un listado de directorio

Cuando el despachante está habilitado, procesa dichas solicitudes registrándose como un controlador para el tipo `httpd/unix-directory`de contenido.

### Apache 2. x {#apache-x}

En Apache 2. x, las cosas son diferentes. Un módulo puede gestionar distintas etapas de la solicitud, como la reparación URL. `mod_dir` gestiona esta fase redirigiendo una solicitud (cuando la URL se asigna a un directorio) a la URL con `/` un anexo.

Dispatcher no intercepta `mod_dir` la reparación, sino que administra completamente la solicitud a la dirección URL redireccionada (es decir, con `/` anexado). Esto puede plantear un problema si el servidor remoto (por ejemplo, AEM) gestiona solicitudes de `/a_path` manera diferente a las solicitudes ( `/a_path/` cuando `/a_path` se asigna a un directorio existente).

Si esto sucede, debe:

* deshabilitar `mod_dir` para `Directory``Location` el subárbol que gestiona el despachante

* utilizar `DirectorySlash Off` para configurar `mod_dir` no anexar `/`
