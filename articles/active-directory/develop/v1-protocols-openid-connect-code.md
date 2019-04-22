---
title: Förstå kodflöde för OpenID Connect-autentisering i Azure AD | Microsoft Docs
description: 'Den här artikeln beskriver hur du använder HTTP-meddelanden för att bevilja åtkomst till webbprogram och webb-API: er i din klient med Azure Active Directory och OpenID Connect.'
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/4/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06639f943542e322e79e137e31be7b8954566a0f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261997"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Bevilja åtkomst till webbprogram med hjälp av OpenID Connect och Azure Active Directory

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) är en enkel Identitetslagret som bygger på OAuth 2.0-protokollet. OAuth 2.0 definierar metoder för att skaffa och använda [ **åtkomsttoken** ](access-tokens.md) att komma åt skyddade resurser, men de inte definierar standardmetoder för att tillhandahålla identitetsinformation. OpenID Connect implementerar autentisering som en utökning av OAuth 2.0-auktoriseringsprocessen. Hittar du information om användaren i form av en [ `id_token` ](id-tokens.md) som verifierar användarens identitet och ger grundläggande profilinformation om användaren.

OpenID Connect är vår rekommendation om du skapar ett webbprogram som finns på en server och kan nås via en webbläsare.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Autentiseringsflöde med OpenID Connect

Det mest grundläggande inloggning flödet innehåller följande steg – var och en av dem som beskrivs i detalj nedan.

