---
title: "Azure AD v2 Android komma igång - testa | Microsoft Docs"
description: "Hur en Android-app kan få en åtkomst-token och anropa API: erna som kräver åtkomst-token från Azure Active Directory v2 slutpunkten eller Microsoft Graph API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 6df64f4820f8409bd8897d5ac24f81bffeeef102
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>Testa din kod

1. Distribuera din kod till din emulatorn.
2. Använd när du är redo att testa Microsoft Azure Active Directory (organisationskonto) eller ett Account (live.com, outlook.com) för att logga in. 

![Exempel skärmbild](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![Logga in](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

### <a name="consent"></a>Medgivande
Första gången en användare loggar in på ditt program, de kommer att visas en medgivande skärm som liknar den nedan, där de måste uttryckligen acceptera: 

![Medgivande](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


### <a name="expected-results"></a>Förväntat resultat
Du bör se resultatet av ett anrop till Microsoft Graph API ”jag” slutpunkt som används till att hämta användarprofil - https://graph.microsoft.com/v1.0/me. En lista över vanliga Microsoft Graph slutpunkter finns [artikel](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den `user.read` omfång att läsa användarens profil. Detta scope läggs automatiskt som standard i alla program som registreras på vår registreringsportal. Vissa andra API: er för Microsoft Graph samt anpassade API: er för backend-servern kan kräva ytterligare scope. Till exempel för Microsoft Graph omfånget `Calendars.Read` krävs för att visa en lista med användarens kalendrar. För att komma åt användarens kalender i en kontext med ett program måste du lägga till den `Calendars.Read` delegerad behörighet att programmet registreringsinformation och Lägg sedan till den `Calendars.Read` omfånget för den `acquireTokenSilentAsync` anropa. Användaren kan uppmanas att ytterligare medgivanden när du ökar antalet scope.

<!--end-collapse-->
