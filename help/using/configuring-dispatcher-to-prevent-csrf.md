---
title: Configuración de Dispatcher para evitar ataques CSRF
seo-title: Configuración de Adobe AEM Dispatcher para evitar ataques CSRF
description: Descubra cómo configurar AEM Dispatcher para evitar ataques de falsificación de solicitudes entre sitios.
seo-description: Descubra cómo configurar Adobe AEM Dispatcher para evitar ataques de falsificación de solicitudes entre sitios.
uuid: f 290 bdeb -54 e 2-4649-b 0 fc -6257 b 422 af 2 d
topic-tags: dispatcher
content-type: referencia
discoiquuid: d 61 d 021 e-b 338-4 a 1 d -91 ee -55427557 e 931
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Configuración de Dispatcher para evitar ataques CSRF{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM proporciona un marco destinado a evitar ataques de falsificación de solicitudes entre sitios. Para utilizar correctamente este marco, debe realizar los siguientes cambios en la configuración del despachante:

>[!NOTE]
>
>Asegúrese de actualizar los números de regla en los ejemplos siguientes según la configuración existente. Recuerde que los vendedores utilizarán la última regla coincidente para conceder un permiso o denegar, así que coloque las reglas cerca de la parte inferior de la lista existente.

1. En `/clientheaders` la sección de su autor-farm. any y publish-farm. any, agregue la siguiente entrada a la parte inferior de la lista:\
   `CSRF-Token`
1. En la /filters ón de su `author-farm.any` archivo y `publish-farm.any` o en `publish-filters.any` el archivo, agregue la línea siguiente para permitir solicitudes `/libs/granite/csrf/token.json` a través del despachante.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. En `/cache /rules` la sección de su `publish-farm.any`, agregue una regla para bloquear el despachante desde la caché del `token.json` archivo. Normalmente, los autores omiten el almacenamiento en caché, por lo que no es necesario que agregue la regla a su `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Para validar que la configuración funcione, observe el modo dispatcher. log en el modo DEBUG para validar que el archivo token. json no se esté almacenando en caché y que filtros no esté bloqueando. Debería ver mensajes similares a:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

También puede validar que las solicitudes se completen correctamente `access_log`. Las solicitudes de «/libs/granite/csrf/token. json devuelven un código de estado HTTP 200.
