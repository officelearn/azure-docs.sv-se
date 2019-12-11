---
title: Microsoft Identity Platform & OpenID Connect-protokoll | Azure
description: Bygg webb program med hjälp av Microsoft Identity Platform-implementeringen av OpenID Connect Authentication Protocol.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc3778f31cb5dd68d3f3f49ed3cddf574b1cc3bd
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966749"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity Platform och OpenID Connect-protokoll

OpenID Connect är ett autentiseringsprotokoll som skapats på OAuth 2,0 som du kan använda för att på ett säkert sätt logga in en användare i ett webb program. När du använder Microsoft Identity Platform-slutpunktens implementering av OpenID Connect kan du lägga till inloggnings-och API-åtkomst till dina webbaserade appar. Den här artikeln visar hur du gör detta oberoende av språk och beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda några Microsoft-bibliotek med öppen källkod.

> [!NOTE]
> Microsoft Identity Platform-slutpunkten har inte stöd för alla Azure Active Directory (Azure AD)-scenarier och-funktioner. För att avgöra om du ska använda Microsoft Identity Platform-slutpunkten läser du om [begränsningar för Microsoft Identity Platform](active-directory-v2-limitations.md).

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) utökar *autentiseringsprotokollet OAuth 2,0 som* ska användas som *autentiseringsprotokoll* , så att du kan utföra enkel inloggning med OAuth. OpenID Connect introducerar konceptet med en *ID-token*, vilket är en säkerhetstoken som gör att klienten kan verifiera användarens identitet. ID-token hämtar även grundläggande profil information om användaren. Eftersom OpenID Connect utökar OAuth 2,0 kan appar säkert Hämta *åtkomsttoken*, som kan användas för att få åtkomst till resurser som skyddas av en [Authorization Server](active-directory-v2-protocols.md#the-basics). Microsoft Identity Platform-slutpunkten tillåter också att appar från tredje part som är registrerade med Azure AD kan utfärda åtkomsttoken för skyddade resurser, till exempel webb-API: er. Mer information om hur du konfigurerar ett program för att utfärda åtkomsttoken finns i [så här registrerar du en app med slut punkten för Microsoft Identity Platform](quickstart-register-app.md). Vi rekommenderar att du använder OpenID Connect om du skapar ett [webb program](v2-app-types.md#web-apps) som finns på en server och har åtkomst till via en webbläsare.

## <a name="protocol-diagram-sign-in"></a>Protokoll diagram: inloggning

Det mest grundläggande inloggnings flödet har stegen som visas i nästa diagram. Varje steg beskrivs i detalj i den här artikeln.

![OpenID Connect-protokoll: Logga in](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Hämta dokumentet för OpenID Connect-metadata

OpenID Connect beskriver ett dokument med metadata som innehåller merparten av den information som krävs för att en app ska kunna logga in. Detta omfattar information som webb adresserna som ska användas och platsen för tjänstens offentliga signerings nycklar. För Microsoft Identity Platform-slutpunkten är detta OpenID Connect-Metadatadokumentet som du bör använda:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Prova! Klicka på [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) för att se konfigurationen av `common` klienter.

`{tenant}` kan ha ett av fyra värden:

| Värde | Beskrivning |
| --- | --- |
| `common` |Användare med både ett privat Microsoft-konto och ett arbets-eller skol konto från Azure AD kan logga in i programmet. |
| `organizations` |Endast användare med arbets-eller skol konton från Azure AD kan logga in i programmet. |
| `consumers` |Endast användare med en personlig Microsoft-konto kan logga in i programmet. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` | Endast användare från en specifik Azure AD-klient (oavsett om de är medlemmar i katalogen med ett arbets-eller skol konto eller om de är gäster i katalogen med en personlig Microsoft-konto) kan logga in i programmet. Antingen det egna domän namnet för Azure AD-klienten eller klient organisationens GUID-identifierare kan användas. Du kan också använda klient organisationen `9188040d-6c67-4c5b-b112-36a304b66dad``consumers` klient organisationens plats.  |

Metadata är ett enkelt JavaScript Object Notation (JSON)-dokument. Se följande kodfragment för ett exempel. Kodfragmentets innehåll beskrivs fullständigt i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

Om din app har anpassade signerings nycklar som ett resultat av funktionen för [anspråks mappning](active-directory-claims-mapping.md) måste du lägga till en `appid` frågeparameter som innehåller app-ID: t för att få ett `jwks_uri` som pekar på appens information om signerings nyckel. Exempel: `https://login.microsoftonline.com/{tenant}/.well-known/v2.0/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` innehåller en `jwks_uri` av `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

Normalt använder du det här Metadatadokumentet för att konfigurera ett OpenID Connect-bibliotek eller SDK. i biblioteket används metadata för att utföra sitt arbete. Men om du inte använder ett förbyggt OpenID Connect-bibliotek kan du följa stegen i resten av den här artikeln för att logga in i en webbapp med hjälp av Microsoft Identity Platform-slutpunkten.

## <a name="send-the-sign-in-request"></a>Skicka inloggnings förfrågan

När din webbapp behöver autentisera användaren kan användaren dirigera användaren till `/authorize` slut punkten. Den här begäran liknar det första benet i [OAuth 2,0-auktoriseringskod](v2-oauth2-auth-code-flow.md), med följande viktiga distinkta:

* Begäran måste innehålla `openid` omfattning i `scope`-parametern.
* Parametern `response_type` måste innehålla `id_token`.
* Begäran måste innehålla parametern `nonce`.

> [!IMPORTANT]
> För att kunna begära en ID-token från/Authorization-slutpunkten måste appens registrering på [registrerings portalen](https://portal.azure.com) ha implicit beviljande av id_tokens aktiverat på fliken autentisering (som anger `oauth2AllowIdTokenImplicitFlow`-flaggan i [program manifestet](reference-app-manifest.md) till `true`). Om den inte är aktive rad returneras ett `unsupported_response` fel: "det angivna värdet för Indataparametern response_type tillåts inte för den här klienten. Förväntat värde är ' Code '

Exempel:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Klicka på följande länk för att utföra den här begäran. När du har loggat in omdirigeras webbläsaren till `https://localhost/myapp/`, med en ID-token i adress fältet. Observera att denna begäran använder `response_mode=fragment` (endast för demonstrations ändamål). Vi rekommenderar att du använder `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Du kan använda `{tenant}`-värdet i sökvägen till begäran för att kontrol lera vem som kan logga in på programmet. De tillåtna värdena är `common`, `organizations`, `consumers`och klient-ID: n. Mer information finns i [grunderna om protokoll](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Krävs | **Program-ID: t (klienten)** som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen som har tilldelats din app. |
| `response_type` | Krävs | Måste innehålla `id_token` för OpenID Connect-inloggning. Det kan också innehålla andra `response_type` värden, till exempel `code`. |
| `redirect_uri` | Rekommenderad | Omdirigerings-URI för appen, där autentiseringsbegäranden kan skickas och tas emot av din app. Det måste exakt matcha en av de omdirigerings-URI: er som du registrerade i portalen, förutom att den måste vara URL-kodad. Om detta inte finns väljer slut punkten en registrerad redirect_uri slumpmässigt för att skicka tillbaka användaren till. |
| `scope` | Krävs | En blankstegsavgränsad lista över omfång. För att OpenID ska kunna ansluta måste den innehålla omfånget `openid`, vilket översätts till behörigheten "logga in dig" i medgivande gränssnittet. Du kan också inkludera andra omfattningar i den här begäran för att begära medgivande. |
| `nonce` | Krävs | Ett värde som ingår i begäran, som genereras av appen, som kommer att inkluderas i det resulterande id_token svärdet som ett anspråk. Appen kan verifiera det här värdet för att minimera omuppspelning av token. Värdet är vanligt vis en slumpmässig, unik sträng som kan användas för att identifiera ursprunget för begäran. |
| `response_mode` | Rekommenderad | Anger den metod som ska användas för att skicka den resulterande auktoriseringskod tillbaka till din app. Det kan vara `form_post` eller `fragment`. För webb program rekommenderar vi att du använder `response_mode=form_post`för att säkerställa den säkraste överföringen av tokens till ditt program. |
| `state` | Rekommenderad | Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Ett slumpmässigt genererat unikt värde används vanligt vis för att [förhindra förfalsknings attacker på begäran](https://tools.ietf.org/html/rfc6749#section-10.12)från en annan plats. Stadiet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn där användaren var på. |
| `prompt` | Valfritt | Anger vilken typ av användar interaktion som krävs. De enda giltiga värdena för tillfället är `login`, `none`och `consent`. `prompt=login`-anspråk tvingar användaren att ange sina autentiseringsuppgifter för den begäran, vilket eliminerar enkel inloggning. `prompt=none`-anspråk är det motsatta. Detta påstående garanterar att användaren inte visas med interaktiva prompter på. Om begäran inte kan slutföras i bakgrunden via enkel inloggning returnerar slut punkten för Microsoft Identity Platform ett fel. `prompt=consent`-anspråket utlöser dialog rutan OAuth-medgivande när användaren loggar in. Dialog rutan uppmanar användaren att bevilja behörighet till appen. |
| `login_hint` | Valfritt | Du kan använda den här parametern för att fylla i fältet användar namn och e-postadress på inloggnings sidan för användaren, om du känner till användar namnet i förväg. Appar använder ofta den här parametern vid omautentisering, när de redan har extraherat användar namnet från en tidigare inloggning med hjälp av `preferred_username`-anspråket. |
| `domain_hint` | Valfritt | Användarens sfär i en federerad katalog.  Detta hoppar över den e-postbaserad identifierings process som användaren går igenom på inloggnings sidan för en något mer strömlinjeformad användar upplevelse. För klienter som är federerade via en lokal katalog som AD FS resulterar detta ofta i en sömlös inloggning på grund av den befintliga inloggningssessionen. |

I det här läget uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen. Slut punkten för Microsoft Identity Platform kontrollerar att användaren har samtyckt till de behörigheter som anges i parametern `scope` fråga. Om användaren inte har samtyckt till någon av dessa behörigheter uppmanas användaren av Microsoft Identity Platform-slutpunkten att godkänna de behörigheter som krävs. Du kan läsa mer om [behörigheter, medgivande och appar för flera klient organisationer](v2-permissions-and-consent.md).

När användaren autentiserar och godkänner medgivande, returnerar Microsoft Identity Platform-slutpunkten ett svar på din app vid angiven omdirigerings-URI med hjälp av den metod som anges i parametern `response_mode`.

### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar när du använder `response_mode=form_post` ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| `id_token` | Den ID-token som appen begärde. Du kan använda parametern `id_token` för att verifiera användarens identitet och påbörja en session med användaren. Mer information om ID-token och deras innehåll finns i [referensen`id_tokens`](id-tokens.md). |
| `state` | Om en `state`-parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till omdirigerings-URI: n så att appen kan hantera dem. Ett fel svar ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En fel kods sträng som du kan använda för att klassificera typer av fel som inträffar och för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa dig att identifiera rotor saken till ett autentiseringsfel. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för slut punkts fel i tillstånd

I följande tabell beskrivs felkoder som kan returneras i `error`-parametern för fel svaret:

| Felkod | Beskrivning | Klient åtgärd |
| --- | --- | --- |
| `invalid_request` | Protokoll fel, till exempel en obligatorisk parameter som saknas. |Åtgärda och skicka begäran på nytt. Detta är ett utvecklings fel som vanligt vis fångas under den första testningen. |
| `unauthorized_client` | Klient programmet kan inte begära en auktoriseringskod. |Detta inträffar vanligt vis när klient programmet inte är registrerat i Azure AD eller inte har lagts till i användarens Azure AD-klient. Programmet kan begära att användaren får instruktioner för att installera programmet och lägga till det i Azure AD. |
| `access_denied` | Resurs ägaren nekade medgivande. |Klient programmet kan meddela användaren att den inte kan fortsätta om inte användaren samtycks. |
| `unsupported_response_type` |Auktoriseringsservern stöder inte svars typen i begäran. |Åtgärda och skicka begäran på nytt. Detta är ett utvecklings fel som vanligt vis fångas under den första testningen. |
| `server_error` | Ett oväntat fel uppstod i servern. |Gör om begäran. Dessa fel kan orsakas av tillfälliga förhållanden. Klient programmet kan förklara för användaren att dess svar är fördröjt på grund av ett tillfälligt fel. |
| `temporarily_unavailable` | Servern är tillfälligt upptagen och kan inte hantera begäran. |Gör om begäran. Klient programmet kan förklara för användaren att dess svar är fördröjt på grund av ett tillfälligt tillstånd. |
| `invalid_resource` | Mål resursen är ogiltig eftersom den inte finns, det går inte att hitta den i Azure AD, eller så är den inte korrekt konfigurerad. |Detta anger att resursen, om den finns, inte har kon figurer ATS i klienten. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD. |

## <a name="validate-the-id-token"></a>Validera ID-token

Det räcker bara att ta emot ett id_token för att autentisera användaren. Du måste verifiera id_token signaturen och kontrol lera anspråk i token enligt appens krav. Slut punkten för Microsoft Identity Platform använder [JSON-Webbtoken (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentliga nycklar för att signera tokens och kontrol lera att de är giltiga.

Du kan välja att validera `id_token` i klient koden, men en vanlig metod är att skicka `id_token` till en backend-server och göra valideringen där. När du har verifierat signaturen för id_token behöver du bara verifiera några anspråk. Se [`id_token` referens](id-tokens.md) för mer information, inklusive [validera token](id-tokens.md#validating-an-id_token) och [viktig information om förnyelse av signerings nyckel](active-directory-signing-key-rollover.md). Vi rekommenderar att du använder ett bibliotek för att parsa och validera token – det finns minst en tillgänglig för de flesta språk och plattformar.

Du kanske också vill verifiera ytterligare anspråk beroende på ditt scenario. Några vanliga valideringar är:

* Se till att användaren/organisationen har registrerat sig för appen.
* Se till att användaren har rätt behörighet/privilegier
* Att se till att en viss styrka autentisering har skett, t. ex. Multi-Factor Authentication.

När du har verifierat id_token kan du starta en-session med användaren och använda anspråk i id_token för att hämta information om användaren i din app. Den här informationen kan användas för visning, poster, anpassning osv.

## <a name="send-a-sign-out-request"></a>Skicka en inloggningsbegäran

När du vill logga ut användaren från din app räcker det inte att rensa appens cookies eller på annat sätt avsluta användarens session. Du måste också omdirigera användaren till Microsoft Identity Platform-slutpunkten för att logga ut. Om du inte gör detta autentiserar användaren om din app utan att ange sina autentiseringsuppgifter igen, eftersom de kommer att ha en giltig enkel inloggnings-session med slut punkten för Microsoft Identity Platform.

Du kan omdirigera användaren till `end_session_endpoint` som anges i OpenID Connect-Metadatadokumentet:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | Tillstånd | Beskrivning |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Rekommenderad | URL: en som användaren omdirigeras till efter att den har loggat ut. Om parametern inte ingår visas ett allmänt meddelande som genereras av Microsoft Identity Platform-slutpunkten. URL: en måste matcha en av de omdirigerings-URI: er som registrerats för ditt program i registrerings portalen för appen. |

## <a name="single-sign-out"></a>Enkel utloggning

När du omdirigerar användaren till `end_session_endpoint`rensar Microsoft Identity Platform-slutpunkten användarens session från webbläsaren. Användaren kan dock fortfarande vara inloggad i andra program som använder Microsoft-konton för autentisering. Om du vill att dessa program ska kunna signera användaren samtidigt skickar Microsoft Identity Platform-slutpunkten en HTTP GET-begäran till den registrerade `LogoutUrl` för alla program som användaren är inloggad på. Program måste svara på den här begäran genom att rensa alla sessioner som identifierar användaren och returnera ett `200`-svar. Om du vill stödja enkel utloggning i ditt program måste du implementera en sådan `LogoutUrl` i programmets kod. Du kan ställa in `LogoutUrl` från registrerings portalen för appen.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokoll diagram: hämtning av åtkomst-token

Många webbappar behöver inte bara signera användaren i, utan även för att få åtkomst till en webb tjänst för användarens räkning genom att använda OAuth. I det här scenariot kombineras OpenID-anslutningar för användarautentisering samtidigt som du får en auktoriseringskod som du kan använda för att hämta åtkomsttoken om du använder OAuth Authorization Code Flow.

Det fullständiga OpenID Connect-flödet för inloggning och hämtning av token ser ut ungefär som i nästa diagram. Vi beskriver varje steg i detalj i nästa avsnitt av artikeln.

![OpenID Connect-protokoll: token-hämtning](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Hämta åtkomsttoken
Om du vill hämta åtkomsttoken ändrar du inloggnings förfrågan:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Klicka på följande länk för att utföra den här begäran. När du har loggat in omdirigeras webbläsaren till `https://localhost/myapp/`, med en ID-token och en kod i adress fältet. Observera att denna begäran endast använder `response_mode=fragment` i demonstrations syfte. Vi rekommenderar att du använder `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Genom att inkludera behörighets omfång i begäran och genom att använda `response_type=id_token code`, säkerställer Microsoft Identity Platform-slutpunkten att användaren har godkänt de behörigheter som anges i `scope` Frågeparametern. Den returnerar en auktoriseringskod till din app för utbyte av en åtkomsttoken.

### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar från att använda `response_mode=form_post` ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| `id_token` | Den ID-token som appen begärde. Du kan använda ID-token för att verifiera användarens identitet och påbörja en session med användaren. Du hittar mer information om ID-tokens och deras innehåll i [`id_tokens` referensen](id-tokens.md). |
| `code` | Den auktoriseringskod som appen begärde. Appen kan använda auktoriseringskod för att begära en åtkomsttoken för mål resursen. En auktoriseringskod är kort livs längd. Normalt går en auktoriseringskod ut om 10 minuter. |
| `state` | Om en tillstånds parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till omdirigerings-URI: n så att appen kan hantera dem på rätt sätt. Ett fel svar ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En fel kods sträng som du kan använda för att klassificera typer av fel som inträffar och för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa dig att identifiera rotor saken till ett autentiseringsfel. |

En beskrivning av möjliga fel koder och rekommenderade klient svar finns i [felkoder för slut punkts fel](#error-codes-for-authorization-endpoint-errors).

När du har en auktoriseringskod och en ID-token kan du logga in användaren i och hämta åtkomsttoken för deras räkning. För att kunna signera användaren i måste du validera ID-token [exakt enligt beskrivningen](id-tokens.md#validating-an-id_token). Om du vill hämta åtkomsttoken följer du stegen som beskrivs i [dokumentationen för OAuth Code Flow](v2-oauth2-auth-code-flow.md#request-an-access-token).
