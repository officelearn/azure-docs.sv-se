---
title: Program typer som stöds av Azure AD B2C
titleSuffix: Azure AD B2C
description: Lär dig mer om de typer av program som du kan använda med Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 753943116edab443509b7d5747e749910e366806
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948802"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Program typer som kan användas i Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) stöder autentisering för en mängd moderna program arkitekturer. Alla baseras på standardprotokollen [OAuth 2.0](active-directory-b2c-reference-protocols.md) och [OpenID Connect](active-directory-b2c-reference-protocols.md). I den här artikeln beskrivs de olika typerna av program som du kan bygga, oberoende av vilket språk eller vilken plattform du föredrar. Det hjälper dig också att förstå de övergripande scenarierna innan du börjar skapa program.

Alla program som använder Azure AD B2C måste registreras i din [Azure AD B2C-klient](active-directory-b2c-get-started.md) med hjälp av [Azure Portal](https://portal.azure.com/). Program registrerings processen samlar in och tilldelar värden, till exempel:

* Ett **program-ID** som unikt identifierar ditt program.
* En **svars-URL** som kan användas för att dirigera svar tillbaka till ditt program.

Varje begäran som skickas till Azure AD B2C anger ett **användar flöde** (en inbyggd princip) eller en **anpassad princip** som styr hur Azure AD B2C fungerar. Med båda princip typerna kan du skapa en mycket anpassningsbar uppsättning användar upplevelser.

Interaktionen för alla program följer ett liknande övergripande mönster:

1. Programmet dirigerar användaren till v 2.0-slutpunkten för att köra en [princip](active-directory-b2c-reference-policies.md).
2. Användaren uppfyller principen enligt principdefinitionen.
3. Programmet tar emot en säkerhetstoken från v 2.0-slutpunkten.
4. Programmet använder säkerhetstoken för att komma åt skyddad information eller en skyddad resurs.
5. Resursservern verifierar säkerhetstoken för att kontrollera att åtkomst kan beviljas.
6. Programmet uppdaterar säkerhetstoken med jämna mellanrum.

De här stegen kan skilja sig något beroende på vilken typ av program som du skapar.

## <a name="web-applications"></a>Webbprogram

För webb program (inklusive .NET, PHP, Java, ruby, python och Node. js) som finns på en server och som nås via en webbläsare, Azure AD B2C stöder [OpenID Connect](active-directory-b2c-reference-protocols.md) för alla användar upplevelser. I Azure AD B2C implementeringen av OpenID Connect initierar ditt webb program användar upplevelser genom att skicka autentiseringsbegäranden till Azure AD. Resultatet av begäran är en `id_token`. Den här säkerhetstoken representerar användarens identitet. Den tillhandahåller även information om användaren i form av anspråk:

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

Läs mer om de typer av tokens och anspråk som är tillgängliga för ett program i [referensen för Azure AD B2C-token](active-directory-b2c-reference-tokens.md).

I ett webb program utför varje körning av en [princip](active-directory-b2c-reference-policies.md) dessa steg på hög nivå:

1. Användaren bläddrar till webb programmet.
2. Webb programmet omdirigerar användaren till Azure AD B2C som anger vilken princip som ska köras.
3. Användaren har slutfört principen.
4. Azure AD B2C returnerar en `id_token` till webbläsaren.
5. `id_token` skickas till omdirigerings-URI: n.
6. `id_token` verifieras och en sessions-cookie anges.
7. En säker sida returneras till användaren.

Valideringen av `id_token` med hjälp av en offentlig signeringsnyckel som fås från Azure AD är tillräckligt för att verifiera användarens identitet. Den här processen anger även en cookie för session som kan användas för att identifiera användaren på efterföljande sid begär Anden.

Om du vill se hur det här scenariot fungerar kan du prova någon av inloggnings kod exemplen för webb program i [avsnittet komma igång](active-directory-b2c-overview.md).

Förutom att under lätta enkel inloggning, kan ett webb serverprogram också ha åtkomst till en backend-webbtjänst. I det här fallet kan webb programmet utföra ett något annorlunda [OpenID Connect-flöde](active-directory-b2c-reference-oidc.md) och hämta token genom att använda auktoriseringsregler och uppdatera tokens. Det här scenariot illustreras i följande avsnitt om [webb-API:er](#web-apis).

## <a name="web-apis"></a>Webb-API:er

Du kan använda Azure AD B2C för att skydda webb tjänster, till exempel programmets RESTful webb-API. Web API:er kan använda OAuth 2.0 för att skydda sina data genom att autentisera inkommande HTTP-begäranden med hjälp av token. Anroparen av ett webb-API lägger till en token i auktoriseringshuvudet för en HTTP-begäran:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webb-API:et kan sedan använda token för att verifiera API-anroparens identitet och för att extrahera information om anroparen från anspråk som är kodade i token. Mer information om vilka typer av token och anspråk som är tillgängliga för en app finns i [referens för Azure AD B2C-token](active-directory-b2c-reference-tokens.md).

Ett webb-API kan ta emot token från många typer av klienter, inklusive webb program, skriv bords-och mobil program, enstaka sidor, daemon på Server sidan och andra webb-API: er. Här är ett exempel på det fullständiga flödet för ett webb program som anropar ett webb-API:

1. Webb programmet kör en princip och användaren slutför användar upplevelsen.
2. Azure AD B2C returnerar en (OpenID Connect) `id_token` och en auktoriseringskod till webbläsaren.
3. Webbläsaren skickar `id_token` och auktoriseringskod till omdirigerings-URI: n.
4. Webb servern verifierar `id_token` och anger en sessions-cookie.
5. Webb servern ber Azure AD B2C för en `access_token` genom att tillhandahålla auktoriseringskod, programklient-ID och klientautentiseringsuppgifter.
6. `access_token` och `refresh_token` returneras till webb servern.
7. Webb-API: et anropas med `access_token` i ett Authorization-huvud.
8. Webb-API: et verifierar token.
9. Säkra data returneras till webb programmet.

Mer information om auktoriseringskoder, uppdateringstoken och stegen för att hämta token finns i [OAuth 2.0-protokollet](active-directory-b2c-reference-oauth-code.md).

Mer information om hur du skyddar ett webb-API med hjälp av Azure AD B2C finns i självstudiekurserna om webb-API:er i [Komma igång-avsnittet](active-directory-b2c-overview.md).

## <a name="mobile-and-native-applications"></a>Mobila och inbyggda program

Program som är installerade på enheter, till exempel mobil-och skriv bords program, behöver ofta åtkomst till backend-tjänster eller webb-API: er för användarnas räkning. Du kan lägga till anpassade identitets hanterings upplevelser i dina egna program och på ett säkert sätt anropa backend-tjänster genom att använda Azure AD B2C och [OAuth 2,0-Authorization Code Flow](active-directory-b2c-reference-oauth-code.md).

I det här flödet kör programmet [principer](active-directory-b2c-reference-policies.md) och tar emot en `authorization_code` från Azure AD när användaren har slutfört principen. `authorization_code` representerar programmets behörighet att anropa backend-tjänster åt den användare som för tillfället är inloggad. Programmet kan sedan byta ut `authorization_code` i bakgrunden för en `access_token` och en `refresh_token`.  Programmet kan använda `access_token` för att autentisera till ett Server dels webb-API i HTTP-begäranden. Den kan också använda `refresh_token` för att hämta en ny `access_token` när en äldre upphör att gälla.

## <a name="current-limitations"></a>Aktuella begränsningar

### <a name="unsupported-application-types"></a>Programtyper som inte stöds

#### <a name="daemonsserver-side-applications"></a>Daemon/program på Server Sidan

Program som innehåller tids krävande processer eller som fungerar utan förekomst av en användare måste också ha ett sätt att komma åt skyddade resurser, till exempel webb-API: er. Dessa program kan autentisera och hämta token genom att använda programmets identitet (i stället för en användares delegerade identitet) och genom att använda flödet OAuth 2,0-klientautentiseringsuppgifter. Flödet för klientens autentiseringsuppgifter är inte detsamma som on-Flow-Flow och on-Flow-Flow bör inte användas för autentisering från server till server.

Även om flödet för klientautentiseringsuppgifter inte stöds av Azure AD B2C, kan du ställa in flöde för autentiseringsuppgifter för klient med hjälp av Azure AD. En Azure AD B2C-klient delar vissa funktioner med Azure AD Enterprise-klienter.  Klientens Credential-flöde stöds med hjälp av Azure AD-funktionerna i Azure AD B2C-klienten.

Information om hur du konfigurerar inloggnings flödet för klienten finns i [Azure Active Directory v 2.0 och OAuth 2,0-klientens autentiseringsuppgifter Flow](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). En lyckad autentisering resulterar i att en token formateras så att den kan användas av Azure AD enligt beskrivningen i [referens för Azure AD-token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Webb-API-länkar (On-Behalf-Of-flöde)

Många arkitekturer har ett webb-API som måste anropa ett annat underordnat webb-API, där både skyddas av Azure AD B2C. Det här scenariot är vanligt i interna klienter som har en webb-API backend och anropar en Microsoft Online-tjänst, till exempel Microsoft Graph-API eller Azure AD Graph API.

Det här scenariot med länkade webb-API:er kan användas genom en tilldelning av OAuth 2.0 JWT-ägarautentiseringsuppgifter, även kallat On-Behalf-Of-flöde.  Detta flöde är emellertid inte implementerat i Azure AD B2C.

### <a name="faulted-apps"></a>Felaktig appar

Redigera inte Azure AD B2C program på följande sätt:

- På andra program hanterings [portaler som program registrerings portalen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
- Använda Graph API eller PowerShell.

Om du redigerar Azure AD B2C-programmet utanför Azure Portal blir det ett felaktigt program och kan inte längre användas med Azure AD B2C. Ta bort programmet och skapa det igen.

Om du vill ta bort programmet går du till [program registrerings portalen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) och tar bort programmet där. Du måste vara ägare till appen (och inte bara en administratör för klienten) för att appen ska vara synlig.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om de inbyggda principerna som tillhandahålls av [användar flöden i Azure Active Directory B2C](active-directory-b2c-reference-policies.md).
