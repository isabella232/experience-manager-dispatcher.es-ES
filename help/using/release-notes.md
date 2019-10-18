---
title: Notas de la versión de Dispatcher de AEM
seo-title: Notas de la versión de Dispatcher de AEM
description: Notas de la versión específicas de Adobe Experience Manager Dispatcher
seo-description: Notas de la versión específicas de Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: notas de la versión
content-type: referencia
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
translation-type: tm+mt
source-git-commit: fd48b2841ca47293cb04ee5063720fa8725f9fcd

---


# Notas de la versión de Dispatcher de AEM{#aem-dispatcher-release-notes}

## Información de versión {#release-information}

|  |  |
|--- |--- |
| Productos | Distribuidor de Adobe Experience Manager (AEM) |
| Versión | 4.3.3 |
| Tipo | Versión menor |
| Fecha | 18 de octubre de 2019 |
| Descargar URL | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Compatibilidad | AEM 6.1 o superior |

## Requisitos y requisitos previos del sistema {#system-requirements-and-prerequisites}

Consulte la página Plataformas [](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) admitidas para obtener más información sobre los requisitos y requisitos previos.

Adobe recomienda encarecidamente utilizar la versión más reciente de AEM Dispatcher para aprovechar la funcionalidad más reciente, las correcciones de errores más recientes y el mejor rendimiento posible.

## Instrucciones de instalación {#installation-instructions}

Para obtener instrucciones detalladas, consulte [Instalación de Dispatcher](dispatcher-install.md).

## Historial de versiones {#release-history}

### Versión 4.3.3 (2019-Oct-18) {#october}

**Corrección** de errores:

* DISP-739 - Distribuidor LogLevel: **level** no funciona
* DISP-749 - El despachante Alpine Linux se bloquea con el nivel de registro de seguimiento

**Mejoras**:

* DISP-813 - Compatibilidad con Dispatcher para openssl 1.1.x
* DISP-814 - Errores 40x de Apache durante los vaciados de caché
* DISP-818 - mod_expires agrega encabezados Cache-Control para contenido no accesible
* DISP-821 - No almacenar el contexto de registro en el socket
* DISP-822 - Dispatcher debe utilizar ppoll en lugar de pselect
* DISP-824 - Secure DispatcherUseForwardedHost
* DISP-825: registrar un mensaje especial cuando ya no hay espacio en disco
* DISP-826 - Compatibilidad con URI de devolución con una cadena de consulta

**Nuevas funciones**:

* DISP-703: proporción de aciertos de caché específica de la granja de servidores
* DISP-827 - Servidor local para pruebas
* DISP-828 - Creación de una imagen de acoplador de prueba para el despachante

### Versión 4.3.2 (2019-Ene-31) {#jan}

**Corrección** de errores:

* DISP-734 - Dispatcher provoca un bloqueo en insert_output_filter si no se establece como controlador
* DISP-735 - Las REs no funcionan en Alpine Linux
* DISP-740 - Cargar despachante en macOS Mojave está desactivado de forma predeterminada
* DISP-742 - Las solicitudes bloqueadas pueden filtrar información a los recursos protegidos del comprobador de autenticación

**Mejoras**:

* DISP-746 - Las cadenas sin etiquetar en dispatcher.any deben generar una advertencia

**Nueva función**:

* DISP-747 - Proporciona información de solicitud en el entorno Apache

### Versión 4.3.1 (2018-Oct-16) {#oct}

**Corrección** de errores:

* DISP-656 - Dispatcher presenta un encabezado ETag incorrecto
* DISP-694 - Suprimir las advertencias cuando las conexiones de mantenimiento se mantengan antiguas
* DISP-714: la administración de sesiones basada en cookies no funciona en IIS
* DISP-715 - Indicador seguro para cookie renderid
* DISP-720 - Los archivos temporales no cerrados pueden provocar agotamiento (demasiados archivos abiertos)
* DISP-721 - Dispatcher interrumpe la función poll() cuando Apache reinicia correctamente el elemento secundario
* DISP-722 - Los archivos de caché se crean con el modo octal 0600
* DISP-723: tiempo de espera implícito de 10 minutos (y reintento) cuando los tiempos de espera de procesamiento se establecen en 0
* DISP-725 - Los caracteres finales después de que las cadenas se conviertan silenciosamente en valores sin nombre
* DISP-726 - Registre una advertencia cuando ninguna granja coincida realmente con el host entrante
* DISP-727 - Dispatcher comprueba la longitud del contenido de la solicitud para los archivos de caché vacíos
* DISP-730 - 404 al intentar acceder al archivo de encabezado a través del despachante
* DISP-731 - Dispatcher es vulnerable a la inyección de registro
* DISP-732 - Dispatcher debe eliminar ‘/’ consecutivo en la dirección URL
* DISP-733 - Dispatcher debe establecer (calcular) un encabezado de edad

