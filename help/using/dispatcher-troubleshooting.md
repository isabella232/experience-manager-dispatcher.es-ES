---
title: Solución de problemas de Dispatcher
seo-title: Solución de problemas de Dispatcher AEM
description: Aprenda a solucionar problemas de Dispatcher.
seo-description: Aprenda a solucionar problemas de AEM Dispatcher.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
translation-type: tm+mt
source-git-commit: 9af0dc22d32f1176b84c28a70b1a4701414d434e
workflow-type: tm+mt
source-wordcount: '553'
ht-degree: 7%

---


# Solución de problemas de Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Las versiones de Dispatcher son independientes de AEM, pero la documentación de Dispatcher está incrustada en la documentación de AEM. Utilice siempre la documentación de Dispatcher incrustada en la documentación para la versión más reciente de AEM.
>
>Es posible que se le haya redirigido a esta página si ha seguido un vínculo a la documentación de Dispatcher insertado en la documentación de una versión anterior de AEM.

>[!NOTE]
>
>Consulte también la Base [de conocimiento de](https://helpx.adobe.com/cq/kb/index/dispatcher.html)Dispatcher, [Resolución de problemas](https://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) de vaciado de despachantes y las Preguntas más frecuentes [sobre problemas principales de](dispatcher-faq.md) Dispatcher para obtener más información.

## Comprobar la configuración básica {#check-the-basic-configuration}

Como siempre, los primeros pasos son comprobar lo básico:

* [Confirmar operación básica](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Compruebe todos los archivos de registro del servidor web y del distribuidor. Si es necesario, aumente el `loglevel` utilizado para el [registro](/help/using/dispatcher-configuration.md#logging)del despachante.

* [Compruebe la configuración](/help/using/dispatcher-configuration.md):

   * ¿Tiene varios despachantes?

      * ¿Ha determinado qué Dispatcher está administrando el sitio web o la página que está investigando?
   * ¿Ha implementado filtros?

      * ¿Están estos impactando en el asunto que estás investigando?


## Herramientas de diagnóstico de IIS {#iis-diagnostic-tools}

IIS proporciona varias herramientas de seguimiento, según la versión real:

* IIS 6: las herramientas de diagnóstico de IIS se pueden descargar y configurar
* IIS 7: el seguimiento está completamente integrado

Esto puede ayudarle a supervisar la actividad.

## No se encontraron IIS y 404 {#iis-and-not-found}

Al utilizar IIS, puede que experimente `404 Not Found` que se le devuelva en varios casos. Si es así, consulte los siguientes artículos de la Base de conocimiento.

* [IIS 6/7: El método POST devuelve 404](https://helpx.adobe.com/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS 6: Solicitudes a direcciones URL que contienen la ruta de acceso base `/bin` devuelta `404 Not Found`](https://helpx.adobe.com/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

También debe comprobar que la raíz de la caché del despachante y la raíz del documento IIS están establecidas en el mismo directorio.

## Problemas al eliminar modelos de flujo de trabajo {#problems-deleting-workflow-models}

**Síntomas**

Problemas al intentar eliminar modelos de flujo de trabajo al acceder a una instancia de autor AEM a través del despachante.

**Pasos para reproducir:**

1. Inicie sesión en la instancia de autor (confirme que las solicitudes se dirigen a través del despachante).
1. Crear un nuevo flujo de trabajo; por ejemplo, con el Título establecido en workflowToDelete.
1. Confirme que el flujo de trabajo se creó correctamente.
1. Seleccione el flujo de trabajo y haga clic con el botón secundario en él y, a continuación, haga clic en **Eliminar**.

1. Haga clic en **Sí** para confirmar.
1. Aparecerá un cuadro de mensaje de error que muestra:\
   &quot; `ERROR 'Could not delete workflow model!!`&quot;.

**Resolución**

Añada los siguientes encabezados a la `/clientheaders` sección del `dispatcher.any` archivo:

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

Esto describe cómo el despachante interactúa con `mod_dir` dentro del servidor web Apache, ya que esto puede producir varios efectos potencialmente inesperados:

### Apache 1.3 {#apache}

En Apache 1.3 `mod_dir` gestiona todas las solicitudes en las que la URL se asigna a un directorio del sistema de archivos.

O bien:

* redirigir la solicitud a un `index.html` archivo existente
* generar una lista de directorios

Cuando el despachante está habilitado, procesa dichas solicitudes registrándose como un controlador para el tipo de contenido `httpd/unix-directory`.

### Apache 2.x {#apache-x}

En Apache 2.x las cosas son diferentes. Un módulo puede gestionar diferentes etapas de la solicitud, como la reparación de direcciones URL. `mod_dir` controla esta etapa redirigiendo una solicitud (cuando la URL se asigna a un directorio) a la URL con un `/` anexo.

Dispatcher no intercepta la `mod_dir` reparación, sino que la gestiona completamente en la dirección URL redireccionada (es decir, con `/` anexado). Esto podría plantear un problema si el servidor remoto (por ejemplo, AEM) maneja las solicitudes de `/a_path` forma diferente a las solicitudes a `/a_path/` (cuando se `/a_path` asigna a un directorio existente).

Si esto sucede, debe:

* deshabilitar `mod_dir` para el `Directory` o `Location` subárbol administrado por el distribuidor

* use `DirectorySlash Off` para configurar `mod_dir` no anexar `/`
