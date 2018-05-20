---
title: Azure Active Directory v2.0 och OpenID Connect-protokollet | Microsoft Docs
description: Skapa webbprogram med hjälp av Azure AD v2.0-implementeringen av autentiseringsprotokollet OpenID Connect.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a0cd077b1c6530c5794c92f131dffb814f5b341d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>Azure Active Directory v2.0 och OpenID Connect-protokoll
OpenID Connect är ett autentiseringsprotokoll som bygger på OAuth 2.0 som du kan använda för inloggning på ett säkert sätt en användare till ett webbprogram. När du använder v2.0-slutpunkten implementering av OpenID Connect kan du lägga till inloggnings- och API-åtkomst till webbaserade appar. I den här artikeln hur vi du gör detta oberoende av språk. Vi beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda alla bibliotek för Microsoft öppen källkod.

> [!NOTE]
> V2.0-slutpunkten har inte stöd för alla Azure Active Directory-scenarier och funktioner. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) utökar OAuth 2.0 *auktorisering* protokoll som ska användas som en *autentisering* protokoll, så att du kan använda enkel inloggning med hjälp av OAuth. OpenID Connect introducerar konceptet för en *ID token*, vilket är en säkerhetstoken som gör att klienten att verifiera användarens identitet. ID-token hämtar också grundläggande profilinformation om användaren. Eftersom OpenID Connect utökar OAuth 2.0, appar på ett säkert sätt hämta *åtkomst till token*, som kan användas för att komma åt resurser som skyddas av en [auktorisering server](active-directory-v2-protocols.md#the-basics). V2.0-slutpunkten kan också appar från tredje part som har registrerats med Azure AD för att utfärda åtkomsttoken för skyddade resurser, till exempel webb-API: er. Mer information om hur du ställer in ett program att utfärda åtkomsttoken finns [hur du registrerar en app med v2.0-slutpunkten](active-directory-v2-app-registration.md). Vi rekommenderar att du använder att OpenID Connect om du skapar en [webbprogrammet](active-directory-v2-flows.md#web-apps) som finns på en server och nås via en webbläsare.

## <a name="protocol-diagram-sign-in"></a>Protokollet diagram: Logga in
Det mest grundläggande flödet inloggning har de steg som visas i nästa diagram. Varje steg i detalj i den här artikeln beskrivs.

![OpenID Connect protocol: Logga in](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Hämta Metadatadokumentet OpenID Connect
OpenID Connect beskriver ett metadata-dokument som innehåller de flesta av information som krävs för att utföra inloggning. Detta omfattar information som de URL: er för att använda och platsen för tjänstens offentliga Signeringsnycklar. För v2.0-slutpunkten är Metadatadokumentet OpenID Connect bör du använda:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP] 
> Testa det! Klicka på [ https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration ](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) att se den `common` konfiguration för klienter. 
>

Den `{tenant}` kan göra något av fyra värden:

| Värde | Beskrivning |
| --- | --- |
| `common` |Användare med både ett personligt microsoftkonto och ett arbets- eller skolkonto konto från Azure Active Directory (Azure AD) kan logga in på programmet. |
| `organizations` |Endast användare med arbets- eller skolkonton från Azure AD kan logga in på programmet. |
| `consumers` |Endast användare med ett personligt microsoftkonto kan logga in på programmet. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` |Endast användare med ett arbets- eller skolkonto konto från en specifik Azure AD klient kan logga in på programmet. Kan användas antingen det egna namnet för Azure AD-klient eller klientens GUID-identifierare. |

Metadata är ett enkelt JavaScript Object Notation (JSON)-dokument. Se följande kodavsnitt ett exempel. Den fragment innehållet är fullständigt beskrivs i den [OpenID Connect specifikationen](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

Använder vanligtvis, Metadatadokumentet för att konfigurera en OpenID Connect biblioteket eller SDK; biblioteket använder metadata med sitt arbete. Om du inte använder ett före build OpenID Connect bibliotek, kan du följa stegen i resten av den här artikeln kan utföra inloggning i en webbapp med v2.0-slutpunkten.

## <a name="send-the-sign-in-request"></a>Skicka begäran om inloggning
När ditt webbprogram måste autentisera användaren, den kan dirigera användare till den `/authorize` slutpunkt. Den här begäran som liknar den första del av den [OAuth 2.0-auktoriseringskodflödet](active-directory-v2-protocols-oauth-code.md), med dessa viktiga skillnader:

* Begäran måste innehålla den `openid` scope i den `scope` parameter.
* Den `response_type` parameter måste innehålla `id_token`.
* Begäran måste innehålla den `nonce` parameter.

> [!IMPORTANT]
> Har för att begära en ID-token, appregistrering i den [registreringsportalen](https://apps.dev.microsoft.com) måste ha den **[Implicit bevilja](active-directory-v2-protocols-implicit.md)** aktiverad för den webbklienten. Om den inte är aktiverad ett `unsupported_response` felmeddelande: ”det angivna värdet för Indataparametern 'response_type' tillåts inte för den här klienten. Förväntat värde är 'code' ”

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
> Klicka på länken nedan om du vill utföra den här begäran. När du loggar in webbläsaren omdirigeras till https://localhost/myapp/, med en ID-token i adressfältet. Observera att denna begäran använder `response_mode=fragment` (endast i demonstrationssyfte). Vi rekommenderar att du använder `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| klient |Krävs |Du kan använda den `{tenant}` värde i sökvägen för begäran om att styra vem som kan logga in på programmet. Tillåtna värden är `common`, `organizations`, `consumers`, och identifierare för innehavare. Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints). |
| client_id |Krävs |Programmet ID som den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tilldelats din app. |
| response_type |Krävs |Måste innehålla `id_token` för OpenID Connect inloggning. Det kan även innehålla andra `response_types` värden, till exempel `code`. |
| redirect_uri |Rekommenderas |Omdirigerings-URI för din app, där autentisering svar kan skickas och tas emot av din app. Den måste matcha en omdirigerings-URI: er som du har registrerat i portalen, förutom att det måste vara URL-kodade. |
| omfång |Krävs |En blankstegsavgränsad lista över scope. Det måste innehålla omfånget för OpenID Connect `openid`, vilket innebär att behörigheten ”logga in dig på” i medgivande Användargränssnittet. Du kan även innehålla andra scope i den här förfrågan för att begära godkännande. |
| temporärt ID |Krävs |Ett värde som ingår i denna begäran som genererats av den app som ska inkluderas i det resulterande id_token värdet som ett anspråk. Appen kan kontrollera värdet för att minimera token replay-attacker. Värdet är vanligtvis en slumpmässig, unik sträng som används för att identifiera ursprunget för begäran. |
| response_mode |Rekommenderas |Anger den metod som ska användas för att skicka den resulterande Auktoriseringskoden tillbaka till din app. Det kan vara `form_post` eller `fragment`. För webbprogram, bör du använda `response_mode=form_post`, för att säkerställa säkraste överföring av token för ditt program. |
| state |Rekommenderas |Ett värde som ingår i denna begäran som också kommer att returneras i token svaret. Det kan vara en sträng med innehåll. Ett slumpmässigt genererat unikt värde som normalt används för att [förhindra attacker med förfalskning av begäran](http://tools.ietf.org/html/rfc6749#section-10.12). Tillståndet också används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis sidan eller vyn som användaren har på. |
| kommandotolk |Valfri |Anger vilken typ av användarinteraktion som krävs. De enda giltiga värdena just nu är `login`, `none`, och `consent`. Den `prompt=login` anspråk Tvingar användaren att ange sina autentiseringsuppgifter på begäran, vilket negeras enkel inloggning. Den `prompt=none` anspråk är motsatsen. Detta anspråk säkerställer att användaren inte visas med en interaktiv prompt som helst. Om begäran inte kan slutföras tyst via enkel inloggning, returneras ett fel i v2.0-slutpunkten. Den `prompt=consent` anspråk utlöser dialogrutan OAuth-medgivande när användaren loggar in. Dialogrutan ombeds användaren att tilldela behörigheter till appen. |
| login_hint |Valfri |Du kan använda den här parametern för att fylla före adressfältet användarnamn och e-post i inloggningssidan för användaren, om du känner till användarnamnet i förväg. Ofta appar att använda den här parametern under återautentiseringen efter redan extraherar användarnamnet från en tidigare inloggning med hjälp av den `preferred_username` anspråk. |
| domain_hint |Valfri |Det här värdet kan vara `consumers` eller `organizations`. Om ingår, hoppar e-postbaserad identifieringsprocessen som användaren som passerar på sidan v2.0-inloggning för en något mer effektiv användarupplevelse. Ofta appar att använda den här parametern under återautentiseringen genom att extrahera den `tid` anspråk från ID-token. Om den `tid` anspråk värdet är `9188040d-6c67-4c5b-b112-36a304b66dad` (Account konsumenten innehavaren), Använd `domain_hint=consumers`. Annars använder `domain_hint=organizations`. |

Nu uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen. V2.0-slutpunkten verifierar att användaren har godkänt för de behörigheter som anges i den `scope` Frågeparametern. Om användaren inte har godkänt till någon av dessa behörigheter, uppmanar v2.0-slutpunkten användaren att godkänna behörigheterna som krävs. Du kan läsa mer om [behörigheter, medgivande och flera appar](active-directory-v2-scopes.md).

När användaren autentiserar och ger samtycke, v2.0-slutpunkten returnerar ett svar till din app på den angivna omdirigerings-URI med hjälp av metoden som anges i den `response_mode` parameter.

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
| id_token |ID-token som appen har begärt. Du kan använda den `id_token` parametern för att verifiera användarens identitet och starta en session med användaren. Mer information om ID-token och deras innehåll finns i [v2.0-slutpunkten tokens referens](active-directory-v2-tokens.md). |
| state |Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i förfrågan och svar är identiska. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till omdirigerings-URI så att appen kan hanteras. Ett felsvar ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felkod sträng som du kan använda för att klassificera typer av fel som inträffar och att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för auktorisering endpoint fel
I följande tabell beskrivs felkoder som kan returneras i den `error` -parametern för felsvaret:

| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| invalid_request |Protokollfel, till exempel det saknas nödvändiga parametern. |Åtgärda och skicka begäran igen. Detta är en utvecklingsfel vanligtvis som standard under inledande testningen. |
| unauthorized_client |Klientprogrammet kan inte begära en auktoriseringskod. |Detta inträffar vanligtvis när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan uppmana användaren med instruktioner för att installera programmet och lägga till den i Azure AD. |
| Om ACCESS_DENIED |Resursägaren nekade medgivande. |Klientprogrammet kan meddela användaren om att det går inte att fortsätta om användaren godkänner. |
| unsupported_response_type |Auktorisering servern stöder inte svarstypen i begäran. |Åtgärda och skicka begäran igen. Detta är en utvecklingsfel vanligtvis som standard under inledande testningen. |
| server_error |Ett oväntat fel uppstod på servern. |Gör om begäran. Dessa fel kan bero på tillfälliga förhållanden. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt fel. |
| temporarily_unavailable |Servern är tillfälligt för upptagen för att hantera begäran. |Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd. |
| invalid_resource |Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det är inte korrekt konfigurerad. |Detta anger att resursen, om den finns inte har konfigurerats i klienten. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till den i Azure AD. |

## <a name="validate-the-id-token"></a>Verifiera ID-token
Ta emot en token med ID: T är inte tillräckliga för att autentisera användaren. Du måste också verifiera signaturen för ID-token och kontrollera anspråk i token per krav som din app. V2.0-slutpunkten använder [JSON Web token (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentlig nyckel att signera token och kontrollera att de är giltiga.

Du kan välja att validera ID-token i klientkod, men en vanlig metod är att skicka ID-token till en backend server och utföra valideringen det. När du har verifierats signaturen för token ID, behöver du verifiera några anspråk. Mer information, inklusive information om [verifiera token](active-directory-v2-tokens.md#validating-tokens) och [viktig information om att signera nyckelförnyelse](active-directory-v2-tokens.md#validating-tokens), finns det [v2.0 tokens referens](active-directory-v2-tokens.md). Vi rekommenderar att du använder ett bibliotek att tolka och validera token. Det finns minst en av dessa bibliotek för de flesta språk och plattformar.
<!--TODO: Improve the information on this-->

Du kanske också vill validera ytterligare anspråk, beroende på ditt scenario. Några vanliga verifieringar inkluderar:

* Se till att användaren eller organisationen har registrerat dig för appen.
* Se till att användaren har nödvändiga behörighet eller auktorisering.
* Se till att en viss styrkan hos autentisering har inträffat, till exempel multifaktorautentisering.

Mer information om anspråk i en token med ID: T finns på [v2.0-slutpunkten tokens referens](active-directory-v2-tokens.md).

När du har fullständigt validerat ID-token, kan du börja en session med användaren. Använda anspråk i ID-token för att få information om användaren i din app. Du kan använda den här informationen för att visa, poster, tillstånd och så vidare.

## <a name="send-a-sign-out-request"></a>Skicka en begäran om utloggning
När du vill logga ut användaren från din app, det är inte tillräckliga för att rensa cookies för din app eller på annat sätt avsluta användarens session. Du måste också omdirigera användaren till v2.0-slutpunkten logga ut. Om du inte gör det autentiserar igen användaren i appen utan att ange sina autentiseringsuppgifter igen, eftersom de har en giltig inloggning session med v2.0-slutpunkten.

Du kan dirigera användare till den `end_session_endpoint` som anges i Metadatadokumentet OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | Villkor | Beskrivning |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Rekommenderas | Den URL som användaren omdirigeras till efter att logga ut. Om parametern inte är inkluderad visas användaren ett allmänt meddelande som genereras av v2.0-slutpunkten. URL: en måste matcha en omdirigerings-URI: er som registrerats för ditt program i portalen för registrering av app. |

## <a name="single-sign-out"></a>Enkel utloggning
När du dirigerar användaren till den `end_session_endpoint`, v2.0-slutpunkten rensar användarens session från webbläsaren. Men kan användaren fortfarande vara inloggad till andra program som använder Microsoft-konton för autentisering. Aktivera programmen att logga ut samtidigt, v2.0 användaren endpoint skickar en HTTP GET-begäran till det registrerade `LogoutUrl` för alla program som användaren är inloggad på. Program måste svara på begäran genom att avmarkera alla sessioner som identifierar användaren och returnera ett `200` svar. Om du vill stödja enkel inloggning ut i ditt program måste du implementera exempelvis en `LogoutUrl` i din programkod. Du kan ange den `LogoutUrl` från portalen för registrering av app.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokollet diagram: komma åt token förvärv
Många webbprogram måste inte bara logga användaren i, men också att få åtkomst till en webbtjänst för användarens räkning genom att använda OAuth. Det här scenariot kombinerar OpenID Connect för autentisering av användare vid hämtning av en auktoriseringskod som du kan använda för att få åtkomst-token om du använder OAuth-auktoriseringskodflödet samtidigt.

Fullständig OpenID Connect-inloggning och token förvärv flödet ser ut ungefär så nästa diagrammet. Vi beskriver varje steg i detalj i följande avsnitt i artikeln.

![OpenID Connect protocol: Token förvärv](../../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>Få åtkomst-token
Ändra begäran logga in för att få åtkomst-token:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Klicka på länken nedan om du vill utföra den här begäran. När du loggar in webbläsaren omdirigeras till https://localhost/myapp/, med en ID-token och koden i adressfältet. Observera att denna begäran använder `response_mode=fragment` (endast i demonstrationssyfte). Vi rekommenderar att du använder `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

Genom att inkludera behörighetsomfattningen i begäran och genom att använda `response_type=id_token code`, v2.0-slutpunkten garanterar att användaren har godkänt för de behörigheter som anges i den `scope` Frågeparametern. Den returnerar ett Auktoriseringskoden till din app till exchange-åtkomst-token.

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
| id_token |ID-token som appen har begärt. Du kan använda ID-token för att verifiera användarens identitet och starta en session med användaren. Du hittar mer information om ID-token och innehållet i den [v2.0-slutpunkten tokens referens](active-directory-v2-tokens.md). |
| Koden |Auktoriseringskoden som begärts av appen. Appen kan använda Auktoriseringskoden för att begära en åtkomst-token för målresursen. En Auktoriseringskoden är mycket tillfällig. Normalt en auktoriseringskod upphör att gälla inom ca 10 minuter. |
| state |Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i förfrågan och svar är identiska. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till omdirigerings-URI så att appen kan hantera dem på lämpligt sätt. Ett felsvar ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felkod sträng som du kan använda för att klassificera typer av fel som inträffar och att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |

En beskrivning av felkoder och rekommenderade Klientsvar finns [felkoder för auktorisering endpoint fel](#error-codes-for-authorization-endpoint-errors).

När du har en Auktoriseringskoden och en ID-token kan du logga in användaren och få åtkomst-token för deras räkning. Om du vill registrera användaren i måste du verifiera token ID [exakt så som det beskrivs](#validate-the-id-token). För att få åtkomst-token, Följ stegen som beskrivs i vår [dokumentationen för OAuth-protokollet](active-directory-v2-protocols-oauth-code.md#request-an-access-token).
