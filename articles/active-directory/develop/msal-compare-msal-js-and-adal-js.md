---
title: Skillnader mellan MSAL.js och ADAL.js | Azure
description: Läs mer om skillnaderna mellan Microsoft Authentication Library för JavaScript (MSAL.js) och Azure AD Authentication Library för JavaScript (ADAL.js) och hur du väljer att använda.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10b5169d3f06e265b3effa3ec18ad8e4f69959d3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121963"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Skillnader mellan MSAL JS och ADAL JS

Både Microsoft Authentication Library för JavaScript (MSAL.js) och Azure AD Authentication Library för JavaScript (ADAL.js) används för att autentisera Azure AD-entiteter och begära token från Azure AD. Fram tills nu, har de flesta utvecklare arbetat med Azure AD för utvecklare (v1.0) för att autentisera Azure AD-identiteter (arbets- och skolkonton konton) genom att begära token med hjälp av ADAL. Nu kan använder MSAL.js, du autentisera en bredare uppsättning Microsoft-identiteter (Azure AD-identiteter och Microsoft-konton och sociala och lokala konton via Azure AD B2C) via Microsoft identity-plattformen (v2.0).

Den här artikeln beskriver hur du väljer mellan Microsoft Authentication Library för JavaScript (MSAL.js) och Azure AD Authentication Library för JavaScript (ADAL.js) och jämför de två biblioteken.

## <a name="choosing-between-adaljs-and-msaljs"></a>Välja mellan ADAL.js och MSAL.js

I de flesta fall som du vill använda Microsoft identity-plattformen och MSAL.js, vilket är den senaste generationen av Microsoft authentication Library. Med MSAL.js kan du hämta token för användare som loggar in på ditt program med Azure AD (arbets- och skolkonton konton), Microsoft (personlig)-konton (MSA) eller Azure AD B2C.

Om du redan är bekant med v1.0 slutpunkt (och ADAL.js) kanske du vill läsa [vad är skillnaden mellan v2.0-slutpunkten?](active-directory-v2-compare.md).

Du behöver dock fortfarande använda ADAL.js om programmet behöver för att logga in användare med tidigare versioner av [Active Directory Federation Services (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Viktiga skillnader i autentisering med MSAL.js

### <a name="core-api"></a>Core API

* ADAL.js använder [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) som en representation av en instans av ditt programs anslutning till auktoriseringsservern eller identitetsprovider via en URL som utfärdare. Däremot MSAL.js API är utformad kring användaren agent-klientprogram (en form av offentliga klientprogram som klientkoden körs i en användaragent till exempel en webbläsare). Det ger den `UserAgentApplication` klass som representerar en instans av programmets autentiseringskontext med auktoriseringsservern. Mer information finns i [initieras med hjälp av MSAL.js](msal-js-initializing-client-applications.md).

* I ADAL.js, metoder för att hämta token är associerade med en enda utfärdaren har angetts den `AuthenticationContext`. I MSAL.js, hämta tokenbegäranden kan ha olika utfärdare värden än vad som anges i den `UserAgentApplication`. På så sätt kan MSAL.js hämta och cachelagra token separat för flera klienter och användarkonton i samma program.

* Metoden för att hämta och förnya token tyst utan att fråga användare heter `acquireToken` i ADAL.js. I MSAL.js, den här metoden heter `acquireTokenSilent` ska vara mer beskrivande för den här funktionen.

### <a name="authority-value-common"></a>Värdet för `common`

I v1.0, med hjälp av den `https://login.microsoftonline.com/common` myndigheten kommer användarna att logga in med valfri Azure AD-konto (för alla organisationer).

I v2.0, med hjälp av den `https://login.microsoftonline.com/common` utfärdaren kan användarna logga in med valfri Azure AD-organisationskonto eller ett personligt Microsoft-konto (MSA). Om du vill begränsa inloggningen till endast Azure AD-konton (samma beteende som med ADAL.js), måste du använda `https://login.microsoftonline.com/organizations`. Mer information finns i `authority` config-alternativet i [initieras med hjälp av MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Omfång för att hämta token
* Omfång i stället för resursparametern i autentiseringsbegäranden att hämta token

    v2.0-protokollet använder omfång i stället för resurs i begäranden. Med andra ord, när programmet behöver för att begära token med behörigheter för en resurs, till exempel MS Graph, är skillnaden i värden som skickas till biblioteket metoder följande:

    V1.0: resource =https://graph.microsoft.com

    v2.0: omfånget = https://graph.microsoft.com/User.Read

    Du kan begära omfång för alla resurser API med URI: N för API: et i det här formatet: appidURI/omfattningen för till exempel: https:\//mytenant.onmicrosoft.com/myapi/api.read

    Endast för MS Graph API, scope-värde `user.read` mappar till https://graph.microsoft.com/User.Read och är utbytbara.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dynamiska omfång för inkrementell medgivande.

    När du skapar program med hjälp av v1.0 behövs du för att registrera den fullständiga uppsättningen behörigheter (statiska omfång) som krävs av programmet för användaren att godkänna vid tidpunkten för inloggningen. Du kan använda omfattningsparametern i v2.0, för att begära behörigheterna vid tidpunkten som du vill. Dessa kallas dynamiska omfång. På så sätt kan användarna att ange inkrementell medgivande till omfång. Så kan du göra det om du inte behöver någon slags åtkomst, i början du bara vill att användaren att logga in på ditt program. Om du behöver senare möjlighet att läsa kalendern för användaren kan du begära kalender omfattning i acquireToken-metoder och hämta användarens medgivande. Exempel:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Omfång för V1.0 API: er

    När du hämtar token för V1.0 API: er med hjälp av MSAL.js, kan du begära alla statiska omfång som har registrerats på API: et genom att lägga till `.default` till URI: N för App-ID för API: er som omfång. Exempel:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [jämförelse mellan v1.0 och v2.0](active-directory-v2-compare.md).
