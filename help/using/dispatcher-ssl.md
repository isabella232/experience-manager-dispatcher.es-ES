---
title: Utilizar SSL con Dispatcher
seo-title: Using SSL with Dispatcher
description: Aprenda a configurar Dispatcher para que se comunique con AEM mediante conexiones SSL.
seo-description: Learn how to configure Dispatcher to communicate with AEM using SSL connections.
uuid: 1a8f448c-d3d8-4798-a5cb-9579171171ed
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 771cfd85-6c26-4ff2-a3fe-dff8d8f7920b
index: y
internal: n
snippet: y
exl-id: ec378409-ddb7-4917-981d-dbf2198aca98
source-git-commit: e87af532ee3268f0a45679e20031c3febc02de58
workflow-type: tm+mt
source-wordcount: '1355'
ht-degree: 100%

---

# Utilizar SSL con Dispatcher {#using-ssl-with-dispatcher}

Utilice conexiones SSL entre Dispatcher y el equipo de procesamiento:

* [SSL unidireccional](#use-ssl-when-dispatcher-connects-to-aem)
* [SSL mutuo](#configuring-mutual-ssl-between-dispatcher-and-aem)

>[!NOTE]
>
>Las operaciones relacionadas con los certificados SSL están vinculadas a productos de terceros. No están cubiertos por el Contrato de Mantenimiento y Soporte Platino de Adobe.

## Usar SSL cuando Dispatcher se conecta a AEM {#use-ssl-when-dispatcher-connects-to-aem}

Configure Dispatcher para que se comunique con la instancia de procesamiento de AEM o CQ mediante conexiones SSL.

Antes de configurar Dispatcher, configure AEM o CQ para utilizar SSL:

* AEM 6.2: [Habilitación de HTTP sobre SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=es)
* AEM 6.1: [Habilitación de HTTP sobre SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=es)
* Versiones de AEM anteriores: consulte [esta página](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=es).

### Encabezados de solicitud relacionados con SSL {#ssl-related-request-headers}

Cuando Dispatcher recibe una solicitud HTTPS, Dispatcher incluye los siguientes encabezados en la solicitud posterior que envía a AEM o CQ:

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

Una solicitud a través de Apache-2.4 con `mod_ssl` incluye encabezados similares al del siguiente ejemplo:

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### Configurar Dispatcher para usar SSL {#configuring-dispatcher-to-use-ssl}

Para configurar Dispatcher para que se conecte con AEM o CQ a través de SSL, su archivo [dispatcher.any](dispatcher-configuration.md) necesita las siguientes propiedades:

* Host virtual que administra solicitudes HTTPS.
* La sección `renders` del host virtual incluye un elemento que identifica el nombre de host y el puerto de la instancia de CQ o AEM que utiliza HTTPS.
* El elemento `renders` incluye una propiedad denominada `secure` de valor `1`.

Nota: Cree otro host virtual para administrar solicitudes HTTP si fuera necesario.

En el siguiente ejemplo, el archivo `dispatcher.any` muestra los valores de propiedad para la conexión mediante HTTPS a una instancia de CQ que se ejecuta en el host `localhost` y en el puerto `8443`:

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requests
         "https://*"
      }
      /renders
      {
      /0001
         {
            # hostname or IP of the render
            /hostname "localhost"
            # port of the render
            /port "8443"
            # connect via HTTPS
            /secure "1"
         }
      }
     # the rest of the properties are omitted
   }

   /non-secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTP requests
         "http://*"
      }
      /renders
      {
         /0001
      {
         # hostname or IP of the render
         /hostname "localhost"
         # port of the render
         /port "4503"
      }
   }
    # the rest of the properties are omitted
}
```

## Configurar un SSL mutuo entre Dispatcher y AEM {#configuring-mutual-ssl-between-dispatcher-and-aem}

Para utilizar SSL mutuo, configure las conexiones entre Dispatcher y el equipo de procesamiento (normalmente una instancia de publicación de AEM o CQ):

* Dispatcher se conecta a la instancia de procesamiento a través de SSL.
* La instancia de procesamiento comprueba la validez del certificado de Dispatcher.
* Dispatcher comprueba que la CA del certificado de la instancia de procesamiento es de confianza.
* (Opcional) Dispatcher comprueba que el certificado de la instancia de procesamiento coincide con la dirección del servidor de la instancia de procesamiento.

Para configurar SSL mutuo, necesita certificados firmados por una autoridad de certificación (CA) de confianza. Los certificados autofirmados no son adecuados. Puede actuar como CA o utilizar los servicios de una CA de terceros para firmar sus certificados. Para configurar SSL mutuo, necesita los siguientes elementos:

* Certificados firmados para la instancia de procesamiento y Dispatcher
* El certificado de CA (si actúa como CA)
* Abra las bibliotecas OpenSSL para generar la CA, los certificados y las solicitudes de certificado

Para configurar SSL mutuo, realice los siguientes pasos:

1. [Instale](dispatcher-install.md) la última versión de Dispatcher para su plataforma. Utilice un binario de Dispatcher que admita SSL (SSL está en el nombre del archivo, como dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar).
1. [Cree o consiga un certificado firmado por la CA](dispatcher-ssl.md#main-pars-title-3) para Dispatcher y la instancia de procesamiento.
1. [Cree un repositorio de claves que contenga certificado de procesamiento](dispatcher-ssl.md#main-pars-title-6) y configure el servicio HTTP del procesamiento para utilizarlo.
1. [Configure el módulo del servidor web de Dispatcher](dispatcher-ssl.md#main-pars-title-4) para SSL mutuo.

### Crear o conseguir certificados firmados por la CA {#creating-or-obtaining-ca-signed-certificates}

Cree o consiga los certificados firmados por la CA que autentican la instancia de publicación y Dispatcher.

#### Crear su CA {#creating-your-ca}

Si actúa como CA, utilice [OpenSSL](https://www.openssl.org/) para crear la autoridad de certificación que firme los certificados de servidor y cliente. (Debe tener instaladas las bibliotecas OpenSSL). Si utiliza una CA de terceros, no realice este procedimiento.

1. Abra un terminal y cambie el directorio actual al que contenga el archivo `CA.sh`, como `/usr/local/ssl/misc`.
1. Para crear la CA, introduzca el siguiente comando y, a continuación, proporcione los valores cuando se le solicite:

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >Varias propiedades del archivo `openssl.cnf` controlan el comportamiento del script CA.sh. Edite este archivo según sea necesario antes de crear su CA.

#### Crear certificados {#creating-the-certificates}

Utilice OpenSSL para crear las solicitudes de certificado que se enviarán a la CA de terceros o para firmar con la CA.

Al crear un certificado, OpenSSL utiliza la propiedad Nombre común para identificar al titular del certificado. Para el certificado de la instancia de procesamiento, utilice el nombre de host del equipo de instancia como Nombre común si está configurando Dispatcher para aceptar el certificado, solo si coincide con el nombre de host de la instancia de publicación. (Consulte la propiedad [DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11)).

1. Abra un terminal y cambie el directorio actual al que contenga el archivo CH.sh de sus bibliotecas OpenSSL.
1. Introduzca el siguiente comando y proporcione los valores cuando se le solicite. Si es necesario, utilice el nombre de host de la instancia de publicación como Nombre común. El nombre de host es un nombre con resolución DNS para la dirección IP del procesamiento:

   ```shell
   ./CA.sh -newreq
   ```

   Si utiliza una CA de terceros, envíe el archivo newreq.pem a la CA para que lo firme. Si actúa como CA, avance al paso 3.

1. Para firmar el certificado mediante el certificado de su CA, introduzca el siguiente comando:

   ```shell
   ./CA.sh -sign
   ```

   Se crearán dos archivos denominados `newcert.pem` y `newkey.pem` en el directorio que contenga sus archivos de administración de CA. Estos dos archivos son el certificado público y la clave privada del equipo de procesamiento, respectivamente.

1. Cambie el nombre `newcert.pem` por `rendercert.pem` y cambie el nombre `newkey.pem` por `renderkey.pem`.
1. Repita los pasos 2 y 3 para crear un nuevo certificado y una nueva clave pública para el módulo de Dispatcher. Asegúrese de utilizar un nombre común específico de la instancia de Dispatcher.
1. Cambie el nombre `newcert.pem` por `dispcert.pem` y cambie el nombre `newkey.pem` por `dispkey.pem`.

### Configurar SSL en el equipo de procesamiento {#configuring-ssl-on-the-render-computer}

Configure SSL en la instancia de procesamiento utilizando los archivos `rendercert.pem` y `renderkey.pem`.

#### Conversión del certificado de procesamiento al formato JKS (Java™ KeyStore) {#converting-the-render-certificate-to-jks-format}

Utilice el siguiente comando para convertir el certificado de procesamiento, que es un archivo PEM, a un archivo PKCS#12. Incluya también el certificado de la CA que firmó el certificado de procesamiento:

1. En una ventana del terminal, cambie el directorio actual a la ubicación del certificado de procesamiento y la clave privada.
1. Para convertir el certificado de procesamiento, que es un archivo PEM, a un archivo PKCS#12, introduzca el siguiente comando. Incluya también el certificado de la CA que firmó el certificado de procesamiento:

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. Para convertir el archivo PKCS#12 al formato Java™ KeyStore (JKS), introduzca el siguiente comando:

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. Java™ Keystore se crea utilizando un alias predeterminado. Puede cambiar el alias si lo desea:

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### Añadir el certificado de CA al truststore del procesador. {#adding-the-ca-cert-to-the-render-s-truststore}

Si actúa como CA, importe el certificado de CA a un repositorio de claves. A continuación, configure la JVM que ejecuta la instancia de procesamiento para confiar en el repositorio de claves.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. Utilice un editor de texto para abrir el archivo cacert.pem y quitar todo el texto que preceda a la siguiente línea:

   `-----BEGIN CERTIFICATE-----`

1. Utilice el siguiente comando para importar el certificado en un repositorio de claves:

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. Para configurar la JVM que ejecuta la instancia de procesamiento para que confíe en el repositorio de claves, utilice la siguiente propiedad del sistema:

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   Por ejemplo, si utiliza el script crx-quickstart/bin/quickstart para iniciar la instancia de publicación, puede modificar la propiedad CQ_JVM_OPTS:

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### Configurar la instancia de procesamiento {#configuring-the-render-instance}

Para configurar el servicio HTTP de la instancia de procesamiento para que utilice SSL, utilice el certificado de procesamiento con las instrucciones en la sección *Habilitar SSL en la instancia de publicación*:

* AEM 6.2: [Habilitación de HTTP sobre SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=es)
* AEM 6.1: [Habilitación de HTTP sobre SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=es)
* Versiones de AEM anteriores: consulte [esta página.](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=es)

### Configurar SSL para el módulo de Dispatcher {#configuring-ssl-for-the-dispatcher-module}

Para configurar Dispatcher para que utilice SSL mutuo, prepare el certificado de Dispatcher y, a continuación, configure el módulo del servidor web.

### Crear un certificado de Dispatcher unificado {#creating-a-unified-dispatcher-certificate}

Combine el certificado de Dispatcher y la clave privada no cifrada en un solo archivo PEM. Utilice un editor de texto o el comando `cat` para crear un archivo similar al siguiente ejemplo:

1. Abra un terminal y cambie el directorio actual a la ubicación del archivo dispkey.pem.
1. Para desencriptar la clave privada, introduzca el siguiente comando:

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. Utilice un editor de texto o el comando `cat` para combinar la clave privada no cifrada y el certificado en un solo archivo similar al siguiente ejemplo:

   ```xml
   -----BEGIN RSA PRIVATE KEY-----
   MIICxjBABgkqhkiG9w0B...
   ...M2HWhDn5ywJsX
   -----END RSA PRIVATE KEY-----
   -----BEGIN CERTIFICATE-----
   MIIC3TCCAk...
   ...roZAs=
   -----END CERTIFICATE-----
   ```

### Especificar el certificado que se va a usar para Dispatcher {#specifying-the-certificate-to-use-for-dispatcher}

Agregue las siguientes propiedades a la [configuración del módulo Dispatcher](dispatcher-install.md#main-pars-55-35-1022) (en `httpd.conf`):

* `DispatcherCertificateFile`: Ruta al archivo de certificado unificado de Dispatcher que contiene el certificado público y la clave privada no cifrada. Este archivo se utiliza cuando el servidor SSL solicita el certificado de cliente de Dispatcher.
* `DispatcherCACertificateFile`: La ruta al archivo de certificado de la CA, utilizada si el servidor SSL presenta una CA en la que no confía una autoridad raíz.
* `DispatcherCheckPeerCN`: Si se habilita (`On`) o se deshabilita (`Off`) la comprobación de nombres de host para certificados de servidor remoto.

El siguiente código es un ejemplo de configuración:

```xml
<IfModule disp_apache2.c>
  DispatcherConfig conf/dispatcher.any
  DispatcherLog    logs/dispatcher.log
  DispatcherLogLevel 3
  DispatcherNoServerHeader 0
  DispatcherDeclineRoot 0
  DispatcherUseProcessedURL 0
  DispatcherPassError 0
  DispatcherCertificateFile disp_unified.pem
  DispatcherCACertificateFile cacert.pem
  DispatcherCheckPeerCN On
</IfModule>
```
