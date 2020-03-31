---
source-git-commit: 36c4ad10a9140d368fdbf3f0939e6756cc2bfbbf
translation-type: tm+mt

---
# Directrices para contribuir a la documentación de AEM

## Filosofía de la documentación de AEM

Sabemos que los usuarios de Adobe Experience Manager trabajan en entornos muy competitivos, esforzándose por crear experiencias digitales que los diferencien de sus competidoras. Por lo tanto, es fundamental que, cuando Adobe ofrece nuevas herramientas avanzadas en AEM, estas herramientas se complementen con una documentación precisa y clara que permita al cliente aprovechar inmediatamente su inversión en AEM y maximizar el retorno de la inversión.

El objetivo de la documentación de AEM es poner la documentación en manos de los usuarios de AEM lo antes posible. Por lo tanto, damos prioridad a la documentación precisa y utilizable y nos esforzamos por actualizarla y mejorarla continuamente.

## AEM Documentation Contributions

Para mejorar continuamente la documentación de AEM, toda la comunidad de usuarios de AEM puede contribuir a la documentación. Ya sea a través de solicitudes o problemas, las mejoras en la documentación pueden ser correcciones, aclaraciones, expansiones y ejemplos adicionales.

## Normas de documentación

Si bien acogemos con satisfacción las contribuciones a nuestra documentación, cualquier contribución a la documentación de AEM, ya sea en forma de solicitud de extracción o de un problema, debería ajustarse a nuestras normas de contribución y documentación.

Las contribuciones que no cumplan estas normas podrán rechazarse.

### documento casos de uso estándar.

La documentación de AEM cubre casos de uso estándar. Los casos de uso que exceden el ámbito de la instalación estándar y el uso del producto no forman parte de la documentación de AEM.

### Generalmente no se documento errores ni sus soluciones alternativas.

La documentación de AEM cubre casos de uso estándar. Por este motivo, los errores, los efectos causados por errores y las soluciones alternativas para los errores generalmente no están documentados.

Las excepciones a esta regla se aplican a las notas de la versión en las que los problemas conocidos se pueden enumerar con posibles soluciones aprobadas por la administración de productos de AEM.

### Las contribuciones de documentación no sirven para responder preguntas técnicas.

Cualquier idea que tenga para mejorar la documentación de AEM será bienvenida como contribución. Sin embargo, los comentarios, los problemas y las solicitudes de extracción solo están destinados a *contribuciones* . No están pensados para utilizarse para responder a sus preguntas sobre cómo utilizar AEM, implementar su proyecto AEM o resolver problemas técnicos.

Las preguntas sobre el uso de AEM o los errores técnicos que pueda haber recibido se deben notificar mediante el proceso de asistencia normal a través del portal [de asistencia técnica de](https://daycare.day.com/home.html) Experience Manager o analizarse en la comunidad [de](http://help-forums.adobe.com/content/adobeforums/en/experience-manager-forum/adobe-experience-manager.html)Experience Manager.

***Las contribuciones a la documentación de AEM no sustituyen a la asistencia de Adobe*** y se rechazará cualquier contribución de este tipo que busque respuestas a preguntas relacionadas con la asistencia.

### Las contribuciones deben hacer referencia claramente a las páginas de documentación afectadas.

Si crea un problema para sugerir mejoras en la documentación, debe incluir vínculos a las páginas afectadas. Si crea un problema utilizando el vínculo **Editar esta página** en una página de documentación, el problema se creará automáticamente con un vínculo a la página.

Esto no se aplica a las solicitudes de extracción, ya que las solicitudes de extracción por su naturaleza hacen referencia a las páginas afectadas.

## Directrices de documentación

Pedimos que cualquier contribución a nuestra documentación siga ciertas pautas de estilo.

Seguir estas directrices facilita la revisión de su contribución y, por lo tanto, la integración en nuestra documentación es más rápida.

### Idioma y estilo

#### Idioma

* La documentación de AEM se crea y se mantiene en inglés de EE. UU.
* Mantenga las oraciones tan simples como sea posible.
* Mantenga el lenguaje claro y conciso.

Recuerde que los lectores de la documentación de AEM son de todo el mundo y no se puede esperar que hablen inglés de forma nativa o fluida. Evite los coloquialismos y mantenga el lenguaje tan claro y simple como sea posible.

#### Seguir el Manual de estilo de Microsoft

[El Manual de estilo](https://docs.microsoft.com/en-us/style-guide/welcome/) de Microsoft es una guía de estilo de documentación disponible libremente que se centra en la documentación de software y la documentación de AEM sigue esta guía siempre que sea posible.

### Formato

| Elemento | Estilo |
|---|---|
| Elemento u opción de la interfaz de usuario | **negrita** |
| Nombre de archivo, ruta, entrada de usuario, valores de parámetro | `monospaced` |
| Código, línea de comandos | ```Code Block``` |

### Capturas de pantalla

Las capturas de pantalla deben utilizarse con prudencia y solo cuando la descripción textual no sea suficiente.

No se deben utilizar marcadores u otras anotaciones en las capturas de pantalla (como marcos rojos, flechas o texto). De este modo, las capturas de pantalla son más fáciles de reutilizar o replicar en versiones localizadas de la documentación.

### Referencias específicas de la versión

Intente evitar cualquier referencia directa a una versión específica en todo el contenido de la documentación siempre que sea posible. Esto hace que la documentación sea más flexible y extensible para futuras versiones.

### Uso del día, AEM, CQ, CRX

El producto siempre debe mencionarse por primera vez en un artículo con su nombre completo **Adobe Experience Manager** y, a continuación, puede denominarse **AEM**.

No se deben utilizar Day, Day Software, CQ y CRX excepto cuando sea inevitable, como en nombres de clase o en referencia al historial de AEM.