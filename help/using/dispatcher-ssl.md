---
title: Uso de SSL con Dispatcher
seo-title: Uso de SSL con Dispatcher
description: Aprenda a configurar Dispatcher para comunicarse con AEM mediante conexiones SSL.
seo-description: Aprenda a configurar Dispatcher para comunicarse con AEM mediante conexiones SSL.
uuid: 1 a 8 f 448 c-d 3 d 8-4798-a 5 cb -9579171171 ed
contentOwner: Usuario
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referencia
discoiquuid: 771 cfd 85-6 c 26-4 ff 2-a 3 fe-dff 8 d 8 f 7920 b
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Uso de SSL con Dispatcher {#using-ssl-with-dispatcher}

Utilice conexiones SSL entre Dispatcher y el equipo de procesamiento:

* [SSL unidireccional](dispatcher-ssl.md#main-pars-title-1)
* [SSL mutuo](dispatcher-ssl.md#main-pars-title-2)

>[!NOTE]
>
>Las operaciones relacionadas con certificados SSL están enlazadas a productos de terceros. No están cubiertas por el contrato de mantenimiento y asistencia técnica Platinum de Adobe.

## Utilizar SSL cuando Dispatcher conecta con AEM {#use-ssl-when-dispatcher-connects-to-aem}

Configure Dispatcher para que se comunique con la instancia de procesamiento AEM o CQ mediante conexiones SSL.

Antes de configurar Dispatcher, configure AEM o CQ para utilizar SSL:

* AEM 6.2: [Habilitar HTTP a través de SSL](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/config-ssl.html)
* AEM 6.1: [Habilitar HTTP a través de SSL](https://docs.adobe.com/content/docs/en/aem/6-1/deploy/configuring/config-ssl.html)
* Versiones anteriores de AEM: consulte [esta página](https://helpx.adobe.com/experience-manager/aem-previous-versions.html).

### Encabezados de solicitud relacionados con SSL {#ssl-related-request-headers}

Cuando Dispatcher envía una solicitud HTTPS, Dispatcher incluye los siguientes encabezados en la solicitud siguiente que envía a AEM o CQ:

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

Una solicitud a través de Apache -2.2 incluye `mod_ssl` encabezados similares al siguiente ejemplo:

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### Configuración de Dispatcher para utilizar SSL {#configuring-dispatcher-to-use-ssl}

Para configurar Dispatcher para que se conecte con AEM o CQ a través de SSL, [el](dispatcher-configuration.md) archivo dispatcher. any requiere las siguientes propiedades:

* Host virtual que gestiona solicitudes HTTPS.
* `renders` La sección del host virtual incluye un elemento que identifica el nombre de host y el puerto de la instancia de CQ o AEM que utiliza HTTPS.
* El `renders` elemento incluye una propiedad denominada `secure` value `1`.

Nota: Cree otro host virtual para gestionar solicitudes HTTP si es necesario.

El siguiente ejemplo dispatcher. any file muestra los valores de propiedad para la conexión mediante HTTPS a una instancia de CQ que se ejecuta en host `localhost` y puerto `8443`:

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

## Configuración de SSL mutua entre Dispatcher y AEM {#configuring-mutual-ssl-between-dispatcher-and-aem}

Configure las conexiones entre Dispatcher y el equipo de procesamiento (normalmente una instancia de publicación de AEM o CQ) para utilizar Secure SSL:

* Dispatcher se conecta a la instancia de procesamiento a través de SSL.
* La instancia de procesamiento verifica la validez del certificado de Dispatcher.
* Dispatcher verifica que la CA del certificado de la instancia de procesamiento sea de confianza.
* (Opcional) Dispatcher comprueba que el certificado de la instancia de procesamiento coincide con la dirección de servidor de la instancia de procesamiento.

Para configurar una SSL mutua, necesita certificados firmados por una autoridad de certificados (CA) de confianza. Los certificados con firma automática no son adecuados. Puede actuar como CA o utilizar los servicios de una CA de terceros para firmar sus certificados. Para configurar una SSL mutua, necesita los siguientes elementos:

* Certificados firmados para la instancia de procesamiento y Dispatcher
* Certificado CA (si actúa como CA)
* bibliotecas openssl para generar la CA, los certificados y las solicitudes de certificado.

Realice los siguientes pasos para configurar una SSL mutua:

1. [Instale](dispatcher-install.md) la versión más reciente de Dispatcher para su plataforma. Utilice un binario Dispatcher que admita SSL (SSL está en el nombre del archivo, como dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar).
1. [Cree o obtenga certificado firmado CA](dispatcher-ssl.md#main-pars-title-3) para Dispatcher y la instancia de procesamiento.
1. [Cree un keystore que contenga un certificado de procesamiento](dispatcher-ssl.md#main-pars-title-6) y configure el servicio HTTP de procesamiento para utilizarlo.
1. [Configure el módulo de servidor web Dispatcher](dispatcher-ssl.md#main-pars-title-4) para una SSL mutua.

### Creación o obtención de certificados firmados por CA {#creating-or-obtaining-ca-signed-certificates}

Cree o obtenga los certificados firmados por CA que autentican la instancia de publicación y Dispatcher.

#### Creación de su CA {#creating-your-ca}

Si actúa como CA, utilice [openssl](https://www.openssl.org/) para crear la entidad emisora de certificados que firma el servidor y los certificados de cliente. (Debe tener instaladas las bibliotecas openssl). Si utiliza una CA de terceros, no realice este procedimiento.

1. Abra un terminal y cambie el directorio actual al directorio que rodea el archivo CA. sh, como `/usr/local/ssl/misc`.
1. Para crear la CA, introduzca el comando siguiente y, a continuación, proporcione los valores cuando se le indique:

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >Varias propiedades del archivo openssl. cnf controlan el comportamiento de la secuencia de comandos CA. sh. Debe modificar este archivo según sea necesario antes de crear la CA.

#### Creación de certificados {#creating-the-certificates}

Utilice openssl para crear solicitudes de certificado para enviarlas al de terceros o para firmar con su CA.

Cuando se crea un certificado, openssl utiliza la propiedad Nombre común para identificar el marcador de certificado. Para el certificado de la instancia de procesamiento, utilice el nombre de host del equipo de instancia como Nombre común si está configurando Dispatcher para aceptar el certificado únicamente si coincide con el nombre de host de la instancia de Publish. (Consulte la propiedad [dispatchercheckpeercn](dispatcher-ssl.md#main-pars-title-11) .)

1. Abra un terminal y cambie el directorio actual al directorio que contiene el archivo CH. sh de las bibliotecas openssl.
1. Introduzca el siguiente comando y proporcione valores cuando se le solicite. Si es necesario, utilice el nombre de host de la instancia de publicación como Nombre común. El nombre de host es el nombre DNS-soluvable para la dirección IP del procesamiento:

   ```shell
   ./CA.sh -newreq
   ```

   Si utiliza una CA de terceros, envíe el archivo newreq. pem a la CA para firmar. Si actúa como CA, continúe con el paso 3.

1. Introduzca el siguiente comando para firmar el certificado utilizando el certificado de su CA:

   ```shell
   ./CA.sh -sign
   ```

   En el directorio que contiene los archivos de administración de CA, se crean dos archivos denominados newcert. pem y newkey. pem. Se trata del certificado público y de la clave privada del equipo de procesamiento, respectivamente.

1. Cambie el nombre de newcert. pem para rendercert. pem y cambie el nombre de newkey. pem a renderkey. pem.
1. Repita los pasos 2 y 3 para crear un nuevo certificado y una nueva clave pública para el módulo Dispatcher. Asegúrese de utilizar un nombre común específico de la instancia Dispatcher.
1. Cambie el nombre de newcert. pem a dispcert. pem y cambie el nombre de newkey. pem a dispkey. pem.

### Configuración de SSL en el equipo de procesamiento {#configuring-ssl-on-the-render-computer}

Configure SSL en la instancia de procesamiento utilizando los archivos rendercert. pem y renderkey. pem.

#### Conversión del certificado de procesamiento en formato JKS {#converting-the-render-certificate-to-jks-format}

Utilice el siguiente commídeo y convierta el certificado de procesamiento, que es un archivo PEM, a un archivo PKCS # 12. También incluya el certificado de la CA que ha firmado el certificado de procesamiento:

1. En una ventana de terminal, cambie el directorio actual a la ubicación del certificado de procesamiento y de la clave privada.
1. Introduzca el siguiente commídeo y convierta el certificado de procesamiento, que es un archivo PEM, a un archivo PKCS # 12. También incluya el certificado de la CA que ha firmado el certificado de procesamiento:

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. Introduzca el siguiente comando para convertir el archivo PKCS # 12 en formato Java keystore (JKS):

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. Java Keystore se crea con un alias predeterminado. Cambie el alias si lo desea:

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### Adición de CA Cert al Truststore de Render {#adding-the-ca-cert-to-the-render-s-truststore}

Si actúa como CA, importe el certificado CA a un keystore. A continuación, configure JVM que ejecute la instancia de procesamiento para confiar en el keystore.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. Utilice un editor de texto para abrir el archivo cacert. pem y quitar todo el texto que precede a la línea de seguidores:

   `-----BEGIN CERTIFICATE-----`

1. Utilice el siguiente comando para importar el certificado en un keystore:

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. Para configurar el JVM que ejecuta la instancia de procesamiento para confiar en el keystore, utilice la siguiente propiedad del sistema:

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   Por ejemplo, si utiliza la secuencia de comandos crx-quickstart/bin/quickstart para iniciar la instancia de publicación, puede modificar la propiedad CQ_ JVM_ OPTS:

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### Configuración de la instancia de procesamiento {#configuring-the-render-instance}

Utilice el certificado de procesamiento con las instrucciones de *la sección Habilitar SSL en la instancia* de publicación para configurar el servicio HTTP de la instancia de procesamiento para utilizar SSL:

* AEM 6.2: [Habilitar HTTP a través de SSL](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/config-ssl.html)
* AEM 6.1: [Habilitar HTTP a través de SSL](https://docs.adobe.com/content/docs/en/aem/6-1/deploy/configuring/config-ssl.html)
* Versiones anteriores de AEM: consulte [esta página.](https://helpx.adobe.com/experience-manager/aem-previous-versions.html)

### Configuración de SSL para el módulo Dispatcher {#configuring-ssl-for-the-dispatcher-module}

Para configurar Dispatcher a fin de utilizar SSL mutuos, prepare el certificado Dispatcher y, a continuación, configure el módulo de servidor web.

### Creación de un certificado de despachante unificado {#creating-a-unified-dispatcher-certificate}

Combine el certificado de Dispatcher y la clave privada no cifrada en un solo archivo PEM. Utilice un editor de texto o `cat` el comando para crear un archivo similar al siguiente ejemplo:

1. Abra un terminal y cambie el directorio actual a la ubicación del archivo dispkey. pem.
1. Para descifrar la clave privada, introduzca el siguiente comando:

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. Utilice un editor de texto o el `cat` comando para combinar la clave privada no cifrada y el certificado en un solo archivo similar al siguiente ejemplo:

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

Añada las siguientes propiedades a la configuración del módulo [Dispatcher](dispatcher-install.md#main-pars-55-35-1022) (en `httpd.conf`):

* `DispatcherCertificateFile`: La ruta al archivo de certificado unificado Dispatcher que contiene el certificado público y la clave privada no cifrada. Este archivo se utiliza cuando el servidor SSL solicita el certificado de cliente Dispatcher.
* `DispatcherCACertificateFile`: Ruta al archivo de certificado CA, que se utiliza si el servidor SSL presenta una CA a la que la autoridad raíz no tiene confianza.
* `DispatcherCheckPeerCN`: Indica si se habilita ( `On`) o deshabilita ( `Off`) el nombre de host de certificados de servidor remoto.

El siguiente código es una configuración de ejemplo:

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

