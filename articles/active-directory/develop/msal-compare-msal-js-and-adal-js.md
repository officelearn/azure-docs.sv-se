---
title: Skillnader mellan MSAL.js och ADAL.js | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om skillnaderna mellan Microsoft Authentication Library for JavaScript (MSAL.js) och Azure AD Authentication Library for JavaScript (ADAL.js) och hur du väljer vilken du vill använda.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 7238a78279528b4522d09178d00bf916f14bad88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696426"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Skillnader mellan MSAL JS och ADAL JS

Både Microsoft Authentication Library for JavaScript (MSAL.js) och Azure AD Authentication Library for JavaScript (ADAL.js) används för att autentisera Azure AD-entiteter och begära token från Azure AD. Hittills har de flesta utvecklare arbetat med Azure AD för utvecklare (v1.0) för att autentisera Azure AD-identiteter (arbets- och skolkonton) genom att begära token med ADAL. Nu kan du använda MSAL.js en bredare uppsättning Microsoft-identiteter (Azure AD-identiteter och Microsoft-konton samt sociala och lokala konton via Azure AD B2C) via Microsoft identity platform (v2.0).

I den här artikeln beskrivs hur du väljer mellan Microsoft Authentication Library for JavaScript (MSAL.js) och Azure AD Authentication Library for JavaScript (ADAL.js) och jämför de två biblioteken.

## <a name="choosing-between-adaljs-and-msaljs"></a>Välja mellan ADAL.js och MSAL.js

I de flesta fall vill du använda Microsofts identitetsplattform och MSAL.js, som är den senaste generationen microsoft-autentiseringsbibliotek. Med MSAL.js får du token för användare som loggar in på ditt program med Azure AD (arbets- och skolkonton), Microsoft (personliga) konton (MSA) eller Azure AD B2C.

Om du redan är bekant med v1.0-slutpunkten (och ADAL.js) kanske du vill läsa [Vad är annorlunda med v2.0-slutpunkten?](active-directory-v2-compare.md).

Du måste dock fortfarande använda ADAL.js om ditt program behöver logga in användare med tidigare versioner av [Active Directory Federation Services (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Viktiga skillnader i autentisering med MSAL.js

### <a name="core-api"></a>Grundläggande API

* ADAL.js använder [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) som representation av en instans av programmets anslutning till auktoriseringsservern eller identitetsprovidern via en behörighets-URL. Tvärtom är MSAL.js API utformat kring klientprogram för användaragenter (en form av offentligt klientprogram där klientkoden körs i en användaragent, till exempel en webbläsare). Den tillhandahåller `UserAgentApplication` klassen för att representera en instans av programmets autentiseringskontext med auktoriseringsservern. Mer information finns i [Initiera med MSAL.js](msal-js-initializing-client-applications.md).

* I ADAL.js associeras metoderna för att hämta token `AuthenticationContext`med en enda myndighet som anges i . I MSAL.js kan de inhämtningstokensbegäranden ta `UserAgentApplication`andra behörighetsvärden än vad som anges i . Detta gör att MSAL.js att hämta och cacheminnen separat för flera klienter och användarkonton i samma program.

* Metoden för att hämta och förnya token tyst `acquireToken` utan att användaren uppmanas att använda nämns i ADAL.js. I MSAL.js heter `acquireTokenSilent` den här metoden att vara mer beskrivande för den här funktionen.

### <a name="authority-value-common"></a>Myndighetsvärde`common`

I v1.0 tillåter `https://login.microsoftonline.com/common` du användare att logga in med alla Azure AD-konton (för alla organisationer).

I v2.0, `https://login.microsoftonline.com/common` med hjälp av myndigheten, tillåter användare att logga in med alla Azure AD-organisationskonto eller ett Microsoft-personligt konto (MSA). Om du vill begränsa inloggningen till endast Azure AD-konton (samma beteende `https://login.microsoftonline.com/organizations`som med ADAL.js) måste du använda . Mer information finns `authority` i alternativet config i [Initiera med MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Scope för att hämta token
* Omfattning i stället för resursparameter i autentiseringsbegäranden för att hämta token

    v2.0-protokollet använder scope i stället för resurser i begärandena. Med andra ord, när ditt program behöver begära token med behörigheter för en resurs som MS Graph, är skillnaden i värden som skickas till biblioteksmetoderna följande:

    v1.0: resurs\:= https //graph.microsoft.com

    v2.0: scope\:= https //graph.microsoft.com/User.Read

    Du kan begära scope för alla resurs-API med hjälp av URI för API i\/det här formatet: appidURI/scope Till exempel: https: /mytenant.onmicrosoft.com/myapi/api.read

    Endast för MS Graph API `user.read` mappas\/ett scopevärde till https: /graph.microsoft.com/User.Read och kan användas omväxlande.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dynamiska scope för inkrementellt samtycke.

    När du skapar program som använder v1.0, behövde du registrera hela uppsättningen behörigheter (statiska scope) som krävs av programmet för användaren att samtycka till vid tidpunkten för inloggningen. I v2.0 kan du använda scopeparametern för att begära behörigheterna när du vill ha dem. Dessa kallas dynamiska scope. Detta gör det möjligt för användaren att ge inkrementellt samtycke till scope. Så om du i början bara vill att användaren ska logga in på ditt program och du inte behöver någon form av åtkomst, kan du göra det. Om du senare behöver möjlighet att läsa kalendern för användaren kan du sedan begära kalenderomfånget i acquireToken-metoderna och få användarens medgivande. Ett exempel:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Scope för V1.0 API:er

    När du hämtar token för V1.0 API:er med MSAL.js kan du begära `.default` alla statiska scope som registrerats på API:et genom att lägga till till i app-ID-URI för API:et som scope. Ett exempel:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [v1.0 och v2.0-jämförelsen](active-directory-v2-compare.md).
