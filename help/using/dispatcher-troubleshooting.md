---
title: Solución de problemas de Dispatcher
seo-title: Troubleshooting AEM Dispatcher Problems
description: Aprenda a solucionar problemas de Dispatcher.
seo-description: Learn to troubleshoot AEM Dispatcher issues.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
exl-id: 29f338ab-5d25-48a4-9309-058e0cc94cff
source-git-commit: 26c8edbb142297830c7c8bd068502263c9f0e7eb
workflow-type: tm+mt
source-wordcount: '560'
ht-degree: 100%

---

# Solución de problemas de Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM, pero la documentación de Dispatcher está incrustada en la de AEM. Utilice siempre la documentación de Dispatcher incrustada en la documentación para la versión más reciente de AEM.
>
>Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher insertado en la documentación de una versión anterior de AEM.

>[!NOTE]
>
>Consulte también la [Base de conocimientos de Dispatcher](https://helpx.adobe.com/es/experience-manager/kb/index/dispatcher.html), la [Solución de problemas de vaciado de Dispatcher](https://experienceleague.adobe.com/search.html?lang=es#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]) y las [Preguntas frecuentes sobre los problemas principales de Dispatcher](dispatcher-faq.md) para obtener más información.

## Comprobar la configuración básica {#check-the-basic-configuration}

Como siempre, los primeros pasos son comprobar los conceptos básicos:

* [Confirmar operación básica](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Compruebe todos los archivos de registro de su servidor web y de Dispatcher. Si es necesario, aumente el `loglevel` utilizado para el [registro](/help/using/dispatcher-configuration.md#logging) de Dispatcher.

* [Comprobar la configuración](/help/using/dispatcher-configuration.md):

   * ¿Tiene varias instancias de Dispatcher?

      * ¿Ha determinado qué Dispatcher está administrando el sitio web o la página que está investigando?
   * ¿Ha implementado filtros?

      * ¿Estos filtros afectan al asunto que está investigando?


## Herramientas de diagnóstico de IIS {#iis-diagnostic-tools}

IIS proporciona varias herramientas de seguimiento, en función de la versión real:

* IIS 6: las herramientas de diagnóstico de IIS se pueden descargar y configurar
* IIS 7: el seguimiento está totalmente integrado

Estas herramientas pueden ayudarle a monitorizar la actividad.

## IIS y 404 no encontrado {#iis-and-not-found}

Al utilizar IIS, es posible que `404 Not Found` se devuelva en varias ocasiones. Si es así, consulte los siguientes artículos de la Base de conocimientos.

* [IIS 6/7: el método POST devuelve el valor 404](https://helpx.adobe.com/es/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: las solicitudes a direcciones URL que contienen la ruta base `/bin` devuelven `404 Not Found`](https://helpx.adobe.com/es/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

También debe comprobar que la raíz de la caché de Dispatcher y la del documento de IIS estén configuradas en el mismo directorio.

## Problemas al eliminar modelos de flujo de trabajo {#problems-deleting-workflow-models}

**Síntomas**

Problemas al intentar eliminar modelos de flujo de trabajo al acceder a una instancia de autor de AEM a través de Dispatcher.

**Pasos a seguir:**

1. Inicie la sesión en la instancia de autor (confirme que las solicitudes se dirigen a través de Dispatcher).
1. Cree un nuevo flujo de trabajo; por ejemplo, con el título establecido en workflowToDelete.
1. Confirme que el flujo de trabajo se ha creado correctamente.
1. Seleccione el flujo de trabajo y haga clic con el botón derecho del ratón en él y, a continuación, haga clic en **Eliminar**.

1. Haga clic en **Sí** para confirmar.
1. Aparece un cuadro de mensaje de error que muestra lo siguiente:\
   “`ERROR 'Could not delete workflow model!!`”.

**Resolución**

Agregue los siguientes encabezados a la sección `/clientheaders` del archivo `dispatcher.any`:

* `x-http-method-override`
* `x-requested-with`

```
{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}
```

## Interferencia con mod_dir (Apache) {#interference-with-mod-dir-apache}

Este proceso describe cómo interactúa Dispatcher con `mod_dir` dentro del servidor web Apache, ya que puede producir varios efectos potencialmente inesperados:

### Apache 1.3 {#apache}

En Apache 1.3, `mod_dir` administra cada solicitud donde la URL se asigna a un directorio en el sistema de archivos.

Puede:

* redirigir la solicitud a un archivo `index.html` existente
* generar una lista de directorios

Cuando Dispatcher esté activado, procesa dichas solicitudes registrándose como controlador para el tipo de contenido `httpd/unix-directory`.

### Apache 2.x {#apache-x}

En Apache 2.x las cosas son diferentes. Un módulo puede administrar diferentes etapas de la solicitud, como la corrección de URL. `mod_dir` administra esta fase redireccionando una solicitud (cuando la dirección URL se asigna a un directorio) a la dirección URL con un `/` anexado.

Dispatcher no intercepta la corrección `mod_dir`, pero administra completamente la solicitud a la dirección URL redirigida (es decir, con `/` anexado). Este proceso puede suponer un problema si el servidor remoto (por ejemplo, AEM) administra las solicitudes a `/a_path` de forma diferente a las solicitudes a `/a_path/` (cuando `/a_path` se asigna a un directorio existente).

Si esto sucede, debe:

* desactivar `mod_dir` para el subárbol `Directory` o `Location` que administra Dispatcher

* utilizar `DirectorySlash Off` para configurar `mod_dir` no anexar `/`