**Mejoras**:

* DISP-656 - Dispatcher presenta un encabezado ETag incorrecto
* DISP-694 - Suprimir las advertencias cuando las conexiones de mantenimiento se mantengan antiguas
* DISP-715 - Indicador seguro para cookie renderid
* DISP-722 - Los archivos de caché se crean con el modo octal 0600
* DISP-726 - Registre una advertencia cuando ninguna granja coincida realmente con el host entrante

### Versión 4.3.0 (2018-Jun-13) {#jun}

**Corrección** de errores:

* DISP-682: nivel de registro numérico aplicado incorrectamente
* DISP-685 - Los binarios Solaris SPARC de 32 bits tienen una referencia indefinida a __divdi3
* DISP-688 - Dispatcher no devuelve el encabezado "X-Cache-Info" en la respuesta 404
* DISP-690 - El encabezado de última modificación no se puede almacenar en caché
* DISP-691 - Violaciones de acceso en w3wp.exe
* DISP-693 - Necesidad de actualizar los detalles arquitectónicos de los servidores solaris en la página de descarga del despachante
* DISP-695 - Problema con el nivel DispatcherLog en el módulo Dispatcher 4.2.3
* DISP-698 - Dispatcher TTL necesita admitir el s-maxage y las directivas privadas
* DISP-700 - El módulo no funciona correctamente en Alpine Linux
* DISP-704 - Las solicitudes de explorador que contienen %2b se envían al Editor sin codificar
* DISP-705 - Bloqueo de Dispatcher debido a doble gratuita o corrupción (tapa rápida)
* DISP-706 - Durante la invalidación, dispatcher sigue los enlaces simbólicos de referencia inversa que pueden provocar un bucle infinito
* DISP-709 - Bloquee algunas extensiones de URL personales
* DISP-710 - Compilaciones para Linux no utilizables en Cent OS 6

**Mejoras**:

* DISP-652 - Dispatcher presenta un encabezado de fecha incorrecto

## Recursos útiles {#helpful-resources}

* [Información general de AEM Dispatcher](dispatcher.md)

## Descargas {#downloads}

### Apache 2.4 {#apache}

| Plataforma | Arquitectura | Compatibilidad con SSL | Descargar |
|---|---|---|---|
| Linux | i686 (32 bits) | Ninguna | [dispatcher-apache2.4-linux-i686-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.3.tar.gz) |
| Linux | i686 (32 bits) | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz) |
| Linux | i686 (32 bits) | 1.1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz) |
| Linux | x86_64 (64 bits) | Ninguna | [dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz) |
| Linux | x86_64 (64 bits) | 1.0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz) |
| Linux | x86_64 (64 bits) | 1.1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz) |
| macOS | x86_64 (64 bits) | Ninguna | [dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz) |

### IIS {#iis}

| Plataforma | Arquitectura | Compatibilidad con SSL | Descargar |
|---|---|---|---|
| Windows | x86 (32 bits) | Ninguna | [dispatcher-is-windows-x86-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.3.zip) |
| Windows | x86 (32 bits) | 1.0 | [dispatcher-is-windows-x86-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.3.zip) |
| Windows | x86 (32 bits) | 1.1 | [dispatcher-is-windows-x86-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.3.zip) |
| Windows | x64 (64 bits) | Ninguna | [dispatcher-is-windows-x64-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.3.zip) |
| Windows | x64 (64 bits) | 1.0 | [dispatcher-is-windows-x64-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.3.zip) |
| Windows | x64 (64 bits) | 1.1 | [dispatcher-is-windows-x64-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.3.zip) |
