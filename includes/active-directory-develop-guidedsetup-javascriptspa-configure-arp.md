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
ms.openlocfilehash: eead4c6a66a317c7404205415cbf04c442ffe8d1
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060716"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Lägg till programmets registreringsinformation i din App

I det här steget måste du konfigurera omdirigerings-URL för din registreringsinformation för programmet och sedan lägga till program-ID i JavaScript SPA-programmet.

### <a name="configure-redirect-url"></a>Konfigurera omdirigerings-URL

Konfigurera den `Redirect URL` fältet med URL-Adressen för din index.html-sida baserat på din webbserver och klicka sedan på *uppdatering*.


> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio-instruktionerna för att hämta omdirigerings-URL
> Följ dessa steg för att hämta omdirigerings-URL:
> 1.    I **Solution Explorer**, väljer du projektet och titta på den **egenskaper** fönster. Om du inte ser en **egenskaper** fönster, tryck på **F4**.
> 2.    Kopiera värdet från **URL** till Urklipp:<br/> ![Projektegenskaper](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Klistra in värdet som en **omdirigerings-URL** överst i den här sidan, klicka sedan på **Update**

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Inställningen omdirigerings-URL för nod
> För Node.js, du kan ange webb-serverport i den *server.js* fil. Den här självstudien används port 30662 referens, men du kan använda någon annan port. Följ anvisningarna nedan för att ställa in en omdirigerings-URL i registreringsinformation för programmet:<br/>
> Ange `http://localhost:30662/` som en **omdirigerings-URL** överst i den här sidan eller Använd `http://localhost:[port]/` om du använder en anpassad TCP-port (där *[port]* är anpassade TCP-portnumret) och klicka sedan på  **Uppdatering**

### <a name="configure-your-javascript-spa-application"></a>Konfigurera ditt JavaScript SPA-program

1.  I den `index.html` filen som skapades under projektkonfiguration, lägga till registreringsinformationen som programmet. Lägg till följande kod högst upp i den `<script></script>` taggar i brödtexten i din `index.html` fil:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
