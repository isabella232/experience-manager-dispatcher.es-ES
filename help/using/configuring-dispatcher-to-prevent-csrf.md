---
title: Configuración de Dispatcher para prevenir ataques de tipo CSRF
seo-title: Configuración de Adobe AEM Dispatcher para evitar ataques CSRF
description: Obtenga información sobre cómo configurar AEM Dispatcher para evitar ataques de falsificación de solicitudes entre sitios.
seo-description: Descubra cómo configurar Adobe AEM Dispatcher para evitar ataques de falsificación de solicitudes entre sitios.
uuid: f290bdeb-54e2-4649-b0fc-6257b422af2d
topic-tags: dispatcher
content-type: reference
discoiquuid: d61d021e-b338-4a1d-91ee-55427557e931
translation-type: tm+mt
source-git-commit: 69edbe7608b46c93d238515e4223606eadad0ac4
workflow-type: tm+mt
source-wordcount: '246'
ht-degree: 4%

---


# Configuración de Dispatcher para prevenir ataques de tipo CSRF{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM proporciona un marco para prevenir los ataques de falsificación de solicitudes entre sitios. Para poder utilizar correctamente este marco, debe realizar los siguientes cambios en la configuración del despachante:

>[!NOTE]
>
>Asegúrese de actualizar los números de regla en los ejemplos siguientes en función de la configuración existente. Recuerde que los despachantes utilizarán la última regla coincidente para conceder una autorización o denegación, de modo que coloque las reglas cerca de la parte inferior de la lista existente.

1. En la sección `/clientheaders` de la granja de autores.any y la granja de publicaciones.any, agregue la entrada siguiente a la parte inferior de la lista:\
   `CSRF-Token`
1. En la sección /filtros del archivo `author-farm.any` y `publish-farm.any` o `publish-filters.any`, agregue la línea siguiente para permitir solicitudes de `/libs/granite/csrf/token.json` a través del despachante.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. En la sección `/cache /rules` de su `publish-farm.any`, agregue una regla para impedir que el despachante almacene en caché el archivo `token.json`. Los autores suelen omitir el almacenamiento en caché, por lo que no es necesario agregar la regla a su `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Para validar que la configuración está funcionando, vea el archivo dispatcher.log en modo DEBUG para validar que el archivo token.json no se esté almacenando en caché y que los filtros no lo bloqueen. Debería ver mensajes similares a:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

También puede validar que las solicitudes se están realizando correctamente en su apache `access_log`. Las solicitudes de &quot;/libs/granite/csrf/token.json deben devolver un código de estado HTTP 200.
