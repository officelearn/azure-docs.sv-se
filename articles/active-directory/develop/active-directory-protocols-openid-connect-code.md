---
title: Flödet OpenID Connect autentisering kod i Azure AD | Microsoft Docs
description: 'Den här artikeln beskriver hur du använder HTTP-meddelanden för att bevilja åtkomst till webbprogram och webb-API: er i din klient med hjälp av Azure Active Directory och OpenID Connect.'
services: active-directory
documentationcenter: .net
author: hpsin
manager: mtillman
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4d9593aad789a9888c32297d634ba19e669bd461
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Bevilja åtkomst till webbprogram med hjälp av OpenID Connect och Azure Active Directory
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) är en enkel Identitetslagret som bygger på OAuth 2.0-protokollet. OAuth 2.0 definierar metoder för att hämta och använda **åtkomst till token** att komma åt skyddade resurser, men inte definierar standardmetoder för att ge ID-information. OpenID Connect implementerar autentisering som ett tillägg till auktoriseringen OAuth 2.0. Den ger information om användaren i form av en `id_token` som verifierar användarens identitet och ger grundläggande profilinformation om användaren.

OpenID Connect är vår rekommendation om du skapar ett program som finns på en server och som nås via en webbläsare.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Autentiseringsflödet med OpenID Connect
Det mest grundläggande flödet inloggning innehåller följande steg - dem beskrivs i detalj nedan.

