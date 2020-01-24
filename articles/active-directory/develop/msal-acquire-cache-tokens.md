---
title: Hämta &-cache-token med MSAL | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att förvärva och cachelagra token med hjälp av Microsoft Authentication Library (MSAL).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 9a9dda1bba4d587881d32d937fa0e20b68a5b383
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696579"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Hämta och cachelagra token med Microsoft Authentication Library (MSAL)

[Åtkomsttoken gör det](access-tokens.md) möjligt för klienter att på ett säkert sätt anropa webb-API: er som skyddas av Azure. Det finns många sätt att hämta en token med hjälp av Microsoft Authentication Library (MSAL). På vissa sätt krävs användar interaktioner via en webbläsare. Vissa kräver ingen interaktion från användaren. I allmänhet beror det på hur du kan hämta en token på om programmet är ett offentligt klient program (stationär eller mobilapp) eller ett konfidentiellt klient program (webbapp, webb-API eller daemon-program som en Windows-tjänst).

MSAL cachelagrar en token efter att den har hämtats.  Program koden bör försöka hämta en token tyst (från cachen), först innan du hämtar en token på annat sätt.

Du kan också rensa token-cachen, som uppnås genom att ta bort kontona från cachen. Detta tar inte bort sessions-cookien som finns i webbläsaren, men.

## <a name="scopes-when-acquiring-tokens"></a>Omfattningar vid hämtning av token

[Omfattningar](v2-permissions-and-consent.md) är de behörigheter som ett webb-API visar för klient program för att begära åtkomst till. Klient program begär användarens medgivande för dessa omfattningar när de gör autentiseringsbegäranden för att hämta token för att få åtkomst till webb-API: er. Med MSAL kan du hämta token för att få åtkomst till Azure AD för utvecklare (v 1.0) och API: er för Microsoft Identity Platform (v 2.0). v 2.0-protokollet använder omfång i stället för resurs i begär Anden. Mer information finns i [jämförelse mellan v 1.0 och v 2.0](active-directory-v2-compare.md). Baserat på webb-API: ns konfiguration av den token-version som den accepterar, returnerar v 2.0-slutpunkten åtkomsttoken till MSAL.

Ett antal MSAL-metoder för att hämta token kräver en *omfattnings* parameter. Den här parametern är en enkel lista med strängar som deklarerar önskade behörigheter och resurser som begärs. Välkända omfattningar är [Microsoft Graph behörigheter](/graph/permissions-reference).

