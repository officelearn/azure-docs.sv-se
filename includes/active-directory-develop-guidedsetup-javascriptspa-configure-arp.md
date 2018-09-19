---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: fc06da3b1ad66aa15237a25d2f50374043c860ba
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293523"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Lägg till programmets registreringsinformation i din App

I det här steget måste du konfigurera omdirigerings-URL för din registreringsinformation för programmet och sedan lägga till program-ID i JavaScript SPA-programmet.

### <a name="configure-redirect-url"></a>Konfigurera omdirigerings-URL

Konfigurera den `Redirect URL` fältet med URL-Adressen för din index.html-sida baserat på din webbserver och klicka sedan på *uppdatering*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio-instruktionerna för att hämta omdirigerings-URL
> Du kan hämta omdirigerings-URL:
> 1.    I *Solution Explorer*, väljer du projektet och titta på den `Properties` fönstret (om du inte ser en egenskapsfönstret, trycker du på `F4`)
> 2.    Kopiera värdet från `URL` till Urklipp:<br/> ![Projektegenskaper](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Klistra in värdet som en `Redirect URL` överst i den här sidan, klicka sedan på `Update`

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Inställningen omdirigerings-URL för nod
> För Node.js, du kan ange webb-serverport i den *server.js* fil. Den här självstudien använder port 30662 för referens, men kan använda någon annan port som är tillgängliga. Dock använda följande instruktioner för att ställa in en omdirigerings-URL i programmet registreringsinformation:<br/>
> Ange `http://localhost:30662/` som en `Redirect URL` överst i den här sidan eller Använd `http://localhost:[port]/` om du använder en anpassad TCP-port (där *[port]* är anpassade TCP-portnumret) och klicka sedan på ”Uppdatera”

### <a name="configure-your-javascript-spa-application"></a>Konfigurera ditt JavaScript SPA-program

1.  Skapa en fil med namnet `msalconfig.js` med registreringsinformation för programmet. Om du använder Visual Studio väljer du projektet (rotmappen för projektet), högerklicka och välj: `Add`  >  `New Item`  >  `JavaScript File`. Ge den namnet `msalconfig.js`
2.  Lägg till följande kod till din `msalconfig.js` fil:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
