---
title: Auktorisera åtkomst till webbapp med OpenID Connect & Azure AD | Microsoft-dokument
description: I den hÃ¤r artikeln beskrivs hur du ankÃ¤nder HTTP-meddelanden fÃ¤r att auktorisera åtkomst till webbprogram och webb-API:er i din klient med Azure Active Directory och OpenID Connect.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eef8174056be7e6be35cea56788c0a519d02944e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154448"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Ge åtkomst till webbprogram med hjälp av OpenID Connect och Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) är ett enkelt identitetslager byggt ovanpå OAuth 2.0-protokollet. OAuth 2.0 definierar mekanismer för att hämta och använda [**åtkomsttoken**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för att komma åt skyddade resurser, men de definierar inte standardmetoder för att tillhandahålla identitetsinformation. OpenID Connect implementerar autentisering som ett tillägg till OAuth 2.0-auktoriseringsprocessen. Den ger information om slutanvändaren i [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) form av en som verifierar användarens identitet och ger grundläggande profilinformation om användaren.

OpenID Connect är vår rekommendation om du bygger ett webbprogram som finns på en server och nås via en webbläsare.

## <a name="register-your-application-with-your-ad-tenant"></a>Registrera ditt program med din AD-klient
Registrera först ditt program med din Azure Active Directory -klientorganisation (Azure AD). Det ger dig en program-ID för ditt program och låter det ta emot tokens.

