---
title: Hantera token (Microsoft Authentication Library) | Azure
description: Läs mer om införskaffa och cachelagring token med Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d32b56b28d9ce7425e782fc10fa9ffb67047ce0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139507"
---
# <a name="acquiring-and-caching-tokens-using-msal"></a>Hämtar och cachelagring token med MSAL
[Åtkomsttoken](access-tokens.md) att klienterna på ett säkert sätt anropa webb-API: er som skyddas av Azure. Det finns många sätt att hämta en token med hjälp av Microsoft Authentication Library (MSAL). Några sätt kräver användarinteraktioner via en webbläsare. Vissa kräver inte någon användarinteraktioner. I allmänhet beror sätt att hämta en token på om programmet är en offentlig klient (desktop eller mobile app) eller en konfidentiell klientprogram (Web App, webb-API eller daemon-program som en Windows-tjänst).

MSAL cachelagrar en token när du har införskaffat.  Programkod bör försöka att hämta en token tyst (från cachen) först innan du skaffa en token som på annat sätt.

Du kan även radera token cacheminne, vilket gör detta genom att ta bort konton från cachen. Sessions-cookie som är i webbläsaren, men tas inte bort.

## <a name="scopes-when-acquiring-tokens"></a>Scope när du hämtar token
[Scope](v2-permissions-and-consent.md) är de behörigheter som exponerar ett webb-API för klientprogram att begära åtkomst till. Klientprogram begär användarens medgivande för dessa scope vid autentiseringsbegäranden att hämta token för att få åtkomst till webb-API: er. MSAL kan du hämta token komma åt Azure AD för utvecklare (v1.0) och Microsoft identity-plattformen (v2.0) API: er. v2.0-protokollet använder omfång i stället för resurs i begäranden. Mer information finns i [jämförelse mellan v1.0 och v2.0](active-directory-v2-compare.md). V2.0-slutpunkten returnerar baserat på webb-API: er konfiguration av token version godtas, den åtkomst-token till MSAL.

Ett antal MSAL hämta token metoder kräver en *scope* parametern. Den här parametern är en enkel lista med strängar som deklarerar önskade behörigheter och resurser som har begärts. Välkända scope är de [Microsoft Graph-behörigheter](/graph/permissions-reference).