Det går också att komma åt v 1.0-resurser med MSAL. Mer information finns i avsnittet [om omfång för ett v 1.0-program](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Begär speciella omfattningar för ett webb-API

När ditt program behöver begära tokens med specifika behörigheter för ett resurs-API måste du skicka de omfattningar som innehåller app-ID-URI: n för API: et i formatet nedan: *&lt;app-ID-uri&gt;/&lt;omfånget&gt;*

Exempel: scope för Microsoft Graph API: `https://graph.microsoft.com/User.Read`

Eller till exempel omfång för ett anpassat webb-API: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

För Microsoft Graph-API: t är det bara ett omfångs värde `user.read` mappar till `https://graph.microsoft.com/User.Read`-format och kan användas utbytbart.

> [!NOTE]
> Vissa webb-API: er som Azure Resource Manager API (https://management.core.windows.net/) förväntar sig ett efterföljande "/" i AUD-åtkomsttoken. I det här fallet är det viktigt att skicka omfånget som https://management.core.windows.net//user_impersonation (Observera det dubbla snedstrecket) för att token ska vara giltig i API: et.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Begär dynamiska omfattningar för stegvist godkännande

När du skapar program med hjälp av v 1.0 var du tvungen att registrera en fullständig uppsättning behörigheter (statiska omfattningar) som krävs av programmet för att användaren ska kunna godkänna vid inloggnings tillfället. I v 2.0 kan du begära ytterligare behörigheter efter behov med hjälp av omfattnings parametern. Dessa kallas dynamiska omfattningar och tillåter att användaren ger ett stegvist tillstånd för omfattningar.

Du kan till exempel först logga in användaren och neka alla typer av åtkomst. Senare kan du ge dem möjlighet att läsa användarens kalender genom att begära kalender omfånget i metoderna för att hämta token och få användarens medgivande.

Till exempel: `https://graph.microsoft.com/User.Read` och `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Hämtar token tyst (från cachen)

MSAL upprätthåller ett token-cache (eller två cacheminnen för konfidentiella klient program) och cachelagrar en token efter att den har hämtats.  I många fall kommer ett försök att hämta en token att hämta en annan token med fler omfång baserat på en token i cacheminnet. Du kan också uppdatera en token när den snart upphör att gälla (eftersom token cache också innehåller en uppdateringstoken).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Rekommenderat anrops mönster för offentliga klient program

Program koden bör försöka hämta en token tyst (från cachen), först.  Om metod anropet returnerar ett "gränssnitt som krävs" fel eller undantag, försök att förvärva en token på annat sätt. 

Det finns dock två flöden innan du **inte bör** försöka hämta en token tyst:

- [flöde](msal-authentication-flows.md#client-credentials)för klientautentiseringsuppgifter, som inte använder användartoken, men en programtoken-cache. Den här metoden tar hand om verifieringen av det här programtoken-cacheminnet innan en begäran skickas till STS.
- [flöde för auktoriseringskod](msal-authentication-flows.md#authorization-code) i Web Apps, eftersom det löser in en kod som programmet fick genom att logga in användaren, och har fått tillstånd för fler omfång. Eftersom en kod skickas som en parameter, och inte ett konto, kan metoden inte söka i cachen innan den löser in koden, vilket kräver att det ändå är ett anrop till tjänsten.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Rekommenderat anrops mönster i Web Apps att använda kod flödet för auktorisering

För webb program som använder [OpenID Anslut auktoriseringskod](v2-protocols-oidc.md), är det rekommenderade mönstret i styrenheterna att:

- Instansiera ett konfidentiellt klient program med token cache med anpassad serialisering. 
- Hämta token med hjälp av Authorization Code Flow

## <a name="acquiring-tokens"></a>Hämtar token

Metoden för att förvärva en token är vanligt vis beroende av om det är en offentlig klient eller ett konfidentiellt klient program.

### <a name="public-client-applications"></a>Offentliga klient program

För offentliga klient program (skriv bord eller mobilapp):
- Kan ofta hämta token interaktivt, där användaren loggar in via ett användar gränssnitt eller popup-fönster.
- Kan [Hämta en token tyst för den inloggade användaren](msal-authentication-flows.md#integrated-windows-authentication) med hjälp av integrerad Windows-autentisering (IWA/Kerberos) om Skriv bords programmet körs på en Windows-dator som är ansluten till en domän eller till Azure.
- Kan [Hämta en token med ett användar namn och lösen ord](msal-authentication-flows.md#usernamepassword) i .NET Framework Desktop Client-program, men det rekommenderas inte. Använd inte användar namn/lösen ord i konfidentiella klient program.
- Kan hämta en token via [enhets kod flödet](msal-authentication-flows.md#device-code) i program som körs på enheter som saknar webbläsare. Användaren får en URL och en kod som sedan går till en webbläsare på en annan enhet och anger koden och loggar in.  Azure AD skickar sedan en token tillbaka till webbläsaren-mindre enhet.

### <a name="confidential-client-applications"></a>Konfidentiella klient program

För konfidentiella klient program (webb program, webb-API eller daemon-program som en Windows-tjänst):
- Hämta token **för själva programmet** och inte för en användare med hjälp av [flödet för klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials). Detta kan användas för att synkronisera verktyg, eller verktyg som behandlar användare i allmänhet och inte en speciell användare. 
- Använd [flödet på uppdrags nivå](msal-authentication-flows.md#on-behalf-of) för ett webb-API för att anropa ett API för användarens räkning. Programmet identifieras med klientens autentiseringsuppgifter för att hämta en token baserat på en användar kontroll (SAML till exempel eller en JWT-token). Det här flödet används av program som behöver åtkomst till resurser för en viss användare i tjänst-till-tjänst-anrop.
- Hämta token med hjälp av [auktoriseringskod-flödet](msal-authentication-flows.md#authorization-code) i webbappar när användaren loggar in via URL: en för begäran om auktorisering. OpenID Connect Application använder vanligt vis den här mekanismen, vilket gör att användaren kan logga in med öppen ID Connect och sedan få åtkomst till webb-API: er för användarens räkning.

## <a name="authentication-results"></a>Verifierings resultat

När klienten begär en åtkomsttoken, returnerar Azure AD även ett verifierings resultat som innehåller vissa metadata om åtkomst-token. Den här informationen omfattar förfallo tiden för åtkomsttoken och de omfattningar som den är giltig för. Med den här informationen kan din app utföra intelligent cachelagring av åtkomsttoken utan att behöva parsa åtkomsttoken.  Resultatet av autentiseringen visar:

- Åtkomsttoken [för webb](access-tokens.md) -API: et för att få åtkomst till resurser. Detta är en sträng, vanligt vis en Base64-kodad JWT men klienten bör aldrig titta inuti åtkomsttoken. Formatet är inte garanterat vara stabilt och kan krypteras för resursen. Användare som skriver kod beroende på åtkomsttoken på klienten är en av de största källorna till fel och klient logiks avbrott.
- Användarens [ID-token](id-tokens.md) (detta är en JWT).
- Förfallo tiden för token, som anger det datum/tid när token upphör att gälla.
- Klient-ID: t innehåller innehavaren där användaren hittades. För gäst användare (Azure AD B2B-scenarier) är klient-ID: t gäst klienten, inte den unika klient organisationen. När token levereras i namnet på en användare innehåller verifierings resultatet även information om den här användaren. För konfidentiella klient flöden där tokens begärs utan användare (för programmet) är denna användar information null.
- De omfattningar som token utfärdades för.
- Unikt ID för användaren.

## <a name="next-steps"></a>Nästa steg

Om du använder MSAL för Java kan du läsa mer om [anpassad cachelagring av token i MSAL för Java](msal-java-token-cache-serialization.md).

Lär dig mer om att [hantera fel och undantag](msal-handling-exceptions.md).
