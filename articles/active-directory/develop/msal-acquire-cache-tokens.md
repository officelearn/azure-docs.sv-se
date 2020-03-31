---
title: Hämta & cachetoken med MSAL | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om hur du hämtar och cachelagring av token med hjälp av Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c1f1cbf85b96aade745cc4248aed4bc89e41b450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085161"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Hämta och cacheminnen med hjälp av Microsofts autentiseringsbibliotek (MSAL)

[Åtkomsttoken](access-tokens.md) gör det möjligt för klienter att anropa webb-API:er som skyddas av Azure på ett säkert sätt. Det finns många sätt att hämta en token med Hjälp av Microsoft Authentication Library (MSAL). Vissa sätt kräver användarinteraktioner via en webbläsare. Vissa kräver inga användarinteraktioner. I allmänhet beror sättet att skaffa en token på om programmet är ett offentligt klientprogram (skrivbords- eller mobilapp) eller ett konfidentiellt klientprogram (Web App, Webb-API eller demonprogram som en Windows-tjänst).

MSAL cachelagrar en token efter att den har förvärvats.  Programkod bör försöka få en token tyst (från cacheminnet), först, innan du hämtar en token på annat sätt.

Du kan också rensa tokencachen, som uppnås genom att ta bort kontona från cacheminnet. Detta tar dock inte bort sessionscookien som finns i webbläsaren.

## <a name="scopes-when-acquiring-tokens"></a>Scope vid inlösen av token

[Scope](v2-permissions-and-consent.md) är de behörigheter som ett webb-API exponerar för klientprogram att begära åtkomst till. Klientprogram begär användarens samtycke till dessa scope när du gör autentiseringsbegäranden för att få token för att komma åt webb-API:erna. MSAL kan du få tokens för att komma åt Azure AD för utvecklare (v1.0) och Microsoft Identity Platform (v2.0) API:er. v2.0-protokollet använder scope i stället för resurser i begärandena. För mer information, läs [v1.0 och v2.0 jämförelse](active-directory-v2-compare.md). Baserat på webb-API:ets konfiguration av tokenversionen som accepteras returnerar v2.0-slutpunkten åtkomsttoken till MSAL.

Ett antal MSAL-inhämtning av tokenmetoder kräver en *scopeparameter.* Den här parametern är en enkel lista med strängar som deklarerar önskade behörigheter och resurser som begärs. Välkända scope är [Behörigheterna för Microsoft Graph](/graph/permissions-reference).