![OpenId Connect-Autentiseringsflödet](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>OpenID Connect-metadatadokument

OpenID Connect beskriver ett metadatadokument som innehåller de flesta av information som krävs för en app för att utföra logga in. Detta omfattar information, till exempel URL: er att använda och platsen för tjänstens offentliga Signeringsnycklar. Metadatadokument OpenID Connect finns på:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadata är ett vanligt JavaScript Object Notation (JSON)-dokument. Se följande kodavsnitt för ett exempel. Innehållet i kodfragment beskrivs ingående i den [OpenID Connect-specifikationen](https://openid.net). Observera att den ger en klient-ID istället `common` på plats {klient} ovan kommer att leda klientspecifik URI: er i JSON-objektet som returnerades.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

Om din app har anpassade Signeringsnycklar användningen av den [mappning av anspråk](active-directory-claims-mapping.md) funktion, som du måste lägga till en `appid` frågeparameter som innehåller app-ID för att få en `jwks_uri` som pekar på din app signeringsnyckel information. Till exempel: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` innehåller en `jwks_uri` av `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

## <a name="send-the-sign-in-request"></a>Skicka begäran inloggning

När ditt webbprogram måste autentisera användaren, den måste be användaren att den `/authorize` slutpunkt. Den här begäran liknar den första delen i den [flöde för OAuth 2.0 Authorization Code](v1-protocols-oauth-code.md), med några viktiga skillnader:

* Begäran måste innehålla omfånget `openid` i den `scope` parametern.
* Den `response_type` parameter måste innehålla `id_token`.
* Begäran måste innehålla den `nonce` parametern.

Så att en exempelförfrågan skulle se ut så här:

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
| klient |obligatorisk |Den `{tenant}` värdet i sökvägen för begäran som kan användas för att styra vem som kan logga in i programmet. Tillåtna värden är klient-ID: n, till exempel `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` för klient-oberoende token |
| client_id |obligatorisk |Program-ID som tilldelats din app när du registrerade med Azure AD. Du hittar du i Azure-portalen. Klicka på **Azure Active Directory**, klickar du på **Appregistreringar**, Välj programmet och leta upp det program-ID på programsidan. |
| response_type |obligatorisk |Måste innehålla `id_token` för OpenID Connect-inloggning. De kan också innehålla andra response_types som `code` eller `token`. |
| omfång | Rekommenderas | OpenID Connect-specifikationen kräver omfånget `openid`, vilket innebär att behörigheten ”logga du in” i godkännande-UI. Detta och andra OIDC-scope ignoreras på v1.0-slutpunkt, men är fortfarande bästa praxis för standardkompatibel klienter. |
| nonce |obligatorisk |Ett värde som ingår i den begäran som skapats av appen, som ingår i den resulterande `id_token` som ett anspråk. Appen kan sedan att verifiera det här värdet om du vill lösa token repetitionsattacker. Värdet är vanligtvis en slumpmässig, unik sträng eller ett GUID som kan användas för att fastställa ursprunget för begäran. |
| redirect_uri | Rekommenderas |Redirect_uri för din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av redirect_uris som du registrerade i portalen, men det måste vara url-kodas. Om det saknas, skickas användaragenten tillbaka till en av omdirigerings-URI: er har registrerats för appen, slumpmässigt. Maximal längd är 255 byte |
| response_mode |valfri |Anger den metod som ska användas för att skicka den resulterande authorization_code tillbaka till din app. Värden som stöds är `form_post` för *HTTP formuläret post* och `fragment` för *URL fragment*. För webbprogram, bör du använda `response_mode=form_post` så säkraste överföringen av token för ditt program. Standardvärdet för alla flöden, inklusive en id_token är `fragment`.|
| tillstånd |Rekommenderas |Ett värde i begäran som returneras i token-svaret. Det kan vara en sträng med innehåll som du önskar. Ett slumpmässigt genererat unikt värde som normalt används för [att förhindra attacker med förfalskning av begäran](https://tools.ietf.org/html/rfc6749#section-10.12). Tillstånd används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som de befann sig i. |
| fråga |valfri |Anger vilken typ av interaktion från användaren som krävs. De enda giltiga värdena är för närvarande ”inloggning”, ”ingen” och ”godkänna”. `prompt=login` Tvingar användaren att ange sina autentiseringsuppgifter på begäran, vilket eliminerar enkel inloggning. `prompt=none` är motsatsen - ser till att användaren inte visas med den interaktiva prompten alls. Om begäran inte kan slutföras tyst via enkel inloggning, returnerar slutpunkten ett fel. `prompt=consent` utlösare OAuth godkänner dialogrutan när användaren loggar in, ber användaren att bevilja behörigheter till appen. |
| login_hint |valfri |Kan användas för att fylla förväg adressfältet användarnamn/e-post i inloggningssidan för användaren, om du känner till sina användarnamn i tid. Appar som ofta använda den här parametern under omautentisering som redan har extraherats användarnamnet från en tidigare logga in med den `preferred_username` anspråk. |

Nu uppmanas användaren att ange sina autentiseringsuppgifter och slutför autentiseringen.

### <a name="sample-response"></a>Exempelsvar

En exempelsvaret när användaren har autentiserats kan se ut så här:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| id_token |Den `id_token` som appen har begärt. Du kan använda den `id_token` att verifiera användarens identitet och starta en session med användaren. |
| tillstånd |Ett värde i begäran som returneras också i token-svaret. Ett slumpmässigt genererat unikt värde som normalt används för [att förhindra attacker med förfalskning av begäran](https://tools.ietf.org/html/rfc6749#section-10.12). Tillstånd används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som de befann sig i. |

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
| fel |En felkodsträngen som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för slutpunkt-auktoriseringsfel

I följande tabell beskrivs olika felkoder som kan returneras i de `error` -parametern för felsvaret.

| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| invalid_request |Protokollfel, till exempel en obligatorisk parameter saknas. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och vanligtvis påträffades under första testningen. |
| unauthorized_client |Klientprogrammet har inte behörighet att begära en auktoriseringskod. |Detta inträffar vanligtvis när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan uppmana användaren med instruktion för att installera programmet och lägga till den till Azure AD. |
| access_denied |Resursägaren nekas godkännande |Klientprogrammet kan meddela användaren om att det går inte att fortsätta om inte användaren godkänner. |
| unsupported_response_type |Auktoriseringsservern stöder inte svarstypen i begäran. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och vanligtvis påträffades under första testningen. |
| server_error |Ett oväntat fel inträffade på servern. |Gör om begäran. Dessa fel kan bero på tillfälliga förhållanden. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt fel. |
| temporarily_unavailable |Servern är tillfälligt för upptagen för att hantera begäran. |Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd. |
| invalid_resource |Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det inte är korrekt konfigurerad. |Detta anger resursen om den finns, inte har konfigurerats på klienten. Programmet kan uppmana användaren med instruktion för att installera programmet och lägga till den till Azure AD. |

## <a name="validate-the-idtoken"></a>Verifiera id_token

Bara få ett `id_token` räcker inte att autentisera användaren; du måste verifiera signaturen och kontrollera anspråk i den `id_token` enligt krav för din app. Azure AD-slutpunkten använder JSON Web token (JWTs) och kryptering med offentlig nyckel för att signera token och kontrollera att de är giltiga.

Du kan välja att verifiera den `id_token` i klienten kod, men en vanlig metod är att skicka den `id_token` till backend-servern och utföra valideringen det. 

Du kan också välja att validera ytterligare anspråk beroende på ditt scenario. Vissa vanliga verifieringar är:

* Att se till att användaren/organisationen har registrerat sig för appen.
* Se till att användaren har rätt auktorisering/privilegier med hjälp av den `wids` eller `roles` anspråk. 
* Att se till att en viss styrkan hos autentisering har inträffat, till exempel multifaktorautentisering.

När du har verifierat den `id_token`, du kan starta en session med användaren och använda anspråk i den `id_token` att hämta information om användare i din app. Den här informationen kan användas för visning, poster, anpassning, osv. Mer information om `id_tokens` och anspråk, läsa [AAD id_tokens](id-tokens.md).

## <a name="send-a-sign-out-request"></a>Skicka en förfrågan om utloggning

När du vill logga ut användaren från appen, räcker det inte att rensa appens cookies eller på annat sätt slutet sessionen med användaren. Du måste också omdirigera användaren till den `end_session_endpoint` för utloggning. Om du inte göra det, kommer användaren att kunna autentiseras på nytt till din app utan att behöva ange sina autentiseringsuppgifter igen, eftersom de inte har en giltig inloggnings-session med Azure AD-slutpunkten.

Du kan helt enkelt omdirigera användaren till den `end_session_endpoint` som beskrivs i dokumentet för OpenID Connect metadata:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| post_logout_redirect_uri |Rekommenderas |Den URL som användaren ska omdirigeras till efter lyckad utloggning. Om inte ingår, visas ett allmänt meddelande med användaren. |

## <a name="single-sign-out"></a>Enkel utloggning

När du omdirigerar användaren till den `end_session_endpoint`, Azure AD tar bort användarens session i webbläsaren. Men kan användaren fortfarande vara inloggad till andra program som använder Azure AD för autentisering. Om du vill aktivera programmen utloggningen användaren samtidigt, Azure AD skickar en HTTP GET-begäran till det registrerade `LogoutUrl` över alla program som användaren för närvarande är inloggad på. Program måste svara på den här begäran genom att avmarkera alla sessioner som identifierar användaren och returnera en `200` svar. Om du vill stödja enkel inloggning ut i ditt program måste du implementera, till exempel en `LogoutUrl` i programkoden. Du kan ange den `LogoutUrl` från Azure portal:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj din Active Directory genom att klicka på ditt konto i det övre högra hörnet på sidan.
3. Välj den vänstra navigeringspanelen **Azure Active Directory**, välj sedan **appregistreringar** och välj ditt program.
4. Klicka på **inställningar**, sedan **egenskaper** och hitta den **URL för utloggning** textrutan. 

## <a name="token-acquisition"></a>Tokenförvärv
Många webbprogram måste inte bara logga in användaren i, utan också komma åt en webbtjänst som användaren med OAuth. Det här scenariot kombinerar OpenID Connect för autentisering av användare vid förvärv av samtidigt en `authorization_code` som kan användas för att hämta `access_tokens` med hjälp av den [OAuth Authorization Code Flow](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Få åtkomst-token
För att hämta åtkomsttoken som du behöver ändra inloggningsbegäran ovan:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Genom att inkludera behörighetsomfattningar i begäran och med hjälp av `response_type=code+id_token`, `authorize` endpoint garanterar att användaren har samtyckt till behörigheterna som anges i den `scope` frågeparameter och returnera appen en auktoriseringskod till exchange för en åtkomsttoken.

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
| Kod |Authorization_code som appen har begärt. Appen kan använda Auktoriseringskoden för att begära en åtkomsttoken för målresursen. Authorization_codes är korta bott och vanligtvis upphör att gälla efter cirka 10 minuter. |
| tillstånd |Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i begäran och svar är identiska. |

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
| fel |En felkodsträngen som kan användas för att klassificera typer av fel som inträffar och kan användas för att ta hänsyn till fel. |
| error_description |Ett felmeddelande som kan hjälpa utvecklare identifiera grundorsaken till ett autentiseringsfel. |

En beskrivning av möjliga felkoder och deras rekommenderade klientåtgärd i [felkoder för slutpunkt-auktoriseringsfel](#error-codes-for-authorization-endpoint-errors).

När du har fått en auktorisering `code` och en `id_token`, du kan logga in användaren och få [åtkomsttoken](access-tokens.md) å deras vägnar. Om du vill registrera användare i, måste du verifiera den `id_token` exakt så som beskrivs ovan. För att få åtkomsttoken kan du följa stegen som beskrivs i avsnittet ”använda Auktoriseringskoden för att begära en åtkomst-token” i vår [OAuth code flow-dokumentation](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Nästa steg

* Läs mer om den [åtkomsttoken](access-tokens.md).
* Läs mer om den [ `id_token` och anspråk](id-tokens.md).
