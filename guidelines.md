---
source-git-commit: 053f90d9a0574c7d470cec6794498919e3340713
workflow-type: ht
source-wordcount: '752'
ht-degree: 100%

---
# Directrices para contribuir a la documentación de Adobe Experience Manager

## Filosofía de la documentación

Sabemos que los usuarios de Adobe Experience Manager están trabajando en entornos altamente competitivos, esforzándose por crear experiencias digitales que los diferencien de su competencia. Por lo tanto, es vital que cuando Adobe ofrece nuevas herramientas avanzadas en AEM, estas herramientas se complementen con una documentación precisa y clara que permita al cliente aprovechar inmediatamente su inversión en AEM y maximizar el retorno de la inversión.

El objetivo de la documentación de AEM es poner la información en manos de los usuarios de AEM lo antes posible. Por lo tanto, queremos que nuestra documentación sea precisa y útil y nos esforzamos por actualizarla y mejorarla continuamente.

## Contribuciones a la documentación

Para mejorar continuamente la documentación de AEM, contamos con la ayuda de toda la comunidad de usuarios de AEM. Ya sea a través de solicitudes de extracción o incidencias, las mejoras en la documentación pueden ser correcciones, aclaraciones, expansiones y ejemplos adicionales.

## Normas de documentación

Aunque nos encanta recibir las contribuciones a nuestra documentación, toda contribución a la documentación de AEM, ya sea en forma de solicitud de extracción o de incidencia, debe ajustarse a nuestras normas de contribución y documentación.

Las contribuciones que no cumplan estas normas se rechazarán.

### Registramos los casos de uso estándar.

La documentación de AEM abarca estos casos de uso estándar. Los casos de uso que exceden el ámbito de la instalación estándar y el uso del producto no forman parte de la documentación de AEM.

### Generalmente, no se registran errores ni sus soluciones alternativas.

La documentación de AEM abarca estos casos de uso estándar. Por este motivo, los errores, los efectos causados por errores y las soluciones alternativas para los errores no suelen registrarse.

Las excepciones a esta regla se aplican a las notas de la versión, donde los problemas conocidos pueden enumerarse con posibles soluciones aprobadas por el equipo de administración del producto de AEM.

### Las contribuciones a la documentación no sirven para responder preguntas técnicas.

Cualquier idea que tenga para mejorar la documentación de AEM es bienvenida como contribución. Sin embargo, los comentarios, las incidencias y las solicitudes de extracción están destinados únicamente a *contribuciones*. No están pensados para utilizarse para responder a sus preguntas sobre cómo utilizar AEM, implementar su proyecto de AEM o resolver problemas técnicos.

Cualquier pregunta sobre el uso de AEM o errores técnicos que pueda tener debe notificarse a través del proceso de asistencia normal mediante el [Portal de asistencia empresarial de Experience Cloud](https://helpx.adobe.com/es/contact/enterprise-support.ec.html) o analizarse en la [Comunidad de Experience Manager](https://forums.adobe.com/community/experience-cloud/marketing-cloud/experience-manager).

***Las contribuciones a la documentación de AEM no sustituyen al equipo de asistencia de Adobe*** y se rechazará cualquier contribución de este tipo que busque respuestas a preguntas relacionadas con la asistencia.

### Las contribuciones deben hacer referencia claramente a las páginas de documentación afectadas.

Si crea un problema para sugerir mejoras en la documentación, debe incluir vínculos a las páginas afectadas. Si crea un problema utilizando el vínculo **Editar esta página** en una página de documentación, el problema se creará automáticamente con un vínculo a la página.

Esto no se aplica a las solicitudes de extracción, ya que las solicitudes de extracción, por su naturaleza hacen referencia a las páginas afectadas.

## Directrices de documentación

Pedimos que cualquier contribución a nuestra documentación siga ciertas pautas de estilo.

Seguir estas directrices facilita la revisión de su contribución y, por lo tanto, la integración en nuestra documentación es más rápida.

### Idioma y estilo

#### Idioma

* La documentación de AEM se redacta y se actualiza en inglés estadounidense (originalmente).
* Escriba frases lo más simples posibles.
* Utilice un lenguaje claro y conciso.

Recuerde, los lectores de la documentación de AEM son de todo el mundo y no se puede esperar que sean hablantes nativos o fluidos del inglés. Evite los coloquialismos y utilice un lenguaje tan claro y simple como sea posible.

#### Siga el Manual de estilo de Microsoft

El [Manual de estilo de Microsoft](https://docs.microsoft.com/es-es/style-guide/welcome/) es una guía de estilo de documentación disponible libremente que se centra en documentación de software y la documentación AEM debe seguir esta guía siempre que sea posible.

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

Intente evitar cualquier referencia directa a una versión específica en todo el contenido de la documentación, siempre que sea posible. Esto hace que la documentación sea más flexible y extensible para futuras versiones.

### Uso de Día, AEM, CQ, CRX

El producto siempre se debe mencionar por su nombre completo, **Adobe Experience Manager**, por primera vez en un artículo y, después, se pueden usar sus siglas **AEM**.

No se deben utilizar Día, Software de día, CQ y CRX excepto cuando sea inevitable, como en nombres de clase o en referencia al historial de AEM.