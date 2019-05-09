---
title: Notas de la versión de AEM Dispatcher
seo-title: Notas de la versión de AEM Dispatcher
description: Notas de la versión específicas de Adobe Experience Manager Dispatcher
seo-description: Notas de la versión específicas de Adobe Experience Manager Dispatcher
uuid: ae 3 ccf 62-0514-4 c 03-a 3 b 9-71799 a 482 cbd
topic-tags: notas de la versión
content-type: referencia
products: SG_ EXPERIENCEMANAGER/6.4
discoiquuid: ff 3 d 38 e 0-71 c 9-4 b 41-85 f 9-fa 896393 aac 5
translation-type: tm+mt
source-git-commit: 2d72839d459973cba40f6a938ee157198c7cf50a

---


# Notas de la versión de AEM Dispatcher{#aem-dispatcher-release-notes}

## Información de versión {#release-information}

|  |
|--- |--- |
| Productos | Adobe Experience Manager (AEM) Dispatcher |
| Versión | 4.3.2 |
| Tipo | Versión menor |
| Fecha | 31 de enero de 2019 |
| URL de descarga | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Servicios de información de Internet de Microsoft (IIS)](release-notes.md#iis)</li></ul> |
| Compatibilidad | AEM 6.1 o superior |

## Requisitos del sistema y requisitos previos {#system-requirements-and-prerequisites}

Consulte la página [Plataformas](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) admitidas para obtener más información acerca de los requisitos y requisitos previos.

Adobe recomienda encarecidamente utilizar la versión más reciente de AEM Dispatcher para aprovechar la funcionalidad más reciente, las correcciones de errores más recientes y el mejor rendimiento posible.

## Instrucciones de instalación {#installation-instructions}

Para obtener instrucciones detalladas, consulte [Instalación de Dispatcher](dispatcher-install.md).

## Historial de versiones {#release-history}

### Versión 4.3.2 (2019-Jan -31) {#jan}

**Corrección de errores**:

* DISP -734 - Dispatcher produce un bloqueo en insert_ output_ filter si no se establece como controlador
* DISP -735 - RES no funciona en Linux Alpine
* DISP -740 - La carga de Dispatcher en macos Mojave está deshabilitada de forma predeterminada
* DISP -742 - Las solicitudes bloqueadas pueden filtrar información a recursos protegidos por autenticadores.

**Mejoras**:

* DISP -746 - Cadenas no modificadas en Dispatcher. any debería generar una advertencia

**Nueva función**:

* DISP -747 - Proporcionar información de solicitud en el entorno Apache

### Versión 4.3.1 (2018-2018) {#oct}

**Corrección de errores**:

* DISP -656 - Dispatcher proporciona un encabezado etag incorrecto
* DISP -694 - Suprimir advertencias cuando se mantienen conexiones viva
* DISP -714 - La administración de sesiones basada en cookies no funciona en IIS
* DISP -715 - Indicador seguro para cookie renderid
* DISP -720 - Los archivos temporales no cerrados pueden llevar al agotamiento (demasiados archivos abiertos)
* DISP -721 - Dispatcher interrumpe la encuesta () cuando Apache reinicia el elemento secundario
* DISP -722 - Los archivos de caché se crean con el modo octal 0600.
* DISP -723 - Tiempo de espera implícito de 10 minutos (y reintento) cuando los tiempos de espera de procesamiento se definen en 0
* DISP -725 - Los caracteres finales después de cadenas se convierten de forma silenciosa en valor sin nombre
* DISP -726 - Registro de una advertencia cuando ninguna granja coincide con el host entrante
* DISP -727 - Dispatcher comprueba la longitud del contenido de los archivos de caché vacíos
* DISP -730 - 404 al intentar acceder al archivo de encabezado sobre Dispatcher
* DISP -731 - Dispatcher es vulnerable a la inyección de registros
* DISP -732 - Dispatcher debe eliminar «/» consecutiva en la URL
* DISP -733 - Dispatcher debe establecer (calculate) un encabezado de edad

**Mejoras**:

* DISP -656 - Dispatcher proporciona un encabezado etag incorrecto
* DISP -694 - Suprimir advertencias cuando se mantienen conexiones viva
* DISP -715 - Indicador seguro para cookie renderid
* DISP -722 - Los archivos de caché se crean con el modo octal 0600.
* DISP -726 - Registro de una advertencia cuando ninguna granja coincide con el host entrante

### Versión 4.3.0 (2018-junio de 2018) {#jun}

**Corrección de errores**:

* DISP -682 - Nivel de registro numérico aplicado incorrectamente
* DISP -685 - 32 bits Solaris SPARC binarios tienen una referencia undefined a__ divdi 3
* DISP -688 - Dispatcher no devuelve el encabezado «X-Cache-Info» en la respuesta 404
* DISP -690 - El encabezado Last-Modified no se puede almacenar en caché
* DISP -691 - Accesos de acceso en w 3 wp. exe
* DISP -693 - Need to update Architecture details for solaris servers on dispatcher download page
* DISP -695 - Problema con dispatcherlog en el módulo Dispatcher 4.2.3
* DISP -698 - Dispatcher TTL necesita admitir las directivas s-maxage y privadas
* DISP -700 - Module no funciona correctamente en Linux Alpine
* DISP -704 - Las solicitudes del explorador que contienen % 2 b se envían al Editor no codificado
* DISP -705 - Dispatcher crash due to double free or corrupto (fasttop)
* DISP -706 - Durante la invalidación, dispatcher hace referencia a vínculos simbólicos de referencia que pueden provocar un bucle infinito
* DISP -709 - Bloquear algunas extensiones de URL personales
* DISP -710 - Compilaciones para Linux no utilizable en Centos 6

**Mejoras**:

* DISP -652 - Dispatcher proporciona encabezados de fecha incorrectos

## Recursos útiles {#helpful-resources}

* [Información general de AEM Dispatcher](dispatcher.md)

## Descargas {#downloads}

### Apache 2.4 {#apache}

| Plataforma | Arquitectura | Compatibilidad con SSL | Descargar |
|---|---|---|---|
| AIX | Powerpc (32 bits) | No | [dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz) |
| AIX | Powerpc (32 bits) | Sí | [dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz) |
| AIX | Powerpc (64 bits) | No | [dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz) |
| AIX | Powerpc (64 bits) | Sí | [dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz) |
| Linux | i 686 (32 bits) | No | [dispatcher-apache2.4-linux-i686-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.2.tar.gz) |
| Linux | i 686 (32 bits) | Sí | [dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz) |
| Linux | x 86_ 64 (64 bits) | No | [dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz) |
| Linux | x 86_ 64 (64 bits) | Sí | [dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz) |
| macOS | x 86_ 64 (64 bits) | No | [dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz) |
| Solaris | AMD (32 bits) | No | [dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz) |
| Solaris | AMD (32 bits) | Sí | [dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz) |
| Solaris | AMD (64 bits) | No | [dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz) |
| Solaris | AMD (64 bits) | Sí | [dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz) |
| Solaris | SPARC (32 bits) | No | [dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz) |
| Solaris | SPARC (32 bits) | Sí | [dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz) |
| Solaris | SPARC (64 bits) | No | [dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz) |
| Solaris | SPARC (64 bits) | Sí | [dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz) |

### IIS {#iis}

| Plataforma | Arquitectura | Compatibilidad con SSL | Descargar |
|---|---|---|---|
| Windows | x 86 (32 bits) | No | [dispatcher-iis-windows-x86-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.2.zip) |
| Windows | x 86 (32 bits) | Sí | [dispatcher-iis-windows-x86-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl-4.3.2.zip) |
| Windows | x 64 (64 bits) | No | [dispatcher-iis-windows-x64-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.2.zip) |
| Windows | x 64 (64 bits) | Sí | [dispatcher-iis-windows-x64-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl-4.3.2.zip) |