![OpenId Connect Autentiseringsflödet](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>OpenID Connect Metadatadokumentet

OpenID Connect beskriver ett metadata-dokument som innehåller de flesta av information som krävs för att utföra inloggning. Detta omfattar information som de URL: er för att använda och platsen för tjänstens offentliga Signeringsnycklar. Metadatadokumentet OpenID Connect finns på:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadata är ett enkelt JavaScript Object Notation (JSON)-dokument. Se följande kodavsnitt ett exempel. Den fragment innehållet är fullständigt beskrivs i den [OpenID Connect specifikationen](https://openid.net).

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/common/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/common/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    
    ...
}
```

## <a name="send-the-sign-in-request"></a>Skicka begäran om inloggning
När ditt webbprogram måste autentisera användaren, måste den dirigera användare till den `/authorize` slutpunkt. Den här begäran som liknar den första del av den [OAuth 2.0 auktorisering kod flöda](active-directory-protocols-oauth-code.md), med några viktiga skillnader:

* Begäran måste innehålla omfånget `openid` i den `scope` parameter.
* Den `response_type` parameter måste innehålla `id_token`.
* Begäran måste innehålla den `nonce` parameter.

Så att en exempel-begäran skulle se ut så här:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| klient |Krävs |Den `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet.  Tillåtna värden är klient-ID: n, till exempel `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` för klient-oberoende token |
| client_id |Krävs |Program-Id som tilldelats din app när du har registrerat med Azure AD. Du hittar du i Azure Portal. Klicka på **Azure Active Directory**, klickar du på **App registreringar**, Välj programmet och leta upp det program-Id på sidan för programmet. |
| response_type |Krävs |Måste innehålla `id_token` för OpenID Connect inloggning.  Den kan också omfatta andra response_types som `code`. |
| omfång |Krävs |En blankstegsavgränsad lista över scope.  Det måste innehålla omfånget för OpenID Connect `openid`, vilket innebär att behörigheten ”logga in dig på” i medgivande Användargränssnittet.  Du kan också omfatta andra scope i den här förfrågan för att begära godkännande. |
| temporärt ID |Krävs |Ett värde som ingår i den begäran som skapats av appen, som ingår i den resulterande `id_token` som ett anspråk.  Appen kan sedan kontrollera värdet för att minimera token replay-attacker.  Värdet är vanligtvis en slumpmässig, unik sträng eller ett GUID som används för att identifiera ursprunget för begäran. |
| redirect_uri |Rekommenderas |Redirect_uri för din app, där autentisering svar kan skickas och tas emot av din app.  Den måste matcha en redirect_uris som du har registrerat i portalen, förutom det måste vara url-kodade. |
| response_mode |Rekommenderas |Anger den metod som ska användas för att skicka den resulterande authorization_code tillbaka till din app.  Värden som stöds är `form_post` för *HTTP formuläret post* och `fragment` för *URL-fragment*.  För webbprogram, bör du använda `response_mode=form_post` så säkraste överföringen av token för ditt program. Standard, om `response_mode` inte är inkluderad, är `fragment`.|
| state |Rekommenderas |Ett värde som ingår i denna begäran som returneras i token svaret.  Det kan vara en sträng med innehåll som du vill.  Ett slumpmässigt genererat unikt värde används vanligtvis för [förhindra attacker med förfalskning av begäran](http://tools.ietf.org/html/rfc6749#section-10.12).  Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis sidan eller de befann sig i vyn. |
| kommandotolk |valfri |Anger vilken typ av användarinteraktion som krävs.  De enda giltiga värdena är för närvarande ”inloggning” none, och 'godkännande'.  `prompt=login` Tvingar användaren att ange sina autentiseringsuppgifter på begäran, giltigt att negera enkel inloggning på.  `prompt=none` är motsatsen - ser till att användaren inte visas med en interaktiv prompt som helst.  Om begäran inte kan slutföras utan meddelanden via enkel inloggning på returnerar slutpunkten ett fel.  `prompt=consent` utlösare av OAuth medgivande dialogrutan när användaren loggar in, be användaren att tilldela behörigheter till appen. |
| login_hint |valfri |Kan användas för att fylla före adressfältet användarnamn/e-post i inloggningssidan för användaren, om du känner till sitt lösenord i förväg.  Ofta appar använder den här parametern under omautentisering som redan har extraherats användarnamnet från en tidigare inloggning med hjälp av den `preferred_username` anspråk. |

Nu uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen.

### <a name="sample-response"></a>Exempelsvar
Ett exempelsvar, när användaren har autentiserats kan se ut så här:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| id_token |Den `id_token` som appen har begärt. Du kan använda den `id_token` att verifiera användarens identitet och starta en session med användaren. |
| state |Ett värde som ingår i denna begäran returneras också token svar. Ett slumpmässigt genererat unikt värde används vanligtvis för [förhindra attacker med förfalskning av begäran](http://tools.ietf.org/html/rfc6749#section-10.12).  Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, exempelvis sidan eller de befann sig i vyn. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till den `redirect_uri` så att appen kan hantera dem på rätt sätt:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felkod sträng som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för auktorisering endpoint fel
I följande tabell beskrivs de olika felkoder som kan returneras i den `error` parameter för felsvar.

| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| invalid_request |Protokollfel, till exempel en obligatorisk parameter saknas. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och vanligtvis fångas upp under inledande testningen. |
| unauthorized_client |Klientprogrammet är inte tillåtet att begära en auktoriseringskod. |Detta inträffar vanligtvis när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan be användare med instruktioner för att installera programmet och lägga till den i Azure AD. |
| Om ACCESS_DENIED |Resursägare nekad medgivande |Klientprogrammet kan meddela användaren om att det går inte att fortsätta om användaren godkänner. |
| unsupported_response_type |Auktorisering servern stöder inte svarstypen i begäran. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och vanligtvis fångas upp under inledande testningen. |
| server_error |Ett oväntat fel uppstod på servern. |Gör om begäran. Dessa fel kan bero på tillfälliga förhållanden. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt fel. |
| temporarily_unavailable |Servern är tillfälligt för upptagen för att hantera begäran. |Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd. |
| invalid_resource |Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det är inte korrekt konfigurerad. |Detta anger resursen, om det finns inte har konfigurerats i klienten. Programmet kan be användare med instruktioner för att installera programmet och lägga till den i Azure AD. |

## <a name="validate-the-idtoken"></a>Verifiera id_token
Bara tar emot en `id_token` räcker inte att autentisera användaren; du måste verifiera signaturen och kontrollera anspråk i den `id_token` per krav som din app. Azure AD-slutpunkten använder JSON Web token (JWTs) och kryptering med offentlig nyckel för att signera token och kontrollera att de är giltiga.

Du kan välja att validera den `id_token` i klienten koden, men en vanlig metod är att skicka den `id_token` till backend-servern och utföra valideringen det. När du har verifiera signaturen för den `id_token`, det finns några anspråk som krävs för att verifiera.

Du kan också kontrollera ytterligare anspråk beroende på ditt scenario. Några vanliga verifieringar inkluderar:

* Att säkerställa användarorganisation har registrerat dig för appen.
* Att användaren har rätt behörighet/behörighet
* Säkerställa en vissa styrkan hos autentisering har inträffat, till exempel multifaktorautentisering.

När du har validerat den `id_token`, du kan starta en session med användaren och använda anspråk i den `id_token` att hämta information om användare i din app. Den här informationen kan användas för att visa, poster, tillstånd och så vidare. Mer information om typer av token och anspråk [stöds Token och anspråkstyper](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Skicka en begäran om utloggning
När du vill logga ut från appen användaren räcker det inte att rensa din app cookies eller på annat sätt slutet sessionen med användaren.  Du måste också omdirigera användare till den `end_session_endpoint` för utloggning.  Om du inte göra det, kommer användaren att kunna autentiseras i appen utan att ange sina autentiseringsuppgifter igen, eftersom de har en giltig inloggning session med Azure AD-slutpunkten.

Du kan bara dirigera användare till den `end_session_endpoint` som anges i Metadatadokumentet OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| post_logout_redirect_uri |Rekommenderas |Den URL som användaren ska omdirigeras till när du har lyckats logga ut.  Om inte ingår, visas användaren ett allmänt meddelande. |

## <a name="single-sign-out"></a>Enkel utloggning
När du dirigerar användaren till den `end_session_endpoint`, Azure AD tar bort användarens session från webbläsaren. Men kan användaren fortfarande vara inloggad till andra program som använder Azure AD för autentisering. Om du vill aktivera programmen för att logga ut användaren samtidigt, Azure AD skickar en HTTP GET-begäran till det registrerade `LogoutUrl` för alla program som användaren är inloggad på. Program måste svara på begäran genom att avmarkera alla sessioner som identifierar användaren och returnera ett `200` svar.  Om du vill stödja enkel inloggning ut i ditt program måste du implementera exempelvis en `LogoutUrl` i din programkod.  Du kan ange den `LogoutUrl` från Azure portal:

1. Navigera till den [Azure-portalen](https://portal.azure.com).
2. Välj din Active Directory genom att klicka på ditt konto i det övre högra hörnet på sidan.
3. Vänstra navigeringsfönstret, Välj **Azure Active Directory**, Välj **App registreringar** och välj ditt program.
4. Klicka på **inställningar**, sedan **egenskaper** och Sök efter den **logga ut URL** textruta. 

## <a name="token-acquisition"></a>Token förvärv
Många webbprogram måste inte bara logga in användaren i, utan också komma åt en webbtjänst som användaren använder sig av OAuth. Det här scenariot kombinerar OpenID Connect för autentisering av användare vid hämtning av samtidigt en `authorization_code` som kan användas för att hämta `access_tokens` med hjälp av den [OAuth Authorization kod flöda](active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Få åtkomst-token
Om du vill hämta åtkomsttoken som du behöver ändra begäranden från ovan:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                     
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Genom att inkludera behörighetsomfattningen i begäran och `response_type=code+id_token`, `authorize` endpoint garanterar att användaren har godkänt för de behörigheter som anges i den `scope` Frågeparametern och returkod appen ett tillstånd till exchange en åtkomst-token.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar med `response_mode=form_post` ser ut som:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| id_token |Den `id_token` som appen har begärt. Du kan använda den `id_token` att verifiera användarens identitet och starta en session med användaren. |
| Koden |Authorization_code som begärts av appen. Appen kan använda Auktoriseringskoden för att begära en åtkomst-token för målresursen. Authorization_codes är kort livslängd och vanligtvis ut efter 10 minuter. |
| state |Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i förfrågan och svar är identiska. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till den `redirect_uri` så att appen kan hantera dem på rätt sätt:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett felkod sträng som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera orsaken till ett autentiseringsfel. |

En beskrivning av möjliga felkoder och deras rekommenderade klientåtgärd finns [felkoder för auktorisering endpoint fel](#error-codes-for-authorization-endpoint-errors).

När du har tagit emot ett tillstånd `code` och en `id_token`, du kan logga in användaren och få åtkomst-token för deras räkning.  Du måste validera om du vill registrera användaren i den `id_token` exakt så som beskrivs ovan. För att få åtkomst-token kan du följa stegen som beskrivs i avsnittet ”använda Auktoriseringskoden för att begära en åtkomst-token” i vår [dokumentationen för OAuth-protokollet](active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
