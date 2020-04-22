---
title: OpenID Connect-protokoll – Microsofts identitetsplattform | Azure
description: Skapa webbprogram med hjälp av implementeringen av Microsoft identity platform av OpenID Connect-autentiseringsprotokollet.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ebec4cb6bbbac5b331eb2eb4145716e16e7320fa
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677690"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft identity-plattform och OpenID Connect-protokoll

OpenID Connect är ett autentiseringsprotokoll som bygger på OAuth 2.0 och som du kan använda för att logga in en användare på ett säkert sätt i ett webbprogram. När du använder slutpunkten för Microsoft identity platform-slutpunkten för OpenID Connect kan du lägga till inloggning och API-åtkomst till dina webbaserade appar. Den här artikeln visar hur du gör detta oberoende av språk och beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda microsofts bibliotek med öppen källkod.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) utökar OAuth 2.0-auktoriseringsprotokollet så att det används som ett *authorization* *autentiseringsprotokoll,* så att du kan göra enstaka inloggning med OAuth. OpenID Connect introducerar konceptet med en *ID-token*, vilket är en säkerhetstoken som gör att klienten kan verifiera användarens identitet. ID-token hämtar också grundläggande profilinformation om användaren. Eftersom OpenID Connect utökar OAuth 2.0 kan appar hämta *åtkomsttoken*på ett säkert sätt , som kan användas för att komma åt resurser som skyddas av en [auktoriseringsserver](active-directory-v2-protocols.md#the-basics). Slutpunkten för Microsoft-identitetsplattformen gör det också möjligt för appar från tredje part som är registrerade med Azure AD att utfärda åtkomsttoken för skyddade resurser som webb-API:er. Mer information om hur du konfigurerar ett program för att utfärda åtkomsttoken finns i [Så här registrerar du en app med slutpunkten för Microsoft identity-plattformen](quickstart-register-app.md). Vi rekommenderar att du använder OpenID Connect om du skapar ett [webbprogram](v2-app-types.md#web-apps) som finns på en server och nås via en webbläsare.

## <a name="protocol-diagram-sign-in"></a>Protokolldiagram: Logga in

Det mest grundläggande inloggningsflödet har stegen som visas i nästa diagram. Varje steg beskrivs i detalj i den här artikeln.

![OpenID Connect-protokollet: Logga in](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Hämta metadatadokumentet OpenID Connect

OpenID Connect beskriver ett metadatadokument som innehåller det mesta av den information som krävs för att en app ska kunna logga in. Detta inkluderar information som webbadresser som ska användas och platsen för tjänstens offentliga signeringsnycklar. För slutpunkten för Microsoft-identitetsplattformen är detta det OpenID Connect-metadatadokument som du bör använda:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Prova! Klicka [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) här `common` om du vill se klientkonfigurationen.

Kan `{tenant}` ta ett av fyra värden:

| Värde | Beskrivning |
| --- | --- |
| `common` |Användare med både ett personligt Microsoft-konto och ett arbets- eller skolkonto från Azure AD kan logga in på programmet. |
| `organizations` |Endast användare med arbets- eller skolkonton från Azure AD kan logga in på programmet. |
| `consumers` |Endast användare med ett personligt Microsoft-konto kan logga in på programmet. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` | Endast användare från en viss Azure AD-klientorganisation (oavsett om de är medlemmar i katalogen med ett arbets- eller skolkonto, eller om de är gäster i katalogen med ett personligt Microsoft-konto) kan logga in på programmet. Antingen kan det egna domännamnet för Azure AD-klienten eller klientens GUID-identifierare användas. Du kan också använda `9188040d-6c67-4c5b-b112-36a304b66dad`konsumenten hyresgästen, `consumers` , i stället för hyresgästen.  |

Metadata är ett enkelt JavaScript Object Notation (JSON) dokument. Se följande kodavsnitt för ett exempel. Innehållet i kodavsnittet beskrivs fullständigt i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

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

Om din app har anpassade signeringsnycklar som ett resultat av `appid` att använda funktionen [för anspråksmappning](active-directory-claims-mapping.md) måste du lägga till en frågeparameter som innehåller app-ID:et för att få en `jwks_uri` pekar på appens information om signeringsnyckeln. Till exempel: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` innehåller `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`en av .

Vanligtvis använder du det här metadatadokumentet för att konfigurera ett OpenID Connect-bibliotek eller SDK. biblioteket skulle använda metadata för att utföra sitt arbete. Om du inte använder ett färdigbyggt OpenID Connect-bibliotek kan du följa stegen i resten av den här artikeln för att logga in i en webbapp med hjälp av slutpunkten för Microsoft-identitetsplattformen.

## <a name="send-the-sign-in-request"></a>Skicka inloggningsbegäran

När webbappen behöver autentisera användaren kan den `/authorize` dirigera användaren till slutpunkten. Denna begäran liknar den första delen av [OAuth 2.0 auktorisering kodflöde](v2-oauth2-auth-code-flow.md), med dessa viktiga distinktioner:

* Begäran måste innehålla `openid` scopet `scope` i parametern.
* Parametern `response_type` måste `id_token`innehålla .
* Begäran måste innehålla `nonce` parametern.

> [!IMPORTANT]
> För att kunna begära en ID-token från /auktoriseringsslutpunkten måste appregistreringen i [registreringsportalen](https://portal.azure.com) ha implicit `oauth2AllowIdTokenImplicitFlow` beviljande av id_tokens `true`aktiverat på fliken Autentisering (som anger flaggan i [programmanifestet](reference-app-manifest.md) till ). Om det inte är aktiverat `unsupported_response` returneras ett fel: "Det angivna värdet för indataparametern response_type" är inte tillåtet för den här klienten. Förväntat värde är "kod""

Ett exempel:

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
> Klicka på följande länk för att köra den här begäran. När du har loggat in omdirigeras din webbläsare till `https://localhost/myapp/`, med en ID-token i adressfältet. Observera att denna `response_mode=fragment` begäran används (endast i demonstrationssyfte). Vi rekommenderar att `response_mode=form_post`du använder .
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Du kan `{tenant}` använda värdet i sökvägen till begäran för att styra vem som kan logga in på programmet. De tillåtna `common` `organizations`värdena är , , `consumers`och klientidentifierare. Mer information finns i [grundläggande protokoll](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Krävs | **Program-ID (klient)** som [Azure-portalen – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) erfarenhet som tilldelats din app. |
| `response_type` | Krävs | Måste `id_token` inkludera för OpenID Connect-inloggning. Det kan också `response_type` innehålla andra `code`värden, till exempel . |
| `redirect_uri` | Rekommenderas | Omdirigerings-URI för din app, där autentiseringssvar kan skickas och tas emot av din app. Den måste exakt matcha en av de omdirigerings-URI:er som du registrerade i portalen, förutom att den måste vara URL-kodad. Om det inte finns väljer slutpunkten en registrerad redirect_uri slumpmässigt att skicka tillbaka användaren till. |
| `scope` | Krävs | En utrymmesavskiljande lista över scope. För OpenID Connect måste den `openid`innehålla scopet , som översätts till behörigheten "Logga in dig" i användargränssnittet för medgivande. Du kan också inkludera andra scope i den här begäran om att begära samtycke. |
| `nonce` | Krävs | Ett värde som ingår i begäran, som genereras av appen, som kommer att inkluderas i det resulterande id_token värde som ett anspråk. Appen kan verifiera det här värdet för att minska attacker för tokenreprisuppspelning. Värdet är vanligtvis en randomiserad, unik sträng som kan användas för att identifiera begärans ursprung. |
| `response_mode` | Rekommenderas | Anger den metod som ska användas för att skicka tillbaka den resulterande auktoriseringskoden till din app. Det kan vara `form_post` eller `fragment`. För webbprogram rekommenderar `response_mode=form_post`vi att du använder för att säkerställa den säkraste överföringen av token till ditt program. |
| `state` | Rekommenderas | Ett värde som ingår i begäran som också kommer att returneras i tokensvaret. Det kan vara en sträng av vilket innehåll du vill. Ett slumpmässigt genererat unikt värde används vanligtvis för att [förhindra förfalskningsattacker mellan webbplatser](https://tools.ietf.org/html/rfc6749#section-10.12). Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller visa användaren var på. |
| `prompt` | Valfri | Anger vilken typ av användarinteraktion som krävs. De enda giltiga värdena `login` `none`just `consent`nu är , och . Anspråket `prompt=login` tvingar användaren att ange sina autentiseringsuppgifter på den begäran, vilket förnekar enkel inloggning. Påståendet `prompt=none` är det motsatta. Detta påstående säkerställer att användaren inte presenteras med någon interaktiv fråga på. Om begäran inte kan slutföras tyst via enkel inloggning returnerar slutpunkten för Microsoft-identitetsplattformen ett fel. Anspråket `prompt=consent` utlöser dialogrutan OAuth-medgivande när användaren loggar in. I dialogrutan uppmanas användaren att bevilja behörigheter till appen. |
| `login_hint` | Valfri | Du kan använda den här parametern för att fylla i användarnamnet och e-postadressfältet på inloggningssidan för användaren, om du känner till användarnamnet i förväg. Ofta använder appar den här parametern under omautentisering, efter att redan ha extraherat `preferred_username` användarnamnet från en tidigare inloggning med hjälp av anspråket. |
| `domain_hint` | Valfri | Sfären av användaren i en federerad katalog.  Detta hoppar över den e-postbaserade identifieringsprocessen som användaren går igenom på inloggningssidan, för en något mer strömlinjeformad användarupplevelse. För klienter som federeras via en lokal katalog som AD FS resulterar detta ofta i en sömlös inloggning på grund av den befintliga inloggningssessionen. |

Nu uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen. Slutpunkten för Microsoft-identitetsplattform verifierar att användaren har samtyckt till de behörigheter som anges i `scope` frågeparametern. Om användaren inte har samtyckt till någon av dessa behörigheter uppmanas användaren att godkänna de behörigheter som krävs. Du kan läsa mer om [behörigheter, medgivande och appar för flera innehavare](v2-permissions-and-consent.md).

När användaren har autentiserat och gett sitt samtycke returnerar slutpunkten för Microsoft identity platform ett svar till `response_mode` din app på den angivna omdirigerings-URI:n med den metod som anges i parametern.

### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar `response_mode=form_post` när du använder ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| `id_token` | ID-token som appen begärde. Du kan `id_token` använda parametern för att verifiera användarens identitet och påbörja en session med användaren. Mer information om ID-token och deras innehåll finns i [ `id_tokens` referensen](id-tokens.md). |
| `state` | Om `state` en parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrollera att tillståndsvärdena i begäran och svaret är identiska. |

### <a name="error-response"></a>Felsvar

Felsvar kan också skickas till omdirigera URI så att appen kan hantera dem. Ett felmeddelande ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En felkodsträng som du kan använda för att klassificera typer av fel som uppstår och för att reagera på fel. |
| `error_description` | Ett specifikt felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för auktoriseringsslutpunktsfel

I följande tabell beskrivs felkoder som `error` kan returneras i parametern för felsvaret:

| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| `invalid_request` | Protokollfel, till exempel en parameter som saknas, krävs. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel som vanligtvis fångas under inledande testning. |
| `unauthorized_client` | Klientprogrammet kan inte begära en auktoriseringskod. |Detta inträffar vanligtvis när klientprogrammet inte är registrerat i Azure AD eller inte läggs till användarens Azure AD-klientorganisation. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD. |
| `access_denied` | Resursägaren nekade samtycke. |Klientprogrammet kan meddela användaren att det inte kan fortsätta om inte användaren samtycker. |
| `unsupported_response_type` |Auktoriseringsservern stöder inte svarstypen i begäran. |Åtgärda och skicka begäran igen. Detta är ett utvecklingsfel som vanligtvis fångas under inledande testning. |
| `server_error` | Servern påträffade ett oväntat fel. |Försök igen. Dessa fel kan bero på tillfälliga villkor. Klientprogrammet kan förklara för användaren att svaret är försenat på grund av ett tillfälligt fel. |
| `temporarily_unavailable` | Servern är tillfälligt för upptagen för att hantera begäran. |Försök igen. Klientprogrammet kan förklara för användaren att svaret är försenat på grund av ett tillfälligt villkor. |
| `invalid_resource` | Målresursen är ogiltig eftersom den inte finns, Azure AD inte kan hitta den eller så är den inte korrekt konfigurerad. |Detta indikerar att resursen, om den finns, inte har konfigurerats i klienten. Programmet kan fråga användaren med instruktioner för att installera programmet och lägga till det i Azure AD. |

## <a name="validate-the-id-token"></a>Verifiera ID-token

Att bara ta emot en id_token räcker inte för att autentisera användaren. Du måste validera id_token-signaturen och verifiera anspråken i token enligt appens krav. Slutpunkten för Microsoft identity-plattformen använder [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentliga nycklar för att signera token och verifiera att de är giltiga.

Du kan välja `id_token` att validera klientkoden i klientkoden, `id_token` men en vanlig metod är att skicka till en server för serveringsstyrning och göra valideringen där. När du har validerat signaturen för id_token, finns det några anspråk som du måste verifiera. Se [ `id_token` referensen](id-tokens.md) för mer information, inklusive [validera token och](id-tokens.md#validating-an-id_token) viktig information om [signering av nyckelfördrollning](active-directory-signing-key-rollover.md). Vi rekommenderar att du använder ett bibliotek för att tolka och validera tokens - det finns minst ett tillgängligt för de flesta språk och plattformar.

Du kanske också vill validera ytterligare anspråk beroende på ditt scenario. Några vanliga valideringar är:

* Se till att användaren/organisationen har registrerat sig för appen.
* Se till att användaren har rätt behörighet/behörighet
* Se till att en viss styrka av autentisering har inträffat, till exempel multifaktorautentisering.

När du har validerat id_token kan du påbörja en session med användaren och använda anspråken i id_token för att få information om användaren i appen. Denna information kan användas för visning, poster, personalisering, etc.

## <a name="send-a-sign-out-request"></a>Skicka en ut signeringsbegäran

När du vill logga ut användaren från din app räcker det inte att rensa appens cookies eller på annat sätt avsluta användarens session. Du måste också omdirigera användaren till slutpunkten för Microsoft-identitetsplattformen för att kunna logga ut. Om du inte gör det räkas användaren till din app igen utan att ange sina autentiseringsuppgifter igen, eftersom de har en giltig enstaka inloggningssession med slutpunkten för Microsoft-identitetsplattformen.

Du kan omdirigera användaren `end_session_endpoint` till listan i OpenID Connect-metadatadokumentet:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | Villkor | Beskrivning |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Rekommenderas | URL:en som användaren omdirigeras till efter att ha loggat ut. Om parametern inte ingår visas ett allmänt meddelande som genereras av slutpunkten för Microsoft-identitetsplattformen. Den här URL:en måste matcha en av de omdirigerings-URI:er som är registrerade för ditt program i appregistreringsportalen. |

## <a name="single-sign-out"></a>Enkel utloggning

När du omdirigerar `end_session_endpoint`användaren till microsoft-identitetsplattformens slutpunkt rensar användarens session från webbläsaren. Användaren kan dock fortfarande vara inloggad på andra program som använder Microsoft-konton för autentisering. Om du vill att dessa program ska kunna signera användaren samtidigt skickar slutpunkten för Microsoft-identitetsplattformen en HTTP GET-begäran till registrerade `LogoutUrl` av alla program som användaren för närvarande är inloggad på. Program måste svara på den här begäran genom att rensa `200` alla sessioner som identifierar användaren och returnera ett svar. Om du vill stödja enkel inloggning i din ansökan `LogoutUrl` måste du implementera en sådan i programmets kod. Du kan `LogoutUrl` ställa in från appregistreringsportalen.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokolldiagram: Anskaffning av accesstoken

Många webbappar måste inte bara logga in användaren, utan också komma åt en webbtjänst för användarens räkning med hjälp av OAuth. Det här scenariot kombinerar OpenID Connect för användarautentisering samtidigt som du får en auktoriseringskod som du kan använda för att hämta åtkomsttoken om du använder OAuth-auktoriseringskodflödet.

Det fullständiga OpenID Connect-inloggnings- och tokeninhämtningsflödet ser ut ungefär som nästa diagram. Vi beskriver varje steg i detalj i nästa avsnitt i artikeln.

![OpenID Connect-protokoll: Token acquisition](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Hämta åtkomsttoken
Om du vill hämta åtkomsttoken ändrar du inloggningsbegäran:

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
> Klicka på följande länk för att köra den här begäran. När du har loggat in `https://localhost/myapp/`omdirigeras din webbläsare till , med en ID-token och en kod i adressfältet. Observera att den `response_mode=fragment` här begäran endast används i demonstrationssyfte. Vi rekommenderar att `response_mode=form_post`du använder .
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Genom att inkludera behörighetsscope i `response_type=id_token code`begäran och med hjälp av kontrollerar slutpunkten för Microsoft-identitetsplattformen att användaren har samtyckt till de behörigheter som anges i `scope` frågeparametern. Den returnerar en auktoriseringskod till din app för att byta mot en åtkomsttoken.

### <a name="successful-response"></a>Lyckat svar

Ett lyckat `response_mode=form_post` svar från att använda ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| `id_token` | ID-token som appen begärde. Du kan använda ID-token för att verifiera användarens identitet och påbörja en session med användaren. Du hittar mer information om ID-token och deras innehåll i [ `id_tokens` referensen](id-tokens.md). |
| `code` | Auktoriseringskoden som appen begärde. Appen kan använda auktoriseringskoden för att begära en åtkomsttoken för målresursen. En auktoriseringskod är kortlivad. Vanligtvis upphör en auktoriseringskod att gälla om cirka 10 minuter. |
| `state` | Om en tillståndsparameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrollera att tillståndsvärdena i begäran och svaret är identiska. |

### <a name="error-response"></a>Felsvar

Felsvar kan också skickas till omdirigera URI så att appen kan hantera dem på rätt sätt. Ett felmeddelande ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En felkodsträng som du kan använda för att klassificera typer av fel som uppstår och för att reagera på fel. |
| `error_description` | Ett specifikt felmeddelande som kan hjälpa dig att identifiera orsaken till ett autentiseringsfel. |

En beskrivning av möjliga felkoder och rekommenderade klientsvar finns i [Felkoder för auktoriseringsslutpunktsfel](#error-codes-for-authorization-endpoint-errors).

När du har en auktoriseringskod och en ID-token kan du logga in användaren och få åtkomsttoken för deras räkning. Om du vill logga in användaren måste du validera ID-token [exakt enligt beskrivningen](id-tokens.md#validating-an-id_token). Följ stegen som beskrivs i [OAuth-kodflödesdokumentationen](v2-oauth2-auth-code-flow.md#request-an-access-token)för att hämta åtkomsttoken.