Det är också möjligt i MSAL att komma åt v1.0-resurser. Mer information finns i [Scopes för ett v1.0-program](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Begär specifika scope för ett webb-API

När ditt program behöver begära token med specifika behörigheter för ett resurs-API måste du skicka scope som innehåller API:et för appen ID i det följande formatet: * &lt;app-ID&gt;/&lt;URI-scope&gt;*

Till exempel scope för Microsoft Graph API:`https://graph.microsoft.com/User.Read`

Eller till exempel scope för ett anpassat webb-API:`api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Endast för Microsoft Graph API mappas ett scopevärde `user.read` till `https://graph.microsoft.com/User.Read` formatet och kan användas omväxlande.

> [!NOTE]
> Vissa webb-API:er somhttps://management.core.windows.net/) Azure Resource Manager API ( förvänta dig ett avslutande /' i målgruppsanspråket (aud) för åtkomsttoken. I det här fallet är det https://management.core.windows.net//user_impersonation viktigt att skicka scopet som (notera dubbelsnedstrecket), för att token ska vara giltig i API:et.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Begär dynamiska scope för inkrementellt medgivande

När du skapar program som använder v1.0 var du tvungen att registrera den fullständiga uppsättningen behörigheter (statiska scope) som krävs av programmet för att användaren ska godkänna vid inloggningstillfället. I v2.0 kan du begära ytterligare behörigheter efter behov med hjälp av scopeparametern. Dessa kallas dynamiska scope och tillåter användaren att ge inkrementellt medgivande till scope.

Du kan till exempel först logga in användaren och neka dem någon form av åtkomst. Senare kan du ge dem möjlighet att läsa kalendern för användaren genom att begära kalenderomfånget i inhämtningstokenmetoderna och få användarens medgivande.

Till exempel: `https://graph.microsoft.com/User.Read` och`https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Hämta token tyst (från cacheminnet)

MSAL underhåller en tokencache (eller två cacheminnen för konfidentiella klientprogram) och cachelagrar en token efter att den har förvärvats.  I många fall, försöker tyst få en token kommer att få en annan token med fler scope baserat på en token i cachen. Det kan också uppdatera en token när den närmar sig förfallodatum (eftersom tokencachen också innehåller en uppdateringstoken).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Rekommenderat samtalsmönster för offentliga klientprogram

Programkod bör försöka få en token tyst (från cacheminnet), först.  Om metodanropet returnerar ett "UI required" fel eller undantag kan du prova att hämta en token på annat sätt. 

Det finns dock två flöden innan du **inte bör** försöka att tyst hämta en token:

- [klientautentiseringsflödet](msal-authentication-flows.md#client-credentials), som inte använder användartokencache, men en programtokencache. Den här metoden tar hand om att verifiera den här programtokencachen innan du skickar en begäran till STS.
- [auktoriseringskodflödet](msal-authentication-flows.md#authorization-code) i Web Apps, eftersom det löser in en kod som programmet fick genom att logga in användaren och ge dem samtycke till fler scope. Eftersom en kod skickas som en parameter och inte ett konto, kan metoden inte leta i cacheminnet innan koden löses in, vilket i alla fall kräver ett anrop till tjänsten.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Rekommenderat samtalsmönster i Webbappar med hjälp av flödet Auktoriseringskod

För webbprogram som använder [openid connect-auktoriseringskodflödet](v2-protocols-oidc.md)är det rekommenderade mönstret i styrenheterna att:

- Instansiera ett konfidentiellt klientprogram med en tokencache med anpassad serialisering. 
- Hämta token med hjälp av auktoriseringskodflödet

## <a name="acquiring-tokens"></a>Skaffa token

I allmänhet beror metoden för att skaffa en token på om det är en offentlig klient eller ett konfidentiellt klientprogram.

### <a name="public-client-applications"></a>Program för offentliga klienter

För offentliga klientprogram (dator- eller mobilapp) kan du:
- Ofta förvärva tokens interaktivt, med användaren logga in via ett användargränssnitt eller popup-fönster.
- Kan få en token tyst för den inloggade användaren med integrerad Windows-autentisering (IWA/Kerberos) om skrivbordsprogrammet körs på en [Windows-dator](msal-authentication-flows.md#integrated-windows-authentication) som är ansluten till en domän eller till Azure.
- Kan [hämta en token med ett användarnamn och lösenord](msal-authentication-flows.md#usernamepassword) i .NET framework desktop-klientprogram, men detta rekommenderas inte. Använd inte användarnamn/lösenord i konfidentiella klientprogram.
- Kan hämta en token via [enhetskodflödet](msal-authentication-flows.md#device-code) i program som körs på enheter som inte har en webbläsare. Användaren får en WEBBADRESS och en kod, som sedan går till en webbläsare på en annan enhet och anger koden och loggar in.  Azure AD skickar sedan en token tillbaka till den webbläsarlösa enheten.

### <a name="confidential-client-applications"></a>Konfidentiella klientprogram

För konfidentiella klientprogram (Web App, Web API eller daemon-program som en Windows-tjänst) kan du:
- Hämta token **för själva programmet** och inte för en användare, med hjälp av [klientautentiseringsflödet](msal-authentication-flows.md#client-credentials). Detta kan användas för synkroniseringsverktyg eller verktyg som bearbetar användare i allmänhet och inte en specifik användare. 
- Använd [flödet för on-behalf](msal-authentication-flows.md#on-behalf-of) för ett webb-API för att anropa ett API för användarens räkning. Programmet identifieras med klientautentiseringsuppgifter för att hämta en token baserat på ett användarpåstående (SAML till exempel eller en JWT-token). Det här flödet används av program som behöver komma åt resurser för en viss användare i service-to-service-anrop.
- Hämta token med hjälp av [auktoriseringskodflödet](msal-authentication-flows.md#authorization-code) i webbappar när användaren loggar in via URL:en för auktoriseringsbegäran. OpenID Connect-programmet använder vanligtvis den här mekanismen, som gör att användaren kan logga in med Öppna ID-anslutning och sedan komma åt webb-API:er för användarens räkning.

## <a name="authentication-results"></a>Autentiseringsresultat

När klienten begär en åtkomsttoken returnerar Azure AD också ett autentiseringsresultat som innehåller vissa metadata om åtkomsttoken. Den här informationen omfattar utgångstiden för åtkomsttoken och de scope som den är giltig för. Dessa data gör att din app kan göra intelligent cachelagring av åtkomsttoken utan att behöva tolka själva åtkomsttoken.  Autentiseringsresultatet exponerar:

- [Åtkomsttoken](access-tokens.md) för webb-API:et för åtkomst till resurser. Detta är en sträng, vanligtvis en base64 kodade JWT men klienten bör aldrig titta inuti åtkomsttoken. Formatet är inte garanterat att förbli stabilt och det kan krypteras för resursen. Personer som skriver kod beroende på åtkomsttokeninnehåll på klienten är en av de största felkällorna och klientlogikens avbrott.
- [ID-token](id-tokens.md) för användaren (detta är en JWT).
- Token förfaller, som talar om datum/tid när token upphör att gälla.
- Klient-ID:t innehåller klienten där användaren hittades. För gästanvändare (Azure AD B2B-scenarier) är klient-ID gästklienten, inte den unika klienten. När token levereras i en användares namn innehåller autentiseringsresultatet också information om den här användaren. För konfidentiella klientflöden där token begärs utan användare (för programmet) är den här användarinformationen null.
- De scope som token utfärdades för.
- Det unika ID:t för användaren.

## <a name="next-steps"></a>Nästa steg

Om du använder MSAL för Java kan du läsa om serialisering av [anpassad tokencache i MSAL för Java](msal-java-token-cache-serialization.md).

Läs mer om [hur du hanterar fel och undantag](msal-handling-exceptions.md).
