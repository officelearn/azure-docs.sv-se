---
title: "Azure AD v2 iOS komma igång - Test | Microsoft Docs"
description: "Hur iOS (Swift)-program kan anropa ett API som kräver åtkomst-token i Azure Active Directory v2 slutpunkten"
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
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 4a88096d2b0a23708acdbc1798eac528599b4f71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Testa fråga Microsoft Graph API från iOS-program

Tryck på `Command`  +  `R` att köra koden i simulatorn.

![Exempel skärmbild](media/active-directory-mobileanddesktopapp-ios-test/iostestscreenshot.png)

När du är redo att testa trycker du på *'anropa Microsoft Graph API-* och du uppmanas att ange ditt användarnamn och lösenord.

### <a name="consent"></a>Medgivande
Första gången du loggar in på ditt program visas med en medgivande skärm som liknar den nedan, där du måste acceptera explicit:

![Medgivande skärmen](media/active-directory-mobileanddesktopapp-ios-test/iosconsentscreen.png)

### <a name="expected-results"></a>Förväntat resultat
Du bör se användarens profilinformation som returneras av Microsoft Graph API-anrop i den *loggning* avsnitt.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den `user.read` omfång att läsa användarens profil. Detta scope läggs automatiskt som standard i alla program som registreras på vår registreringsportal. Vissa andra API: er för Microsoft Graph samt anpassade API: er för backend-servern kan kräva ytterligare scope. Till exempel för Microsoft Graph omfånget `Calendars.Read` krävs för att visa en lista med användarens kalendrar. För att komma åt användarens kalender i en kontext med ett program måste du lägga till den `Calendars.Read` delegerad behörighet att programmet registreringsinformation och Lägg sedan till den `Calendars.Read` omfånget för den `acquireTokenSilent` anropa. Användaren kan uppmanas att ytterligare medgivanden när du ökar antalet scope.

<!--end-collapse-->