Det är också möjligt i MSAL för resursåtkomst v1.0. Mer information finns i [omfång för ett program med v1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Begär specifika omfång för ett webb-API
När programmet behöver för att begära token med särskilda behörigheter för en resurs API, behöver du skicka scope som innehåller app-ID-URI för API: et i i formatet nedan:  *&lt;app-ID-URI&gt; / &lt;omfång&gt;*

Till exempel omfång för Microsoft Graph API: `https://graph.microsoft.com/User.Read`

Alternativt kan du till exempel omfång för ett anpassat webb-API: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

För Microsoft Graph API, scope-värde `user.read` mappar till `https://graph.microsoft.com/User.Read` formatera och är utbytbara.

> [!NOTE]
> Vissa webb-API: er, till exempel Azure Resource Manager API (https://management.core.windows.net/) förväntar sig en avslutande ' /' anspråk (aud) för åtkomsttoken i målgruppen. I det här fallet är det viktigt att skicka omfattningen som https://management.core.windows.net//user_impersonation (Observera att dubbla snedstreck) för ska vara giltigt i API-token.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Begäran om dynamiska omfång för inkrementell medgivande
När du skapar program med hjälp av v1.0 var du tvungen att registrera den fullständiga uppsättningen behörigheter (statiska omfång) som krävs av programmet för om användarens tillstånd vid tidpunkten för inloggningen. I version 2.0, kan du begära ytterligare behörighet efter behov med hjälp av parametern omfång. Dessa kallas för dynamiska omfång och Tillåt användaren att ange inkrementell medgivande till omfång.

Du kan först loggar in användaren och avvisa alla slags åtkomst. Senare, kan du ge dem möjlighet att läsa kalendern för användaren genom att begära området kalender hämta token metoderna och få användarens samtycke.

Till exempel: `https://graph.microsoft.com/User.Read` och `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Hämta token tyst (från cachen)
MSAL underhåller en token-cache (eller två cacheminnen för klientprogram som konfidentiell) och cachelagrar en token när du har införskaffat.  I många fall skaffar försöker hämta en token tyst en annan token med fler områden baserat på en token i cacheminnet. Det kan också uppdatera en token när den får nära förfallodatum (eftersom token cachen innehåller också en uppdateringstoken).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Rekommenderade anrop mönster för offentliga klientprogram
Programkod bör försöka att hämta en token tyst (från cachen) först.  Om metodanropet returnerar ett ”Användargränssnittet krävs” fel eller undantag, kan du försöka skaffa en token som på annat sätt. 

Men det finns två flöden som du **bör inte** försöker hämta en token tyst:

- [flöde för klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials), som inte använder token-cache för användaren, men en token cacheminne. Den här metoden hand tar om verifierar den här token cacheminne innan du skickar en begäran till STS.
- [auktoriseringskodflödet](msal-authentication-flows.md#authorization-code) i Web Apps, som det redeems en kod som programmet har fått genom att logga in användaren så att de medgivande för flera omfång. Eftersom en kod skickas som en parameter, inte ett konto, metoden kan inte kontrollera i cachen innan du löser in den kod som kräver ändå ett anrop till tjänsten.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Rekommenderat mönster för anrop i Web Apps med auktoriseringskod flödet 
För webbprogram som använder den [OpenID Connect-auktoriseringskodflöde](v2-protocols-oidc.md), rekommenderade mönster i styrenheterna är att:

- Skapa en instans av ett konfidentiellt klientprogram med en tokencache med anpassad serialisering. 
- Hämta token med hjälp av auktoriseringskodsflödet

## <a name="acquiring-tokens"></a>Hämta token
I allmänhet beror metoden för att skaffa en token på om det är en offentlig klient eller konfidentiell klientprogram.

### <a name="public-client-applications"></a>Offentliga klientprogram
För offentliga klientprogram (desktop eller mobile app), du:
- Ofta hämta token interaktivt, låta användaren logga in via ett användargränssnitt eller popup-fönstret.
- Kan [tyst får en token för den inloggade användaren](msal-authentication-flows.md#integrated-windows-authentication) med hjälp av integrerad Windows-autentisering (IWA/Kerberos) om programmet körs på en Windows-dator ansluten till en domän eller till Azure.
- Kan [hämta en token med ett användarnamn och lösenord](msal-authentication-flows.md#usernamepassword) i .NET framework-skrivbordsklienten program, men detta rekommenderas inte. Använd inte användarnamn/lösenord i konfidentiell klientprogram.
- Hämta en token via den [kodflöde för enheten](msal-authentication-flows.md#device-code) i program som körs på enheter som inte har en webbläsare. Användaren får med en URL och en kod som sedan går till en webbläsare på en annan enhet och anger kod och loggar in.  Sedan Azure AD skickar en token tillbaka till webbläsaren utan enheten.

### <a name="confidential-client-applications"></a>Konfidentiellt klientprogram 
För känsliga program (Web App, webb-API eller daemon-program som en Windows-tjänst), du:
- Hämta token **för själva programmet** och inte för en användare med hjälp av den [flödet för klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials). Detta kan användas för synkronisering verktyg eller verktyg som bearbetar användare i allmänhet och inte en viss användare. 
- Använd den [On-behalf-of-flöde](msal-authentication-flows.md#on-behalf-of) för ett webb-API för att anropa ett API för användarens räkning. Programmet identifieras med klientens autentiseringsuppgifter för att hämta en token baserat på en användare assertion (SAML till exempel eller en JWT-token). Det här flödet används av program som behöver åtkomst till resurser för en viss användare i tjänst-till-tjänst-anrop.
- Hämta token med hjälp av den [auktoriseringskodflödet](msal-authentication-flows.md#authorization-code) i web apps när användaren loggar in via auktoriseringen URL för begäran. OpenID Connect-program använda den här mekanism som gör att användare loggar in med öppna ID connect och åtkomst webb-API: er för användarens räkning.


## <a name="authentication-results"></a>Autentisering-resultat 
När klienten begär en åtkomsttoken, returnerar ett autentiseringsresultat som innehåller vissa metadata om åtkomsttoken också i Azure AD. Informationen omfattar förfallotiden för åtkomst-token och de omfattningar som den är giltig. Dessa data kan din app att göra intelligent cachelagring av åtkomsttoken utan att parsa den åtkomst-token.  Autentiseringresultatet visar:

- Den [åtkomsttoken](access-tokens.md) för webb-API för åtkomst till resurser. Detta är en sträng, vanligtvis en base64-kodade JWT men klienten aldrig bör se ut i åtkomsttoken. Formatet är inte garanterad är stabil och den vara krypterad för resursen. Personer skriva kod beroende på åtkomst-token innehåll på klienten är en av de största källorna av fel och klienten logic sidbrytningar.
- Den [ID-token](id-tokens.md) för användaren (detta är en JWT).
- Token upphör att gälla kan som talar om datum och tid när token upphör att gälla.
- Klient-ID innehåller klienten där användaren hittades. Klient-ID är gäst-klient, inte unikt klient för gästanvändare (Azure AD B2B-scenarier). När token levereras namnet på en användare, innehåller Autentiseringresultatet även information om den här användaren. För konfidentiell klient flöden där token begärs utan användare (för program), är den här användarinformationen null.
- Scope som token utfärdats.
- Unikt ID för användaren.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [hantering av fel och undantag](msal-handling-exceptions.md). 
