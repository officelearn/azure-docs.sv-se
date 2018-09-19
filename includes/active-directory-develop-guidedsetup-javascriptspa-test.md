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
ms.openlocfilehash: 9817e94ba77c83b8620274ba41f9d862b6a5ce6d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293650"
---
## <a name="test-your-code"></a>Testa din kod

### <a name="test-with-visual-studio"></a>Testa med Visual Studio
Om du använder Visual Studio trycker du på **F5** köra projektet. I webbläsaren öppnas http://<span></span>localhost: {portplatsen} och du ser den **anropa Microsoft Graph API** knappen.

<p/><!-- -->

### <a name="test-with-node-or-other-web-server"></a>Testa med noden eller annan webbserver
Om du inte använder Visual Studio, kontrollera att servern har startats. Konfigurera servern att lyssna på en TCP-port som baseras på platsen för din **index.html** fil. Starta webbservern för att lyssna på porten genom att köra följande kommandon i Kommandotolken från programmappen för noden:

```bash
npm install
node server.js
```
Öppna webbläsaren och skriv http://<span></span>localhost:30662 eller http://<span></span>localhost: {port} där **port** är den port som servern lyssnar på. Du bör se innehållet i din index.html-fil och **anropa Microsoft Graph API** knappen.

## <a name="test-your-application"></a>Testa ditt program

När webbläsaren har lästs in index.html-fil, Välj **anropa Microsoft Graph API**. Första gången du kör ditt program, webbläsaren omdirigeras du till Microsoft Azure Active Directory (Azure AD) v2.0-slutpunkten och du uppmanas att logga in:

![Logga in på ditt konto för JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Ge medgivande för programåtkomst

Första gången du loggar in på ditt program, uppmanas du att ge ditt medgivande för att ge programmet åtkomst till din profil och logga in dig på:

![Ge ditt medgivande för programåtkomst](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visa program resultat
När du har loggat in visas din användarprofilsinformation i den **Graph API-anrop svar** box.
 
![Förväntat resultat från Microsoft Graph API-anrop](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Du bör också se grundläggande information om den token som har köpts i den **åtkomsttoken** och **ID Tokenanspråken** rutorna.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den **user.read** omfattning att läsa en användares profil. Det här omfånget läggs automatiskt som standard i alla program som är registrerat på portalen för registrering. Andra API: er för Microsoft Graph, samt anpassade API: er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver den **Calendars.Read** omfattning att lista användarens kalendrar.

Om du vill få åtkomst till användarkalendrar i kontexten för ett program måste du lägga till den **Calendars.Read** delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den **Calendars.Read** begränsa omfånget till den **acquireTokenSilent** anropa. 

>[!NOTE]
>Användaren uppmanas för ytterligare medgivanden när du ökar antalet omfång.

Om en serverdels-API inte kräver ett omfång (rekommenderas inte), kan du använda den **clientId** som detta scope i den **acquireTokenSilent** och **acquireTokenRedirect** anrop.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
