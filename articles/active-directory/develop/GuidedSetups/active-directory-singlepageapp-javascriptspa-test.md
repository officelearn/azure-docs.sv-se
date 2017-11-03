---
title: Azure AD v2 JS SPA interaktiv installation - Test | Microsoft Docs
description: "Hur JavaScript SPA program anropar en API som kräver åtkomst-token i Azure Active Directory v2 slutpunkten"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: c888760ab311e8ac08b1e625bb837f91047db645
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>Testa din kod

> ### <a name="testing-with-visual-studio"></a>Testa med Visual Studio
> Om du använder Visual Studio trycker du på `F5` köra projektet: webbläsaren öppnas och dirigerar dig till *http://localhost: {port}* där du ser den *anropa Microsoft Graph API* knappen.

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>Testa med Python eller en annan webbserver
> Om du inte använder Visual Studio, kontrollera att servern har startats och den är konfigurerad för att lyssna på en TCP-port baserat på den mapp som innehåller din *index.html* fil. För Python, du kan börja lyssna på porten genom att köra det i kommandot fråga / terminal, från appens mapp:
> 
> ```bash
> python -m http.server 8080
> ```
>  Öppna webbläsaren och skriv sedan *http://localhost: 8080* eller *http://localhost: {port}* - där den *port* motsvarar den port som servern lyssnar på. Du bör se innehållet i index.html sidan med de *anropa Microsoft Graph API* knappen.

## <a name="test-your-application"></a>Testa programmet

När webbläsaren läser du in din *index.html*, klicka på den *anropa Microsoft Graph API* knappen. Om det här är första gången omdirigerar dig till Microsoft Azure Active Directory v2 slutpunkten, där du uppmanas att logga in i webbläsaren.
 
![Exempel skärmbild](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Medgivande
Första gången du loggar in på ditt program, visas en medgivande skärm som liknar följande, där du måste acceptera:

 ![Medgivande skärmen](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>Förväntat resultat
Du bör se användarens profilinformation som returneras av svar för Microsoft Graph API-anrop.
 
 ![Resultat](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

Du också se grundläggande information om token har införskaffade i den *åtkomst-Token* och *ID Token anspråk* rutor.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den `user.read` omfång att läsa användarens profil. Detta scope läggs automatiskt som standard i alla program som registreras på vår registreringsportal. Vissa andra API: er för Microsoft Graph samt anpassade API: er för backend-servern kan kräva ytterligare scope. Till exempel för Microsoft Graph omfånget `Calendars.Read` krävs för att visa en lista med användarens kalendrar. För att komma åt användarens kalender i kontexten för ett program måste du lägga till den `Calendars.Read` delegerad behörighet att programmet registreringsinformation och Lägg sedan till den `Calendars.Read` omfånget för den `acquireTokenSilent` anropa. Användaren kan uppmanas att ytterligare medgivanden när du ökar antalet scope.

Om en serverdel API inte kräver ett omfång (rekommenderas inte), kan du använda den `clientId` som scope i den `acquireTokenSilent` och/eller `acquireTokenRedirect` anrop.

<!--end-collapse-->