1. Logga in på [Azure-portalen](https://portal.azure.com).
   
1. Välj din Azure AD-klient genom att välja ditt konto längst upp till höger på sidan, följt av att välja **Switch Directory-navigeringen** och sedan välja lämplig klient. 
   - Hoppa över det här steget om du bara har en Azure AD-klient under ditt konto, eller om du redan har valt lämplig Azure AD-klientorganisation.
   
1. Sök efter och välj **Azure Active Directory**i Azure-portalen .
   
1. På **vänster** Azure Active Directory-menyn väljer du **Appregistreringar**och väljer sedan **Ny registrering**.
   
1. Följ anvisningarna och skapa ett nytt program. Det spelar ingen roll om det är ett webbprogram eller en offentlig klient (mobil & skrivbord) ansökan om den här guiden, men om du vill ha specifika exempel för webbapplikationer eller offentliga klientprogram, kolla in våra [snabbstarter](v1-overview.md).
   
   - **Namn** är appens namn och beskriver appen för användarna.
   - Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
   - Ange **redirect-URI.** För webbprogram är detta den grundläggande URL:en för din app där användarna kan logga in.  Till exempel `http://localhost:12345`. För offentliga klienter (mobila &-skrivbordet) använder Azure AD den för att returnera tokensvar. Ange ett specifikt värde för ditt program.  Till exempel `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. När du har slutfört registreringen tilldelar Azure AD ditt program en unik klientidentifierare **(program-ID).** Du behöver det här värdet i nästa avsnitt, så kopiera det från programsidan.
   
1. Om du vill hitta ditt program i Azure-portalen väljer du **Appregistreringar**och väljer sedan **Visa alla program**.

## <a name="authentication-flow-using-openid-connect"></a>Autentiseringsflöde med OpenID Connect

Det mest grundläggande inloggningsflödet innehåller följande steg - var och en av dem beskrivs i detalj nedan.

![Autentiseringsflöde för OpenId Connect](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Metadatadokument för OpenID Connect

OpenID Connect beskriver ett metadatadokument som innehåller det mesta av den information som krävs för att en app ska kunna utföra inloggning. Detta inkluderar information som webbadresser som ska användas och platsen för tjänstens offentliga signeringsnycklar. Metadatadokumentet för OpenID Connect finns på:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadata är ett enkelt JavaScript Object Notation (JSON) dokument. Se följande kodavsnitt för ett exempel. Innehållet i kodavsnittet beskrivs fullständigt i [OpenID Connect-specifikationen](https://openid.net). Observera att om du anger `common` ett klient-ID i stället för {klient} ovan kommer det att resultera i klientspecifika URI:er i JSON-objektet som returneras.

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

Om din app har anpassade signeringsnycklar som ett resultat av `appid` att använda funktionen [för anspråksmappning](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) måste du lägga till en frågeparameter som innehåller app-ID:et för att få en `jwks_uri` pekar på appens information om signeringsnyckeln. Till exempel: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` innehåller `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`en av .

## <a name="send-the-sign-in-request"></a>Skicka inloggningsbegäran

När webbprogrammet måste autentisera användaren måste användaren dirigeras till `/authorize` slutpunkten. Denna begäran liknar den första delen av [OAuth 2.0 Authorization Code Flow](v1-protocols-oauth-code.md), med några viktiga distinktioner:

* Begäran måste innehålla `openid` scopet `scope` i parametern.
* Parametern `response_type` måste `id_token`innehålla .
* Begäran måste innehålla `nonce` parametern.

Så ett exempel begäran skulle se ut så här:

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
| tenant |krävs |Värdet `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är klientidentifierare, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` till exempel eller `contoso.onmicrosoft.com` eller `common` för klientoberoende token |
| client_id |krävs |Program-ID:t som tilldelats din app när du registrerade det med Azure AD. Du hittar detta i Azure-portalen. Klicka på **Azure Active Directory**, klicka på **Appregistreringar,** välj programmet och leta upp program-ID:et på programsidan. |
| response_type |krävs |Måste `id_token` inkludera för OpenID Connect-inloggning. Det kan också omfatta andra response_types, `code` `token`till exempel eller . |
| omfång | Rekommenderas | OpenID Connect-specifikationen `openid`kräver scopet , som översätts till behörigheten "Logga in dig" i användargränssnittet för medgivande. Detta och andra OIDC-scope ignoreras på v1.0-slutpunkten, men är fortfarande en bästa praxis för standardkompatibla klienter. |
| Nonce |krävs |Ett värde som ingår i begäran, som genereras av `id_token` appen, som ingår i resultatet som ett anspråk. Appen kan sedan verifiera det här värdet för att minska token reprisattacker. Värdet är vanligtvis en randomiserad, unik sträng eller GUID som kan användas för att identifiera begärans ursprung. |
| redirect_uri | Rekommenderas |Den redirect_uri i din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av de redirect_uris du registrerat i portalen, förutom att den måste url-kodas. Om det saknas skickas användaragenten tillbaka till en av de omdirigerings-URI:er som är registrerade för appen, slumpmässigt. Den maximala längden är 255 byte |
| response_mode |valfri |Anger den metod som ska användas för att skicka tillbaka den resulterande authorization_code till din app. Värden som `form_post` stöds är `fragment` för *HTTP-formulärinlägg* och för *URL-fragment*. För webbapplikationer `response_mode=form_post` rekommenderar vi att du använder för att säkerställa den säkraste överföringen av token till ditt program. Standardvärdet för alla flöde inklusive `fragment`en id_token är .|
| state |Rekommenderas |Ett värde som ingår i begäran som returneras i tokensvaret. Det kan vara en sträng av allt innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligtvis för [att förhindra förfalskningsattacker mellan webbplatser](https://tools.ietf.org/html/rfc6749#section-10.12). Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på. |
| Snabb |valfri |Anger vilken typ av användarinteraktion som krävs. För närvarande är de enda giltiga värdena "login", "none" och "consent". `prompt=login`tvingar användaren att ange sina autentiseringsuppgifter på den begäran, vilket motverkar enkel inloggning. `prompt=none`är motsatsen - det säkerställer att användaren inte presenteras med någon interaktiv uppmaning som helst. Om begäran inte kan slutföras tyst via enkel inloggning returnerar slutpunkten ett fel. `prompt=consent`utlöser dialogrutan OAuth consent när användaren loggar in och ber användaren att bevilja behörigheter till appen. |
| login_hint |valfri |Kan användas för att förfylla fältet användarnamn/e-postadress på inloggningssidan för användaren, om du känner till deras användarnamn i förväg. Ofta använder appar den här parametern under omautentisering, efter att redan ha `preferred_username` extraherat användarnamnet från en tidigare inloggning med anspråket. |

Nu uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen.

### <a name="sample-response"></a>Exempelsvar

Ett exempelsvar som `redirect_uri` skickas till den angivna inloggningsbegäran efter att användaren har autentiserats kan se ut så här:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| id_token |Den `id_token` som appen begärde. Du kan `id_token` använda för att verifiera användarens identitet och påbörja en session med användaren. |
| state |Ett värde som ingår i begäran som också returneras i tokensvaret. Ett slumpmässigt genererat unikt värde används vanligtvis för [att förhindra förfalskningsattacker mellan webbplatser](https://tools.ietf.org/html/rfc6749#section-10.12). Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på. |

### <a name="error-response"></a>Felsvar

Felsvar kan också skickas `redirect_uri` till så att appen kan hantera dem på rätt sätt:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträng som kan användas för att klassificera typer av fel som uppstår och som kan användas för att reagera på fel. |
| error_description |Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera orsaken till ett autentiseringsfel. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för auktoriseringsslutpunktsfel

I följande tabell beskrivs de olika felkoder `error` som kan returneras i parametern för felsvaret.

| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| invalid_request |Protokollfel, till exempel en parameter som saknas som krävs. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och fångas vanligtvis under inledande testning. |
| unauthorized_client |Klientprogrammet har inte behörighet att begära en auktoriseringskod. |Detta inträffar vanligtvis när klientprogrammet inte är registrerat i Azure AD eller inte läggs till användarens Azure AD-klientorganisation. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD. |
| access_denied |Resursägaren nekas samtycke |Klientprogrammet kan meddela användaren att det inte kan fortsätta om inte användaren samtycker. |
| unsupported_response_type |Auktoriseringsservern stöder inte svarstypen i begäran. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel och fångas vanligtvis under inledande testning. |
| server_error |Servern påträffade ett oväntat fel. |Försök igen. Dessa fel kan bero på tillfälliga villkor. Klientprogrammet kan förklara för användaren att svaret är försenat på grund av ett tillfälligt fel. |
| temporarily_unavailable |Servern är tillfälligt för upptagen för att hantera begäran. |Försök igen. Klientprogrammet kan förklara för användaren att svaret är försenat på grund av ett tillfälligt tillstånd. |
| invalid_resource |Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller så är den inte korrekt konfigurerad. |Detta indikerar att resursen, om den finns, inte har konfigurerats i klienten. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD. |

## <a name="validate-the-id_token"></a>Validera id_token

Bara ta `id_token` emot en är inte tillräckligt för att autentisera användaren; Du måste validera signaturen och `id_token` verifiera anspråken i kraven per appen. Azure AD-slutpunkten använder JSON Web Tokens (JWTs) och kryptering med offentliga nycklar för att signera token och verifiera att de är giltiga.

Du kan välja `id_token` att validera klientkoden i klientkoden, `id_token` men en vanlig metod är att skicka till en server för serveringsserver och utföra valideringen där. 

Du kanske också vill validera ytterligare anspråk beroende på ditt scenario. Några vanliga valideringar är:

* Se till att användaren/organisationen har registrerat sig för appen.
* Se till att användaren har rätt `wids` `roles` behörighet/behörighet med hjälp av anspråk eller anspråk. 
* Se till att en viss styrka av autentisering har inträffat, till exempel multifaktorautentisering.

När du har `id_token`validerat kan du starta en session med `id_token` användaren och använda anspråken i för att få information om användaren i appen. Denna information kan användas för visning, poster, personalisering, etc. Mer information `id_tokens` om och anspråk finns i [AAD id_tokens](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="send-a-sign-out-request"></a>Skicka en ut signeringsbegäran

När du vill logga ut användaren från appen är det inte tillräckligt att rensa appens cookies eller på annat sätt avsluta sessionen med användaren. Du måste också omdirigera `end_session_endpoint` användaren till för utloggningen. Om du inte gör det kan användaren återuthtentisera till din app utan att ange sina autentiseringsuppgifter igen, eftersom de har en giltig enkel inloggningssession med Azure AD-slutpunkten.

Du kan helt enkelt `end_session_endpoint` omdirigera användaren till listan i OpenID Connect-metadatadokumentet:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| post_logout_redirect_uri |Rekommenderas |URL:en som användaren ska omdirigeras till efter lyckad utloggning.  Den här URL:en måste matcha en av de omdirigerings-URI:er som är registrerade för ditt program i appregistreringsportalen.  Om *post_logout_redirect_uri* inte ingår visas ett allmänt meddelande för användaren. |

## <a name="single-sign-out"></a>Enkel utloggning

När du omdirigerar `end_session_endpoint`användaren till rensar Azure AD användarens session från webbläsaren. Användaren kan dock fortfarande vara inloggad på andra program som använder Azure AD för autentisering. Azure AD skickar en HTTP GET-begäran till registrerade `LogoutUrl` av alla program som användaren för närvarande är inloggad på för att göra det möjligt för dessa program att logga ut samtidigt. Program måste svara på den här begäran genom att rensa `200` alla sessioner som identifierar användaren och returnera ett svar. Om du vill stödja enkel inloggning i din ansökan `LogoutUrl` måste du implementera en sådan i programmets kod. Du kan `LogoutUrl` ange från Azure-portalen:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj Active Directory genom att klicka på ditt konto längst upp till höger på sidan.
3. På navigeringspanelen till vänster väljer du **Azure Active Directory**och väljer sedan **Appregistreringar** och väljer ditt program.
4. Klicka på **Inställningar**, sedan **Egenskaper** och leta reda på textrutan **Utloggningsadress.** 

## <a name="token-acquisition"></a>Token förvärv
Många webbappar måste inte bara logga in användaren, utan även komma åt en webbtjänst för användarens räkning med OAuth. Det här scenariot kombinerar OpenID Connect för `authorization_code` användarautentisering samtidigt `access_tokens` som du hämtar en som kan användas för att hämta med [OAuth Authorization Code Flow](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Hämta åtkomsttoken
Om du vill hämta åtkomsttoken måste du ändra inloggningsbegäran ovanifrån:

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

Genom att inkludera behörighetsscope `response_type=code+id_token`i `authorize` begäran och använda säkerställer slutpunkten att användaren har `scope` samtyckt till de behörigheter som anges i frågeparametern och returnerar en auktoriseringskod för att byta mot en åtkomsttoken.

### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar, `redirect_uri` `response_mode=form_post`som skickas till meding, ser ut som:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| id_token |Den `id_token` som appen begärde. Du kan `id_token` använda för att verifiera användarens identitet och påbörja en session med användaren. |
| kod |Det authorization_code som appen begärde. Appen kan använda auktoriseringskoden för att begära en åtkomsttoken för målresursen. Authorization_codes är kortlivade och löper vanligtvis ut efter ca 10 minuter. |
| state |Om en tillståndsparameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrollera att tillståndsvärdena i begäran och svaret är identiska. |

### <a name="error-response"></a>Felsvar

Felsvar kan också skickas `redirect_uri` till så att appen kan hantera dem på rätt sätt:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträng som kan användas för att klassificera typer av fel som uppstår och som kan användas för att reagera på fel. |
| error_description |Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera orsaken till ett autentiseringsfel. |

En beskrivning av möjliga felkoder och rekommenderade klientåtgärder finns i [Felkoder för auktoriseringsslutpunktsfel](#error-codes-for-authorization-endpoint-errors).

När du har fått `code` en `id_token`auktorisering och en kan du logga in användaren och få [åtkomsttoken](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för deras räkning. Om du vill logga in `id_token` användaren måste du validera exakt som beskrivs ovan. För att få åtkomsttoken kan du följa stegen som beskrivs i avsnittet "Använd auktoriseringskoden för att begära en åtkomsttoken" i vår [OAuth-kodflödesdokumentation](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [åtkomsttoken .](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Läs mer om [ `id_token` anspråk och anspråk](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
