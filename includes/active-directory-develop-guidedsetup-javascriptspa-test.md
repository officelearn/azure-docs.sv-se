---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 494f5fb6f2bfdec86cad01a37e57c3ec219a77f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133740"
---
## <a name="test-your-code"></a>Testa koden

Testa din kod med någon av följande miljöer.

### <a name="test-with-nodejs"></a>Testa med Node.js

Om du inte använder Visual Studio, kontrollera att servern har startats.

1. Konfigurera servern att lyssna på en TCP-port som baseras på platsen för din *index.html* fil. Starta webbservern för att lyssna på porten genom att köra följande kommandon i Kommandotolken från programmappen för Node.js:

    ```bash
    npm install
    node server.js
    ```
1. Ange i din webbläsare **http://\<span >\</span > localhost:30662** eller **http://\<span >\</span > localhost: {port}** , där *port* är den port som servern lyssnar på. Du bör se innehållet i din *index.html* fil och **logga In** knappen.

### <a name="test-with-visual-studio"></a>Testa med Visual Studio

Om du använder Visual Studio väljer projekt-lösning och välj sedan F5 för att köra projektet. I webbläsaren öppnas http://<span></span>localhost: {port}-plats, och **logga In** knappen ska visas.

## <a name="test-your-application"></a>Testa ditt program

När webbläsaren läser du in din *index.html* filen, Välj **logga In**. Du uppmanas att logga in med Microsoft identity-plattformen slutpunkten:

![JavaScript SPA konto logga in fönstret](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Ge medgivande för programåtkomst

Första gången du loggar in på ditt program, uppmanas du att ge åtkomst till din profil och logga in:

![Fönstret ”begärt behörigheter”](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visa program resultat

När du har loggat in returneras din användarprofilsinformation i Microsoft Graph API-svaret som visas på sidan.

![Resultat från Microsoft Graph API-anrop](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den *user.read* omfattning att läsa en användares profil. Det här omfånget läggs automatiskt som standard i alla program som är registrerat på portalen för registrering. Andra API: er för Microsoft Graph, samt anpassade API: er för backend-servern, kan kräva ytterligare scope. Till exempel Microsoft Graph API kräver den *Calendars.Read* omfattning att lista användarens kalendrar.

Om du vill få åtkomst till användarkalendrar i kontexten för ett program måste du lägga till den *Calendars.Read* delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den *Calendars.Read* begränsa omfånget till den `acquireTokenSilent` anropa.

>[!NOTE]
>Användaren uppmanas för ytterligare medgivanden när du ökar antalet omfång.

Om en serverdels-API inte kräver ett omfång (rekommenderas inte), kan du använda den *clientId* som omfång i anrop till hämta token.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
