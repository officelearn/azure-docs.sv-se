---
title: Typer av program som kan användas i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om typerna av program som du kan använda i den Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7ea179ff7e4b525e86003faadfb92e090476bd81
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172654"
---
# <a name="applications-types-that-can-be-used-in-active-directory-b2c"></a>Typer av program som kan användas i Active Directory B2C

Azure Active Directory (Azure AD) B2C stöder autentisering för en mängd olika moderna programarkitekturer. Alla baseras på standardprotokollen [OAuth 2.0](active-directory-b2c-reference-protocols.md) och [OpenID Connect](active-directory-b2c-reference-protocols.md). Det här dokumentet beskriver typerna av program som du kan skapa, oberoende av plattform eller språk du föredrar. Det hjälper dig också att förstå de övergripande scenarierna innan du börjar bygga program.

Alla program som använder Azure AD B2C måste registreras i din [Azure AD B2C-klient](active-directory-b2c-get-started.md) med hjälp av den [Azure-portalen](https://portal.azure.com/). Registreringen program samlar in och tilldelar värden, till exempel:

* En **program-ID** som unikt identifierar ditt program.
* En **svars-URL** som kan användas för att dirigera svar tillbaka till ditt program.

Varje begäran som skickas till Azure AD B2C anger en **användarflödet**, vilket är en princip som styr beteendet för Azure AD. Du kan också använda dessa slutpunkter för att skapa ytterst anpassningsbara användarupplevelser. Vi tillhandahåller en uppsättning användarflöden för att konfigurera vanliga principer, inklusive registrering, inloggning och profilredigeringsprinciper. Men du kan också skapa dina egna anpassade principer. Om du inte är bekant med principer bör du lära dig om [det expanderbara principramverket](active-directory-b2c-reference-policies.md) för Azure AD B2C innan du fortsätter.

Interaktionen för alla program följer ett liknande övergripande mönster:

1. Programmet som leder användaren till v2.0-slutpunkten att köra en [princip](active-directory-b2c-reference-policies.md).
2. Användaren uppfyller principen enligt principdefinitionen.
3. Programmet tar emot en säkerhetstoken från v2.0-slutpunkten.
4. Programmet använder säkerhetstoken för att komma åt känslig information eller en skyddad resurs.
5. Resursservern verifierar säkerhetstoken för att kontrollera att åtkomst kan beviljas.
6. Programmet uppdaterar säkerhetstoken med jämna mellanrum.

De här stegen kan skilja sig något beroende på vilken typ av program som du skapar.

## <a name="web-applications"></a>Webbprogram

Azure AD B2C stöder för webbprogram (inklusive .NET, PHP, Java, Ruby, Python och Node.js) som finns på en server och öppnas via en webbläsare, [OpenID Connect](active-directory-b2c-reference-protocols.md) i alla användarmiljöer. I Azure AD B2C-implementeringen av OpenID Connect initierar din webbapp användarupplevelser genom att utfärda autentiseringsförfrågningar till Azure AD. Resultatet av begäran är en `id_token`. Den här säkerhetstoken representerar användarens identitet. Den tillhandahåller även information om användaren i form av anspråk:

```
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

Mer information om vilka typer av token och anspråk som är tillgängliga för ett program i den [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md).

I ett webbprogram, varje körning av en [princip](active-directory-b2c-reference-policies.md) utförs följande övergripande steg:

1. Användaren bläddrar i webbprogrammet.
2. Webbprogrammet omdirigeras användaren till Azure AD B2C som anger principen att köra.
3. Användaren uppfyller principen.
4. Azure AD B2C returnerar ett `id_token` till webbläsaren.
5. Den `id_token` publiceras omdirigerings-URI.
6. Den `id_token` verifieras och en sessions-cookie har angetts.
7. En säker sida returneras till användaren.

Valideringen av `id_token` med hjälp av en offentlig signeringsnyckel som fås från Azure AD är tillräckligt för att verifiera användarens identitet. Den här processen ställer även en sessions-cookie som kan användas för att identifiera användaren vid efterföljande sidförfrågningar.

Om du vill se det här scenariot fungerar i praktiken kan du prova någon av web application inloggning kodexempel i vår [komma igång-avsnittet](active-directory-b2c-overview.md).

Förutom att underlätta enkel inloggning, kan ett webbserverprogram också behöva åtkomst till en backend-webbtjänst. I det här fallet webbprogrammet kan köra ett något annorlunda [OpenID Connect-flöde](active-directory-b2c-reference-oidc.md) och hämta token genom att använda auktoriseringskoder och uppdateringstoken. Det här scenariot illustreras i följande avsnitt om [webb-API:er](#web-apis).

## <a name="web-apis"></a>Webb-API:er

Du kan använda Azure AD B2C för att skydda webbtjänster, till exempel ditt programs RESTful webb-API. Web API:er kan använda OAuth 2.0 för att skydda sina data genom att autentisera inkommande HTTP-begäranden med hjälp av token. Anroparen av ett webb-API lägger till en token i auktoriseringshuvudet för en HTTP-begäran:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webb-API:et kan sedan använda token för att verifiera API-anroparens identitet och för att extrahera information om anroparen från anspråk som är kodade i token. Mer information om vilka typer av token och anspråk som är tillgängliga för en app finns i [referens för Azure AD B2C-token](active-directory-b2c-reference-tokens.md).

Ett webb-API kan ta emot token från många typer av klienter, inklusive webbprogram, fjärrskrivbord och mobilprogram, en sida program, server-deamon och andra webb-API: er. Här är ett exempel på det fullständiga flödet för ett webbprogram som anropar ett webb-API:

1. Webbprogrammet körs en princip och användaren Slutför användarupplevelsen.
2. Azure AD B2C returnerar ett `access_token` och en auktoriseringskod till webbläsaren.
3. Webbläsaren inlägg i `access_token` och auktoriseringskod omdirigerings-URI.
4. Webbservern bekräftar den `access token` och anger en sessions-cookie.
5. Den `access_token` tillhandahålls till Azure AD B2C med auktoriseringskod programklients-ID och autentiseringsuppgifter.
6. Den `access_token` och `refresh_token` returneras till webbservern.
7. Webb-API anropas med den `access_token` i en auktoriseringsrubrik.
8. Webb-API: verifierar token.
9. Säkra data returneras till webbservern.

Mer information om auktoriseringskoder, uppdateringstoken och stegen för att hämta token finns i [OAuth 2.0-protokollet](active-directory-b2c-reference-oauth-code.md).

Mer information om hur du skyddar ett webb-API med hjälp av Azure AD B2C finns i självstudiekurserna om webb-API:er i [Komma igång-avsnittet](active-directory-b2c-overview.md).

## <a name="mobile-and-native-applications"></a>Mobila och interna program

Program som är installerade på enheter, till exempel bärbara och stationära program behöver ofta åtkomst till backend-tjänster eller webb-API: er åt användare. Du kan lägga till anpassade identitetshanteringsmiljöer i dina interna program och på ett säkert sätt anropa backend-tjänster med hjälp av Azure AD B2C och [OAuth 2.0-auktoriseringskodflödet](active-directory-b2c-reference-oauth-code.md).  

I det här flödet att programmet kör [principer](active-directory-b2c-reference-policies.md) och tar emot en `authorization_code` från Azure AD när användaren uppfyller principen. Den `authorization_code` representerar programmets behörighet att anropa backend-tjänster för den användare som för närvarande är inloggad. Programmet kan sedan byta den `authorization_code` i bakgrunden för en `id_token` och en `refresh_token`.  Programmet kan använda den `id_token` att autentisera till en backend-webb API i HTTP-förfrågningar. Den kan också använda `refresh_token` för att hämta en ny `id_token` när en äldre upphör att gälla.

## <a name="current-limitations"></a>Aktuella begränsningar

### <a name="application-not-supported"></a>Program som inte stöds 

#### <a name="daemonsserver-side-applications"></a>Daemons från serversidan program

Program som innehåller tidskrävande processer eller som fungerar utan närvaron av en användare måste också ett sätt att komma åt skyddade resurser, till exempel webb-API: er. Dessa program kan autentisera och hämta token genom att använda programmets identitet (i stället för en användares delegerade identitet) och med hjälp av OAuth 2.0-klienten flödet. Klienten credential flow är inte samma som på räkning-flöde och på räkning-flöde inte ska användas för autentisering av server-till-server.

Även om autentiseringsuppgifter klientflödet inte stöds för närvarande av Azure AD B2C, kan du ställa in autentiseringsuppgifter klientflödet med hjälp av Azure AD. En Azure AD B2C-klient delar vissa funktioner med Azure AD enterprise klienter.  Autentiseringsuppgifter klientflödet stöds med hjälp av Azure AD-funktionerna för Azure AD B2C-klient. 

Om du vill konfigurera autentiseringsuppgifter klientflödet Se [Azure Active Directory v2.0- och OAuth 2.0-klient autentiseringsuppgifter flow](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). En lyckad autentisering resulterar i mottagandet av en token som är formaterade så att den kan användas av Azure AD som beskrivs i [tokenreferens för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Webb-API-länkar (On-Behalf-Of-flöde)

Många arkitekturer har ett webb-API som måste anropa ett annat underordnat webb-API, där både skyddas av Azure AD B2C. Det här scenariot är vanligt i interna klienter som har en webb-API-serverdel och en Microsoft-onlinetjänst som Azure AD Graph API-anrop.

Det här scenariot med länkade webb-API:er kan användas genom en tilldelning av OAuth 2.0 JWT-ägarautentiseringsuppgifter, även kallat On-Behalf-Of-flöde.  Detta flöde är emellertid inte implementerat i Azure AD B2C.

### <a name="faulted-apps"></a>Felaktig appar

Redigera inte Azure AD B2C-program på följande sätt:

- På andra programhanteringsportaler som den [Programregistreringsportalen](https://apps.dev.microsoft.com/).
- Med Graph API eller PowerShell.

Om du redigerar Azure AD B2C-program utanför Azure portal, blir en felaktig App och inte längre kan användas med Azure AD B2C. Ta bort programmet och skapa det igen.

Om du vill ta bort programmet går du till den [Programregistreringsportalen](https://apps.dev.microsoft.com/) och ta bort appen. Du måste vara ägare till appen (och inte bara en administratör för klienten) för att appen ska vara synlig.

