---
title: Uso de SSL con Dispatcher
seo-title: Uso de SSL con Dispatcher
description: Obtenga información sobre cómo configurar Dispatcher para que se comunique con AEM mediante conexiones SSL.
seo-description: Obtenga información sobre cómo configurar Dispatcher para que se comunique con AEM mediante conexiones SSL.
uuid: 1a8f448c-d3d8-4798-a5cb-9579171171ed
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 771cfd85-6c26-4ff2-a3fe-dff8d8f7920b
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: f9fb0e94dbd1c67bf87463570e8b5eddaca11bf3
workflow-type: tm+mt
source-wordcount: '1375'
ht-degree: 0%

---


# Uso de SSL con Dispatcher {#using-ssl-with-dispatcher}

Utilice conexiones SSL entre Dispatcher y el equipo de procesamiento:

* [SSL unidireccional](#use-ssl-when-dispatcher-connects-to-aem)
* [SSL mutuo](#configuring-mutual-ssl-between-dispatcher-and-aem)

>[!NOTE]
>
>Las operaciones relacionadas con los certificados SSL están enlazadas a productos de terceros. No están cubiertos por el contrato de mantenimiento y soporte Platinum de Adobe.

## Usar SSL cuando Dispatcher se conecte a AEM {#use-ssl-when-dispatcher-connects-to-aem}

Configure Dispatcher para que se comunique con la instancia de procesamiento de AEM o CQ mediante conexiones SSL.

Antes de configurar Dispatcher, configure AEM o CQ para utilizar SSL:

* AEM 6.2: [Habilitación de HTTP sobre SSL](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/config-ssl.html)
* AEM 6.1: [Habilitación de HTTP sobre SSL](https://docs.adobe.com/content/docs/en/aem/6-1/deploy/configuring/config-ssl.html)
* Versiones AEM anteriores: consulte [esta página](https://helpx.adobe.com/experience-manager/aem-previous-versions.html).

### Encabezados de solicitud relacionados con SSL {#ssl-related-request-headers}

Cuando Dispatcher recibe una solicitud HTTPS, Dispatcher incluye los siguientes encabezados en la solicitud posterior que envía a AEM o CQ:

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

Una solicitud a través de Apache-2.4 con `mod_ssl` incluye encabezados similares al siguiente ejemplo:

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### Configuración de Dispatcher para Usar SSL {#configuring-dispatcher-to-use-ssl}

Para configurar Dispatcher para que se conecte con AEM o CQ a través de SSL, el archivo [dispatcher.any](dispatcher-configuration.md) requiere las siguientes propiedades:

* Un host virtual que gestiona solicitudes HTTPS.
* La sección `renders` del host virtual incluye un elemento que identifica el nombre de host y el puerto de la instancia de CQ o AEM que utiliza HTTPS.
* El elemento `renders` incluye una propiedad denominada `secure` del valor `1`.

Nota: Cree otro host virtual para gestionar solicitudes HTTP si es necesario.

El siguiente ejemplo de archivo dispatcher.any muestra los valores de propiedad para la conexión mediante HTTPS a una instancia de CQ que se ejecuta en el host `localhost` y en el puerto `8443`:

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requestss
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
         "https://*"
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

## Configuración de SSL mutuo entre Dispatcher y AEM {#configuring-mutual-ssl-between-dispatcher-and-aem}

Configure las conexiones entre Dispatcher y el equipo de procesamiento (normalmente una instancia de publicación de AEM o CQ) para utilizar SSL mutuo:

* Dispatcher se conecta a la instancia de procesamiento a través de SSL.
* La instancia de procesamiento comprueba la validez del certificado de Dispatcher.
* Dispatcher comprueba que la CA del certificado de la instancia de procesamiento es de confianza.
* (Opcional) Dispatcher comprueba que el certificado de la instancia de procesamiento coincide con la dirección del servidor de la instancia de procesamiento.

Para configurar una SSL mutua, necesita certificados firmados por una autoridad de certificación (CA) de confianza. Los certificados con firma automática no son adecuados. Puede actuar como entidad emisora de certificados o utilizar los servicios de una entidad emisora de certificados de terceros para firmar sus certificados. Para configurar una SSL mutua, necesita los siguientes elementos:

* Certificados firmados para la instancia de procesamiento y Dispatcher
* El certificado de CA (si actúa como CA)
* Bibliotecas OpenSSL para generar la CA, los certificados y las solicitudes de certificado.

Realice los siguientes pasos para configurar una SSL mutua:

1. [](dispatcher-install.md) Instale la versión más reciente de Dispatcher para su plataforma. Utilice un binario Dispatcher que admita SSL (SSL está en el nombre del archivo, como dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar).
1. [Cree u obtenga ](dispatcher-ssl.md#main-pars-title-3) certificados firmados por CA para Dispatcher y la instancia de procesamiento.
1. [Cree un almacén de claves que contenga un ](dispatcher-ssl.md#main-pars-title-6) certificado de procesamiento y configure el servicio HTTP del procesamiento para utilizarlo.
1. [Configure el ](dispatcher-ssl.md#main-pars-title-4) módulo del servidor web Dispatcher para SSL mutuo.

### Creación u obtención de certificados firmados por CA {#creating-or-obtaining-ca-signed-certificates}

Cree u obtenga los certificados firmados por CA que autentican la instancia de publicación y Dispatcher.

#### Creación de la CA {#creating-your-ca}

Si está actuando como CA, utilice [OpenSSL](https://www.openssl.org/) para crear la autoridad certificadora que firma los certificados de cliente y servidor. (Debe tener instaladas las bibliotecas OpenSSL). Si utiliza una CA de terceros, no realice este procedimiento.

1. Abra un terminal y cambie el directorio actual al directorio que contiene el archivo CA.sh, como `/usr/local/ssl/misc`.
1. Para crear la CA, introduzca el siguiente comando y, a continuación, proporcione valores cuando se le solicite:

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >Varias propiedades del archivo openssl.cnf controlan el comportamiento del script CA.sh. Debe modificar este archivo según sea necesario antes de crear la CA.

#### Creación de certificados {#creating-the-certificates}

Utilice OpenSSL para crear las solicitudes de certificado para enviarlas a la CA de terceros o para firmar con la CA.

Al crear un certificado, OpenSSL utiliza la propiedad Nombre común para identificar al titular del certificado. Para el certificado de la instancia de procesamiento, utilice el nombre de host del equipo de instancia como Nombre común si está configurando Dispatcher para aceptar el certificado solo si coincide con el nombre de host de la instancia de Publish. (Consulte la propiedad [DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11)).

1. Abra un terminal y cambie el directorio actual al directorio que contiene el archivo CH.sh de las bibliotecas OpenSSL.
1. Introduzca el siguiente comando y proporcione valores cuando se le solicite. Si es necesario, utilice el nombre de host de la instancia de publicación como Nombre común. El nombre de host es un nombre con resolución DNS para la dirección IP del procesamiento:

   ```shell
   ./CA.sh -newreq
   ```

   Si está utilizando una CA de terceros, envíe el archivo newreq.pem a la CA para que lo firme. Si actúa como CA, continúe con el paso 3.

1. Introduzca el siguiente comando para firmar el certificado mediante el certificado de su CA:

   ```shell
   ./CA.sh -sign
   ```

   En el directorio que contiene los archivos de administración de CA se crean dos archivos denominados newcert.pem y newkey.pem. Son el certificado público y la clave privada del equipo de procesamiento, respectivamente.

1. Cambie el nombre de newcert.pem a rendercert.pem y cambie el nombre de newkey.pem a renderkey.pem.
1. Repita los pasos 2 y 3 para crear un nuevo certificado y una nueva clave pública para el módulo Dispatcher. Asegúrese de utilizar un Nombre común específico de la instancia de Dispatcher.
1. Cambie el nombre de newcert.pem a dispcert.pem y cambie el nombre de newkey.pem a dispkey.pem.

### Configuración de SSL en el equipo de procesamiento {#configuring-ssl-on-the-render-computer}

Configure SSL en la instancia de procesamiento mediante los archivos rendercert.pem y renderkey.pem.

#### Conversión del certificado de procesamiento al formato JKS {#converting-the-render-certificate-to-jks-format}

Utilice el siguiente comando para convertir el certificado de procesamiento, que es un archivo PEM, en un archivo PKCS#12. Incluya también el certificado de la CA que firmó el certificado de procesamiento:

1. En una ventana de terminal, cambie el directorio actual a la ubicación del certificado de procesamiento y la clave privada.
1. Introduzca el siguiente comando para convertir el certificado de procesamiento, que es un archivo PEM, en un archivo PKCS#12. Incluya también el certificado de la CA que firmó el certificado de procesamiento:

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. Introduzca el siguiente comando para convertir el archivo PKCS#12 al formato Java KeyStore (JKS):

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. El almacén de claves de Java se crea con un alias predeterminado. Cambie el alias si lo desea:

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### Añadir el certificado de CA en el Truststore de procesamiento {#adding-the-ca-cert-to-the-render-s-truststore}

Si actúa como entidad emisora de certificados, importe el certificado de CA en un almacén de claves. A continuación, configure el JVM que ejecuta la instancia de procesamiento para que confíe en el almacén de claves.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. Utilice un editor de texto para abrir el archivo cacert.pem y quitar todo el texto que precede a la siguiente línea:

   `-----BEGIN CERTIFICATE-----`

1. Utilice el siguiente comando para importar el certificado en un almacén de claves:

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. Para configurar el JVM que ejecuta la instancia de procesamiento para que confíe en el almacén de claves, utilice la siguiente propiedad del sistema:

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   Por ejemplo, si utiliza la secuencia de comandos crx-quickstart/bin/quickstart para el inicio de la instancia de publicación, puede modificar la propiedad CQ_JVM_OPTS:

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### Configuración de la instancia de procesamiento {#configuring-the-render-instance}

Utilice el certificado de procesamiento con las instrucciones de la sección *Habilitar SSL en la instancia de publicación* para configurar el servicio HTTP de la instancia de procesamiento para utilizar SSL:

* AEM 6.2: [Habilitación de HTTP sobre SSL](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/config-ssl.html)
* AEM 6.1: [Habilitación de HTTP sobre SSL](https://docs.adobe.com/content/docs/en/aem/6-1/deploy/configuring/config-ssl.html)
* Versiones AEM anteriores: consulte [esta página.](https://helpx.adobe.com/experience-manager/aem-previous-versions.html)

### Configuración de SSL para el módulo Dispatcher {#configuring-ssl-for-the-dispatcher-module}

Para configurar Dispatcher para que utilice SSL mutuo, prepare el certificado de Dispatcher y, a continuación, configure el módulo del servidor web.

### Creación de un certificado de despachante unificado {#creating-a-unified-dispatcher-certificate}

Combine el certificado de distribuidor y la clave privada no cifrada en un único archivo PEM. Utilice un editor de texto o el comando `cat` para crear un archivo similar al siguiente ejemplo:

1. Abra un terminal y cambie el directorio actual a la ubicación del archivo dispkey.pem.
1. Para descifrar la clave privada, escriba el siguiente comando:

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. Utilice un editor de texto o el comando `cat` para combinar la clave privada no cifrada y el certificado en un único archivo similar al siguiente ejemplo:

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

### Especificación del certificado que se usará para Dispatcher {#specifying-the-certificate-to-use-for-dispatcher}

Añada las siguientes propiedades en la configuración del módulo [Dispatcher](dispatcher-install.md#main-pars-55-35-1022) (en `httpd.conf`):

* `DispatcherCertificateFile`:: Ruta al archivo de certificado unificado de Dispatcher que contiene el certificado público y la clave privada no cifrada. Este archivo se utiliza cuando el servidor SSL solicita el certificado de cliente Dispatcher.
* `DispatcherCACertificateFile`:: Ruta al archivo de certificado de CA, utilizada si el servidor SSL presenta una CA que no es de confianza para una autoridad raíz.
* `DispatcherCheckPeerCN`:: Si se debe habilitar (  `On`) o deshabilitar (  `Off`) la comprobación de nombres de host para certificados de servidor remoto.

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

