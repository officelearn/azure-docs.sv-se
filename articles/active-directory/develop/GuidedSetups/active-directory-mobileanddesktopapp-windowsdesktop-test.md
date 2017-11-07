---
title: "Azure AD v2 Windows Desktop komma igång - testa | Microsoft Docs"
description: "Hur Windows Desktop .NET (XAML) program anropar en API som kräver åtkomst-token i Azure Active Directory v2 slutpunkten"
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
ms.openlocfilehash: e9fbfc301fb987c72605f8b16a707513661a1b65
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
## <a name="test-your-code"></a>Testa din kod

För att testa ditt program, trycker du på `F5` köra projektet i Visual Studio. Main-fönster visas:

![Exempel skärmbild](media/active-directory-mobileanddesktopapp-windowsdesktop-test/samplescreenshot.png)

När du är redo att testa klickar du på *anropa Microsoft Graph API* och Använd Microsoft Azure Active Directory (organisationskonto) eller ett Account (live.com, outlook.com) för att logga in. Det är det första gången, visas ett fönster som ber användaren logga in:

![Logga in](media/active-directory-mobileanddesktopapp-windowsdesktop-test/signinscreenshot.png)

### <a name="consent"></a>Medgivande
Första gången du loggar in på ditt program visas med en medgivande skärm som liknar den nedan, där du måste acceptera explicit:

![Medgivande skärmen](media/active-directory-mobileanddesktopapp-windowsdesktop-test/consentscreen.png)

### <a name="expected-results"></a>Förväntat resultat
Du bör se användarens profilinformation som returneras av Microsoft Graph API-anrop på skärmen API-anrop resultat.

Du bör också se grundläggande information om token har köpt `AcquireTokenAsync` eller `AcquireTokenSilentAsync` i rutan Token information:

|Egenskap  |Format  |Beskrivning |
|---------|---------|---------|
|Namn | {Fullständig användarnamn} |Användaren förnamn och efternamn|
|Användarnamn |<span>user@domain.com</span> |Användarnamnet som används för att identifiera användaren|
|Token upphör att gälla |{DateTime}         |Den tid då token upphör att gälla. MSAL kommer förlänga giltighetstiden för dig genom att förnya token vid behov|
|Åtkomst-token |{Sträng}         |Token strängen skickas som skickas till HTTP-begäranden som kräver ett authorization-huvud|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter
Graph API kräver den `user.read` omfång Läs användarprofil. Detta scope läggs automatiskt som standard i alla program som registreras på vår registreringsportal. Vissa andra Graph API: er samt anpassade API: er för backend-servern kräver ytterligare scope. Till exempel för diagram, `Calendars.Read` krävs för att lista användarkalendrar. För att komma åt användarens kalender i en kontext med ett program som du behöver lägga till `Calendars.Read` delegerade programmet registreringsinformation och Lägg sedan till `Calendars.Read` till den `AcquireTokenAsync` anropa. Användare kan uppmanas att ytterligare medgivanden när du ökar antalet scope.

<!--end-collapse-->

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]