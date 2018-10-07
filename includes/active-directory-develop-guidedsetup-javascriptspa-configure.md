---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 038ea48bedeb31416627f99b38ebb083846747e4
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843466"
---
## <a name="register-your-application"></a>Registrera ditt program

Det finns flera sätt att skapa ett program, Välj en av dem:

### <a name="option-1-register-your-application-express-mode"></a>Alternativ 1: Registrera ditt program (Express-läge)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:

1.  Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Ange ett namn för ditt program och din e-post.
3.  Kontrollera att alternativet **interaktiva installation** kontrolleras.
4.  Följ anvisningarna för att hämta program-ID och klistra in den i din kod.

### <a name="option-2-register-your-application-advanced-mode"></a>Alternativ 2: Registrera ditt program (Avancerat läge)

1. Gå till [Microsoft-portalen för programregistrering](https://apps.dev.microsoft.com/portal/register-app) för att registrera ett program.
2. Ange ett namn för ditt program och din e-post.
3. Kontrollera att alternativet **interaktiva installation** är avmarkerad.
4.  Klicka på **Lägg till plattform**och välj sedan **Web**.
5. Lägg till den **omdirigerings-URL** som motsvarar programmets URL baserat på din webbserver. Se avsnitten nedan för instruktioner om hur du anger och hämtar omdirigerings-URL i Visual Studio och nod.
6. Välj **Spara**.

> #### <a name="setting-redirect-url-for-node"></a>Inställningen omdirigerings-URL för nod
> För Node.js, du kan ange webb-serverport i den *server.js* fil. Den här självstudien använder port 30662 för referens, men kan använda någon annan port som är tillgängliga. I båda fallen följer du anvisningarna nedan för att ställa in en omdirigerings-URL i registreringsinformation för programmet:<br/>
> - Gå tillbaka till den *Programregistreringsportalen* och ange `http://localhost:30662/` som en `Redirect URL`, eller Använd `http://localhost:[port]/` om du använder en anpassad TCP-port (där *[port]* är den anpassade porten TCP tal) och klicka på ”Spara”

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio-instruktionerna för att hämta omdirigerings-URL
> Följ dessa steg för att hämta omdirigerings-URL:
> 1.    I **Solution Explorer**, väljer du projektet och titta på den **egenskaper** fönster. Om du inte ser en **egenskaper** fönster, tryck på **F4**.
> 2.    Kopiera värdet från **URL** till Urklipp:<br/> ![Projektegenskaper](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Gå tillbaka till den *Programregistreringsportalen* och klistra in värdet som en **omdirigerings-URL** och välj **spara**


#### <a name="configure-your-javascript-spa"></a>Konfigurera din JavaScript SPA

1.  I den `index.html` filen som skapades under projektkonfiguration, lägga till registreringsinformationen som programmet. Lägg till följande kod högst upp i den `<script></script>` taggar i brödtexten i din `index.html` fil:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
<ol start="3">
<li>
Ersätt <code>Enter the application Id here</code> med program-Id som du just registrerade.
</li>
</ol>
