---
title: Programtyper som stöds av Azure AD B2C
titleSuffix: Azure AD B2C
description: Lär dig mer om vilka typer av program du kan använda med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e8328db12bde531c2e27936c09247611ff1a3583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190151"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Programtyper som kan användas i Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) stöder autentisering för en mängd moderna programarkitekturer. Alla baseras på standardprotokollen [OAuth 2.0](protocols-overview.md) och [OpenID Connect](protocols-overview.md). I den här artikeln beskrivs de typer av program som du kan skapa, oberoende av vilket språk eller vilken plattform du föredrar. Du får också förståelse för de övergripande scenarierna innan du börjar utveckla program.

Alla program som använder Azure AD B2C måste registreras i din [Azure AD B2C-klient](tutorial-create-tenant.md) med hjälp av [Azure-portalen](https://portal.azure.com/). Ansökningsregistreringsprocessen samlar in och tilldelar värden, till exempel:

* Ett **program-ID** som unikt identifierar ditt program.
* En **svars-URL** som kan användas för att rikta svar tillbaka till ditt program.

Varje begäran som skickas till Azure AD B2C anger ett **användarflöde** (en inbyggd princip) eller en **anpassad princip** som styr beteendet för Azure AD B2C. Med båda principtyperna kan du skapa en mycket anpassningsbar uppsättning användarupplevelser.

Interaktionen för alla program följer ett liknande övergripande mönster:

1. Programmet dirigerar användaren till v2.0-slutpunkten för att köra en [princip](user-flow-overview.md).
2. Användaren uppfyller principen enligt principdefinitionen.
3. Programmet tar emot en säkerhetstoken från v2.0-slutpunkten.
4. Programmet använder säkerhetstoken för att komma åt skyddad information eller en skyddad resurs.
5. Resursservern verifierar säkerhetstoken för att kontrollera att åtkomst kan beviljas.
6. Programmet uppdaterar säkerhetstoken med jämna mellanrum.

Dessa steg kan skilja sig något beroende på vilken typ av program du bygger.

## <a name="web-applications"></a>Webbprogram

För webbprogram (inklusive .NET, PHP, Java, Ruby, Python och Node.js) som finns på en server och nås via en webbläsare stöder Azure AD B2C [OpenID Connect](protocols-overview.md) för alla användarupplevelser. I Azure AD B2C-implementeringen av OpenID Connect initierar webbprogrammet användarupplevelser genom att utfärda autentiseringsbegäranden till Azure AD. Resultatet av begäran är en `id_token`. Den här säkerhetstoken representerar användarens identitet. Den tillhandahåller även information om användaren i form av anspråk:

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Läs mer om vilka typer av token och anspråk som är tillgängliga för ett program i [Azure AD B2C-tokenreferensen](tokens-overview.md).

I ett webbprogram tar varje körning av en [princip](user-flow-overview.md) dessa steg på hög nivå:

1. Användaren bläddrar till webbprogrammet.
2. Webbprogrammet omdirigerar användaren till Azure AD B2C som anger principen att köra.
3. Användaren slutför principen.
4. Azure AD B2C `id_token` returnerar en till webbläsaren.
5. Den `id_token` bokförs till omdirigera URI.
6. Valideras `id_token` och en sessionscookie ställs in.
7. En säker sida returneras till användaren.

Valideringen av `id_token` med hjälp av en offentlig signeringsnyckel som fås från Azure AD är tillräckligt för att verifiera användarens identitet. Den här processen anger också en sessionscookie som kan användas för att identifiera användaren på efterföljande sidförfrågningar.

Om du vill se det här scenariot i praktiken provar du ett av inloggningskodexemplen för webbprogram i avsnittet [Komma igång](overview.md).

Förutom att underlätta enkel inloggning kan ett webbserverprogram också behöva komma åt en backend-webbtjänst. I det här fallet kan webbprogrammet utföra ett något annorlunda [OpenID Connect-flöde](openid-connect.md) och hämta token med hjälp av auktoriseringskoder och uppdateringstoken. Det här scenariot illustreras i följande avsnitt om [webb-API:er](#web-apis).

## <a name="web-apis"></a>Webb-API:er

Du kan använda Azure AD B2C för att skydda webbtjänster som programmets RESTful-webb-API. Web API:er kan använda OAuth 2.0 för att skydda sina data genom att autentisera inkommande HTTP-begäranden med hjälp av token. Anroparen av ett webb-API lägger till en token i auktoriseringshuvudet för en HTTP-begäran:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webb-API:et kan sedan använda token för att verifiera API-anroparens identitet och för att extrahera information om anroparen från anspråk som är kodade i token. Mer information om vilka typer av token och anspråk som är tillgängliga för en app finns i [referens för Azure AD B2C-token](tokens-overview.md).

Ett webb-API kan ta emot token från många typer av klienter, inklusive webbprogram, skrivbords- och mobilappar, ensidiga program, daemons på serversidan och andra webb-API:er. Här är ett exempel på hela flödet för ett webbprogram som anropar ett webb-API:

1. Webbprogrammet kör en princip och användaren slutför användarupplevelsen.
2. Azure AD B2C returnerar en `id_token` (OpenID Connect) och en auktoriseringskod till webbläsaren.
3. Webbläsaren bokför `id_token` och auktoriseringskoden till omdirigera URI.
4. Webbservern validerar `id_token` och ställer in en sessionscookie.
5. Webbservern ber Azure AD B2C om en `access_token` genom att förse den med auktoriseringskod, programklient-ID och klientautentiseringsuppgifter.
6. Den `access_token` `refresh_token` och returneras till webbservern.
7. Webb-API:et `access_token` anropas med i ett auktoriseringshuvud.
8. Webb-API:et validerar token.
9. Säkra data returneras till webbprogrammet.

Mer information om auktoriseringskoder, uppdateringstoken och stegen för att hämta token finns i [OAuth 2.0-protokollet](authorization-code-flow.md).

Mer information om hur du skyddar ett webb-API med hjälp av Azure AD B2C finns i självstudiekurserna om webb-API:er i [Komma igång-avsnittet](overview.md).

## <a name="mobile-and-native-applications"></a>Mobila och inbyggda program

Program som installeras på enheter, till exempel mobila program och skrivbordsprogram, behöver ofta komma åt backend-tjänster eller webb-API:er för användarnas räkning. Du kan lägga till anpassade identitetshanteringsupplevelser i dina inbyggda program och på ett säkert sätt anropa backend-tjänster med hjälp av Azure AD B2C och [Auktoriseringskodflödet OAuth 2.0](authorization-code-flow.md).

I det här flödet [policies](user-flow-overview.md) kör programmet `authorization_code` principer och tar emot en från Azure AD när användaren har slutfört principen. Representerar `authorization_code` programmets behörighet att anropa backend-tjänster för den användare som för närvarande är inloggad. Ansökan kan sedan `authorization_code` byta ut i `access_token` bakgrunden `refresh_token`för en och en .  Programmet kan använda `access_token` för att autentisera till ett backend-webb-API i HTTP-begäranden. Den kan också använda `refresh_token` för att hämta en ny `access_token` när en äldre upphör att gälla.

## <a name="current-limitations"></a>Aktuella begränsningar

### <a name="unsupported-application-types"></a>Programtyper som inte stöds

#### <a name="daemonsserver-side-applications"></a>Daemons/server-side applikationer

Program som innehåller tidskrävande processer eller som fungerar utan närvaro av en användare behöver också ett sätt att komma åt säkra resurser som webb-API:er. Dessa program kan autentisera och hämta token med hjälp av programmets identitet (i stället för en användares delegerade identitet) och med hjälp av OAuth 2.0-klientautentiseringsflödet. Klientautentiseringsflödet är inte samma sak som för flöde för den skull och för-för-räkning-flöde bör inte användas för server-till-server-autentisering.

Även om klientautentiseringsflödet för närvarande inte stöds av Azure AD B2C, kan du konfigurera klientautentiseringsflöde med Azure AD. En Azure AD B2C-klient delar vissa funktioner med Azure AD-företagsklienter.  Klientautentiseringsflödet stöds med Hjälp av Azure AD-funktionen för Azure AD B2C-klienten.

Information om hur du konfigurerar klientautentiseringsflöde finns i [Azure Active Directory v2.0 och flödet för OAuth 2.0-klientautentiseringsuppgifter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). En lyckad autentisering resulterar i inleverans av en token formaterad så att den kan användas av Azure AD enligt beskrivningen i [Azure AD-tokenreferens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Webb-API-länkar (On-Behalf-Of-flöde)

Många arkitekturer har ett webb-API som måste anropa ett annat underordnat webb-API, där både skyddas av Azure AD B2C. Det här scenariot är vanligt i inbyggda klienter som har en web API-backend och anropar en Microsoft-onlinetjänst, till exempel Microsoft Graph API.

Det här scenariot med länkade webb-API:er kan användas genom en tilldelning av OAuth 2.0 JWT-ägarautentiseringsuppgifter, även kallat On-Behalf-Of-flöde.  Detta flöde är emellertid inte implementerat i Azure AD B2C.

### <a name="faulted-apps"></a>Felaktig appar

Redigera inte Azure AD B2C-program på följande sätt:

- På andra programhanteringsportaler, till exempel [Programregistreringsportalen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
- Använda Graph API eller PowerShell.

Om du redigerar Azure AD B2C-programmet utanför Azure-portalen blir det ett felaktigt program och kan inte längre kan kan hanteras med Azure AD B2C. Ta bort programmet och skapa det igen.

Om du vill ta bort programmet går du till [programregistreringsportalen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) och tar bort programmet där. Du måste vara ägare till appen (och inte bara en administratör för klienten) för att appen ska vara synlig.

## <a name="next-steps"></a>Nästa steg

Läs mer om de inbyggda principerna som tillhandahålls av [användarflöden i Azure Active Directory B2C](user-flow-overview.md).
