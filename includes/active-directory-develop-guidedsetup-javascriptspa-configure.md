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
ms.openlocfilehash: 569a7e74df3016fae133066607fdc0bc32c0044d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293744"
---
## <a name="register-your-application"></a>Registrera ditt program

Det finns flera sätt att skapa ett program, Välj en av dem:

### <a name="option-1-register-your-application-express-mode"></a>Alternativ 1: Registrera ditt program (Express-läge)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:

1.  Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)
2.  Ange ett namn för ditt program och din e-post
3.  Kontrollera att alternativet *interaktiva installation* kontrolleras
4.  Följ anvisningarna för att hämta program-ID och klistra in den i din kod

### <a name="option-2-register-your-application-advanced-mode"></a>Alternativ 2: Registrera ditt program (Avancerat läge)

1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app) att registrera ett program
2. Ange ett namn för ditt program och din e-post 
3. Kontrollera att alternativet *interaktiva installation* är avmarkerat
4.  Klicka på `Add Platform`och välj sedan `Web`
5. Lägg till den `Redirect URL` som motsvarar programmets URL baserat på din webbserver. I avsnitten nedan för instruktioner om hur du ange / hämta omdirigerings-URL i Visual Studio och Python.
6. Klicka på *Spara*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio-instruktionerna för att hämta omdirigerings-URL
> Följ anvisningarna för att hämta omdirigerings-URL:
> 1.    I *Solution Explorer*, väljer du projektet och titta på den `Properties` fönstret (om du inte ser en egenskapsfönstret, trycker du på `F4`)
> 2.    Kopiera värdet från `URL` till Urklipp:<br/> ![Projektegenskaper](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Gå tillbaka till den *Programregistreringsportalen* och klistra in värdet som en `Redirect URL` och klicka på ”Spara”

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Inställningen omdirigerings-URL för nod
> För Node.js, du kan ange webb-serverport i den *server.js* fil. Den här självstudien använder port 30662 för referens, men kan använda någon annan port som är tillgängliga. I båda fallen följer du anvisningarna nedan för att ställa in en omdirigerings-URL i registreringsinformation för programmet:<br/>
> - Gå tillbaka till den *Programregistreringsportalen* och ange `http://localhost:30662/` som en `Redirect URL`, eller Använd `http://localhost:[port]/` om du använder en anpassad TCP-port (där *[port]* är den anpassade porten TCP tal) och klicka på ”Spara”


#### <a name="configure-your-javascript-spa"></a>Konfigurera din JavaScript SPA

1.  Skapa en fil med namnet `msalconfig.js` med registreringsinformation för programmet. Om du använder Visual Studio väljer du projektet (rotmappen för projektet), högerklicka och välj: `Add`  >  `New Item`  >  `JavaScript File`. Ge den namnet `msalconfig.js`
2.  Lägg till följande kod till din `msalconfig.js` fil:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Ersätt <code>Enter_the_Application_Id_here</code> med program-Id som du just registrerade
</li>
</ol>
