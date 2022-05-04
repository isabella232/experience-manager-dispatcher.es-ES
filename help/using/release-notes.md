---
title: Notas de la versión de Dispatcher de AEM
seo-title: AEM Dispatcher Release Notes
description: Notas de versión específicas de Adobe Experience Manager Dispatcher
seo-description: Release notes specific to Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
exl-id: b55c7a34-d57b-4d45-bd83-29890f1524de
source-git-commit: 5430d53571414cf3bf764bb8523c252bb77a3bf2
workflow-type: ht
source-wordcount: '1037'
ht-degree: 100%

---

# Notas de la versión de Dispatcher de AEM {#aem-dispatcher-release-notes}

## Información de la versión {#release-information}

|  |  |
|--- |--- |
| Productos | Adobe Experience Manager (AEM) Dispatcher |
| Versión | 4.3.5 |
| Tipo | Versión menor |
| Fecha | 4 de abril de 2022 |
| Descargar URL | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Compatibilidad | AEM 6.1 o superior |

## Requisitos y requisitos previos del sistema {#system-requirements-and-prerequisites}

Consulte la página [Plataformas admitidas](https://helpx.adobe.com/es/experience-manager/6-4/sites/deploying/using/technical-requirements.html) para obtener más información sobre los requisitos y prerrequisitos.

Adobe recomienda encarecidamente utilizar la última versión de Dispatcher de AEM para beneficiarse de las últimas funcionalidades, las correcciones de errores más recientes y el mejor rendimiento posible.

## Instrucciones de instalación {#installation-instructions}

Para obtener instrucciones detalladas, consulte [Instalación de Dispatcher](dispatcher-install.md).

## Historial de versiones {#release-history}

### Versión 4.3.5 (04 de abril de 2022) {#apr}

**Mejoras**:

* DISP-954: invalidación del soporte incluso si la caducidad no ha pasado
* DISP-949: Dispatcher devuelve 200 en lugar de 404 incluso si la petición POST está bloqueada por el filtro

### Versión 4.3.4 (29 de noviembre de 2021) {#nov}

**Corrección de errores**:

* DISP-833: los encabezados X-Forwarded-Host pueden contener una lista de nombres de host separados por coma
* DISP-835: DispatcherUseForwardedHost absorve el encabezado Host si viene en último lugar

**Mejoras**:

* DISP-874: crea una configuración de Dispatcher para activar o desactivar la implementación de DISP-818 mediante un indicador `DispatcherRestrictUncacheableContent`. El valor por defecto es 0. Cuando está activada, elimina los encabezados de almacenamiento en caché establecidos por el moderador que caducan para el contenido que no se puede almacenar en caché. Es distinto del comportamiento encontrado en la versión 4.3.3 (donde el valor predeterminado era Activado) pero igual que las versiones anteriores a la 4.3.3 (donde el valor predeterminado era Desactivado). Mantener la opción predeterminada `DispatcherRestrictUncacheableContent` desactivada es el método recomendado, por lo que la caché del explorador tiene más flexibilidad. Si, al actualizar de la versión 4.3.3 a la 4.3.4, desea mantener el mismo comportamiento que en la 4.3.3, debe establecer explícitamente `DispatcherRestrictUncacheableContent` como Activado.
* DISP-841: Dispatcher no respeta /serverStaleOnError para el código de respuesta 504
* DISP-874: cree una configuración de Dispatcher para activar o desactivar la implementación de DISP-818
* DISP-883: el seguimiento muestra la descomposición de la solicitud de URL en Dispatcher
* DISP-944: URL de vanidad precargadas

### Versión 4.3.3 (18-Oct-2019) {#october}

**Corrección de errores**:

* DISP-739: Dispatcher de nivel de registro: **nivel** no funciona
* DISP-749: Dispatcher de Alpine Linux se bloquea con el nivel de registro de seguimiento

**Mejoras**:

* DISP-813: compatibilidad con Dispatcher para openssl 1.1.x
* DISP-814: errores de Apache 40x durante los vaciados de caché
* DISP-818: mod_expires agrega encabezados Cache-Control para contenido no almacenable en caché
* DISP-821: no almacenar el contexto de registro en el socket
* DISP-822: Dispatcher debe utilizar ppoll en lugar de pselect
* DISP-824: Secure DispatcherUseForwardedHost
* DISP-825: registrar mensaje especial cuando no hay más espacio en el disco
* DISP-826: admite URI de recuperación con una cadena de consulta

**Nuevas funciones**:

* DISP-703: proporción de visitas de caché específica de la granja
* DISP-827: servidor local para pruebas
* DISP-828: crear una imagen docker de prueba para Dispatcher

### Versión 4.3.2 (31-Ene-2019) {#jan}

**Corrección de errores**:

* DISP-734: Dispatcher provoca un bloqueo en insert_output_filter si no se configura como controlador
* DISP-735: REs no funciona en Alpine Linux
* DISP-740: la carga de Dispatcher en macOS Mojave está deshabilitada de forma predeterminada
* DISP-742: las solicitudes bloqueadas pueden filtrar información a los recursos protegidos por el comprobador de autenticación

**Mejoras**:

* DISP-746: las cadenas sin etiquetar en dispatcher.any deben generar una advertencia

**Nueva función**:

* DISP-747: proporcionar información de solicitud en el entorno Apache

### Versión 4.3.1 (16-Oct-2018) {#oct}

**Corrección de errores**:

* DISP-656: Dispatcher proporciona un encabezado ETag incorrecto
* DISP-694: suprimir las advertencias cuando las conexiones de mantenimiento se quedan obsoletas
* DISP-714: la administración de sesiones basada en cookies no funciona en IIS
* DISP-715: indicador seguro para la cookie renderid
* DISP-720: los archivos temporales no cerrados, pueden causar agotamiento (demasiados archivos abiertos)
* DISP-721: Dispatcher interrumpe poll() cuando Apache reinicia correctamente el elemento secundario
* DISP-722: los archivos de caché se crean con el modo octal 0600
* DISP-723: tiempo de espera implícito de 10 minutos (y reintento) al establecer el tiempo de espera de procesamiento en 0
* DISP-725: los caracteres finales después de cadenas se convierten silenciosamente en valores sin nombre
* DISP-726: registrar una advertencia cuando ninguna granja coincida realmente con el host entrante
* DISP-727: Dispatcher comprueba la longitud del contenido de la solicitud para los archivos de caché vacíos
* DISP-730: 404 al intentar acceder al archivo de encabezado a través de Dispatcher
* DISP-731: Dispatcher es vulnerable a la inyección de registro
* DISP-732: Dispatcher debe eliminar &quot;/&quot; consecutivo en la URL
* DISP-733: Dispatcher debe establecer (calcular) un encabezado de edad

**Mejoras**:

* DISP-656: Dispatcher proporciona un encabezado ETag incorrecto
* DISP-694: suprimir las advertencias cuando las conexiones de mantenimiento se quedan obsoletas
* DISP-715: indicador seguro para la cookie renderid
* DISP-722: los archivos de caché se crean con el modo octal 0600
* DISP-726: registrar una advertencia cuando ninguna granja coincida realmente con el host entrante

### Versión 4.3.0 (13-Jun-2018) {#jun}

**Corrección de errores**:

* DISP-682: nivel de registro numérico aplicado incorrectamente
* DISP-685: los binarios Solaris SPARC de 32 bits tienen una referencia indefinida a __divdi3
* DISP-688: Dispatcher no devuelve el encabezado &quot;X-Cache-Info&quot; en la respuesta 404
* DISP-690: el encabezado Última modificación no se puede almacenar en caché
* DISP-691: violaciones de acceso en w3wp.exe
* DISP-693: necesidad de actualizar los detalles arquitectónicos para los servidores solaris en la página de descarga de Dispatcher
* DISP-695: problema con el nivel de DispatcherLog en el módulo 4.2.3 de Dispatcher
* DISP-698: Dispatcher TTL necesita admitir s-maxage y directivas privadas
* DISP-700: el módulo no funciona correctamente en Alpine Linux
* DISP-704: las solicitudes del explorador que contienen %2b se envían al editor sin codificar
* DISP-705: caída de Dispatcher debido a doble libertad o corrupción (tapa rápida)
* DISP-706: durante la invalidación, Dispatcher sigue enlaces simbólicos de referencia que pueden causar un bucle infinito
* DISP-709: bloquear algunas extensiones de URL de vanidad
* DISP-710: compilaciones para Linux no utilizables en Cent OS 6

**Mejoras**:

* DISP-652: Dispatcher proporciona un encabezado de fecha incorrecto

## Recursos útiles {#helpful-resources}

* [Información general de Dispatcher de AEM](dispatcher.md)

## Descargas {#downloads}

### Apache 2.4 {#apache}

| Plataforma | Arquitectura | Compatibilidad con OpenSSL | Descargar |
|---|---|---|---|
| Linux | i686 (32 bits) | Ninguna | [dispatcher-apache2.4-linux-i686-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.5.tar.gz) |
| Linux | i686 (32 bits) | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.5.tar.gz) |
| Linux | i686 (32 bits) | 1.1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.5.tar.gz) |
| Linux | x86_64 (64 bits) | Ninguna | [dispatcher-apache2.4-linux-x86_64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.5.tar.gz) |
| Linux | x86_64 (64 bits) | 1.0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.5.tar.gz) |
| Linux | x86_64 (64 bits) | 1.1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.5.tar.gz) |
| Linux | aarch64 (64 bits) | Ninguno | [dispatcher-apache2.4-linux-aarch64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-4.3.5.tar.gz) |
| Linux | aarch64 (64 bits) | 1.0 | [dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.5.tar.gz) |
| Linux | aarch64 (64 bits) | 1.1 | [dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.5.tar.gz) |
| macOS | arm64 (64 bits) | Ninguno | [dispatcher-apache2.4-darwin-arm64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-arm64-4.3.5.tar.gz) |
| macOS | x86_64 (64 bits) | Ninguna | [dispatcher-apache2.4-darwin-x86_64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.5.tar.gz) |

### IIS {#iis}

| Plataforma | Arquitectura | Compatibilidad con OpenSSL | Descargar |
|---|---|---|---|
| Windows | x86 (32 bits) | Ninguna | [dispatcher-iis-windows-x86-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.5.zip) |
| Windows | x86 (32 bits) | 1.0 | [dispatcher-iis-windows-x86-ssl1.0-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.5.zip) |
| Windows | x86 (32 bits) | 1.1 | [dispatcher-iis-windows-x86-ssl1.1-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.5.zip) |
| Windows | x64 (64 bits) | Ninguna | [dispatcher-iis-windows-x64-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.5.zip) |
| Windows | x64 (64 bits) | 1.0 | [dispatcher-iis-windows-x64-ssl1.0-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.5.zip) |
| Windows | x64 (64 bits) | 1.1 | [dispatcher-iis-windows-x64-ssl1.1-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.5.zip) |
