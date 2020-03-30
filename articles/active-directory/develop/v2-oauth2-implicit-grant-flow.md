---
title: OAuth 2.0 implicit bidragsflöde - Microsofts identitetsplattform | Azure
description: Säkra ensidiga appar med microsoft identity platform implicit flöde.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 6e3f021fd888bbb408fa66964c54d22f0d68e84e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297701"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsofts identitetsplattform och implicita bidragsflöde

Med slutpunkten för Microsoft-identitetsplattformen kan du logga in användare i dina ensidiga appar med både personliga konton och arbets- eller skolkonton från Microsoft. Ensidiga och andra JavaScript-appar som körs främst i en webbläsare står inför några intressanta utmaningar när det gäller autentisering:

* Säkerhetsegenskaperna för dessa appar skiljer sig avsevärt från traditionella serverbaserade webbprogram.
* Många auktoriseringsservrar och identitetsleverantörer stöder inte CORS-begäranden.
* Helsideswebbläsare omdirigerar bort från appen blir särskilt invasiva för användarupplevelsen.

För dessa program (AngularJS, Ember.js, React.js och så vidare) stöder Microsofts identitetsplattform OAuth 2.0 Implicit Grant-flödet. Det implicita flödet beskrivs i [OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749#section-4.2). Dess främsta fördel är att det gör att appen kan hämta token från Microsofts identitetsplattform utan att utföra ett serverautentiseringsutbyte för server. På så sätt kan appen logga in användaren, underhålla sessionen och hämta token till andra webb-API:er som finns i klientens JavaScript-kod. Det finns några viktiga säkerhetsaspekter att ta hänsyn till när du använder det implicita flödet specifikt kring [klient-](https://tools.ietf.org/html/rfc6749#section-10.3) och [användarpersonifiering](https://tools.ietf.org/html/rfc6749#section-10.3).

I den här artikeln beskrivs hur du programmerar direkt mot protokollet i ditt program.  När det är möjligt rekommenderar vi att du använder de Microsoft Authentication Libraries (MSAL) som stöds i stället för att [hämta token och anropa skyddade webb-API:er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på [exempelapparna som använder MSAL](sample-v2-code.md).

Om du föredrar att inte använda ett bibliotek i din ensidiga app och skicka protokollmeddelanden själv följer du de allmänna stegen nedan.

> [!NOTE]
> Alla Azure Active Directory-scenarier och -funktioner (Azure Active Directory) stöds inte av slutpunkten för Microsoft-identitetsplattformen. För att ta reda på om du ska använda slutpunkten för Microsoft identity platform läser du om begränsningar av [Microsofts identitetsplattform](active-directory-v2-limitations.md).

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Lämpliga scenarier för OAuth2 implicita bidrag

OAuth2-specifikationen förklarar att det implicita bidraget har utformats för att aktivera användaragentprogram – det vill säga JavaScript-program som körs i en webbläsare. Den definierande egenskapen hos sådana program är att JavaScript-kod används för åtkomst till serverresurser (vanligtvis ett webb-API) och för att uppdatera programanvändarupplevelsen därefter. Tänk på program som Gmail eller Outlook Web Access: När du väljer ett meddelande från inkorgen ändras bara panelen för meddelandevisualisering för att visa det nya valet, medan resten av sidan förblir oförändrad. Den här egenskapen står i kontrast till traditionella omdirigeringsbaserade webbappar, där varje användarinteraktion resulterar i en helsidesreback och en helsidesrendering av det nya serversvaret.

Program som använder javascript-baserad metod för sin extrema kallas ensidiga program eller SPA. Tanken är att dessa program endast ska tjäna en första HTML-sida och tillhörande JavaScript, där alla efterföljande interaktioner drivs av webb-API-anrop som utförs via JavaScript. Men hybrid metoder, där ansökan är mestadels postback-driven men utför enstaka JS-samtal, är inte ovanligt - diskussionen om implicit flödesanvändning är relevant för dem också.

Omdirigera-baserade program säkrar vanligtvis sina förfrågningar via cookies, men den metoden fungerar inte lika bra för JavaScript-program. Cookies fungerar bara mot den domän de har genererats för, medan JavaScript-samtal kan riktas mot andra domäner. Faktum är att det ofta kommer att vara fallet: tänk på program som anropar Microsoft Graph API, Office API, Azure API – alla som finns utanför domänen från där programmet visas. En växande trend för JavaScript-program är att inte ha någon backend alls, förlitar sig 100% på tredje part webb-API: er för att genomföra sin verksamhet funktion.

För närvarande är den metod som föredras för att skydda anrop till ett webb-API att använda metoden OAuth2-innehavartoken, där varje anrop åtföljs av en OAuth2-åtkomsttoken. Webb-API:et undersöker den inkommande åtkomsttoken och, om den hittar de nödvändiga scope, ger den åtkomst till den begärda åtgärden. Det implicita flödet ger en bekväm mekanism för JavaScript-program för att få åtkomsttoken för ett webb-API, vilket ger många fördelar när det gäller cookies:

* Tokens kan erhållas på ett tillförlitligt sätt utan behov av samtal med korsursprung – obligatorisk registrering av den omdirigera URI som token är returgarantier för att token inte förskjuts
* JavaScript-program kan hämta så många åtkomsttoken som de behöver, för så många webb-API:er de riktar sig till – utan begränsning på domäner
* HTML5-funktioner som session eller lokal lagring ger full kontroll över tokencacheing och livstidshantering, medan hantering av cookies är ogenomskinlig för appen
* Åtkomsttoken är inte mottagliga för CSRF-attacker (Cross-site request forgery)

Det implicita beviljandeflödet utfärdar inte uppdateringstoken, främst av säkerhetsskäl. En uppdateringstoken är inte lika snävt begränsad som åtkomsttoken, vilket ger mycket mer kraft och orsakar därmed mycket mer skada om den läcker ut. I det implicita flödet levereras token i URL:en, därav risken för avlyssning är högre än i auktoriseringskodsbidraget.

Ett JavaScript-program har dock en annan mekanism till sitt förfogande för att förnya åtkomsttoken utan att upprepade gånger fråga användaren om autentiseringsuppgifter. Programmet kan använda en dold iframe för att utföra nya tokenbegäranden mot auktoriseringsslutpunkten för Azure AD: så länge webbläsaren fortfarande har en aktiv session (läs: har en sessionscookie) mot Azure AD-domänen kan autentiseringsbegäran utan att användaren behöver interagera.

Den här modellen ger JavaScript-programmet möjlighet att självständigt förnya åtkomsttoken och till och med hämta nya för ett nytt API (förutsatt att användaren tidigare samtyckt till dem). På så sätt undviker du den extra bördan av att förvärva, underhålla och skydda en artefakt med högt värde, till exempel en uppdateringstoken. Artefakten som gör den tysta förnyelsen möjlig, Azure AD-sessionscookien, hanteras utanför programmet. En annan fördel med den här metoden är att en användare kan logga ut från Azure AD, med hjälp av något av de program som är inloggade på Azure AD och som körs på någon av webbläsarflikarna. Detta resulterar i borttagning av Azure AD-sessionscookien och JavaScript-programmet förlorar automatiskt möjligheten att förnya token för den utloggade användaren.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Är det implicita bidraget lämpligt för min app?

Det implicita bidraget innebär fler risker än andra bidrag, och de områden du måste uppmärksamma är väl dokumenterade (till exempel [missbruk av åtkomsttoken för att personifiera resursägare i Implicit flöde][OAuth2-Spec-Implicit-Misuse] och [OAuth 2.0 Hot model and Security Considerations][OAuth2-Threat-Model-And-Security-Implications]). Den högre riskprofilen beror dock till stor del på att den är avsedd att aktivera program som kör aktiv kod, som betjänas av en fjärrresurs till en webbläsare. Om du planerar en SPA-arkitektur, inte har några serverdelskomponenter eller avser att anropa ett webb-API via JavaScript, rekommenderas användning av det implicita flödet för tokeninhämtning.

Om ditt program är en inbyggd klient passar det implicita flödet inte bra. Frånvaron av Azure AD-sessionscookien i samband med en inbyggd klient berövar ditt program från hur du upprätthåller en långlivad session. Vilket innebär att ditt program upprepade gånger kommer att uppmana användaren när du får åtkomsttoken för nya resurser.

Om du utvecklar ett webbprogram som innehåller en serverdel och förbrukar ett API från dess serverdelskod, passar inte heller det implicita flödet. Andra bidrag ger dig mycket mer makt. Beviljandet av OAuth2-klientautentiseringsuppgifter ger till exempel möjlighet att hämta token som återspeglar de behörigheter som tilldelats själva programmet, i motsats till användardelegering. Detta innebär att klienten har möjlighet att upprätthålla programmatisk åtkomst till resurser även när en användare inte är aktivt engagerad i en session, och så vidare. Inte bara det, men sådana bidrag ger högre säkerhetsgarantier. Åtkomsttokens överförs till exempel aldrig via användarbläddraren, de riskerar inte att sparas i webbläsarhistoriken och så vidare. Klientprogrammet kan också utföra stark autentisering när du begär en token.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>Protokolldiagram

Följande diagram visar hur hela implicita inloggningsflödet ser ut och de avsnitt som följer beskriver varje steg mer detaljerat.

![Diagram som visar det implicita inloggningsflödet](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Skicka inloggningsbegäran

Om du först vill signera användaren i din app kan du `id_token` skicka en [OpenID Connect-autentiseringsbegäran](v2-protocols-oidc.md) och få en från slutpunkten för Microsoft-identitetsplattformen.

> [!IMPORTANT]
> Om du vill begära en ID-token och/eller en åtkomsttoken måste appregistreringen på sidan [Azure-portal - Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) ha motsvarande implicita bidragsflöde aktiverat genom att välja **ID-token** och.eller **komma åt token** under avsnittet Implicit **beviljande.** Om det inte är aktiverat `unsupported_response` returneras ett fel: **Det angivna värdet för indataparametern "response_type" är inte tillåtet för den här klienten. Förväntat värde är "kod"**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Om du vill testa inloggningen <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize..</a> med det implicita flödet klickar du på . När du har loggat in `https://localhost/myapp/` ska `id_token` din webbläsare omdirigeras till med en i adressfältet.
>

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `tenant` | krävs |Värdet `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna `common` `organizations`värdena är , , `consumers`och klientidentifierare. Mer information finns i [grundläggande protokoll](active-directory-v2-protocols.md#endpoints). |
| `client_id` | krävs | Program-ID (klient) som [sidan Azure Portal - Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) har tilldelats din app. |
| `response_type` | krävs |Måste `id_token` inkludera för OpenID Connect-inloggning. Det kan också omfatta response_type `token`. Om `token` du använder den här kan appen få en åtkomsttoken omedelbart från den auktoriserade slutpunkten utan att behöva göra en andra begäran till den auktoriserade slutpunkten. Om du `token` använder response_type måste `scope` parametern innehålla ett scope som anger vilken resurs som ska utfärda token för (till exempel user.read på Microsoft Graph).  |
| `redirect_uri` | Rekommenderas |Den redirect_uri i din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av de redirect_uris du registrerat i portalen, förutom att den måste url-kodas. |
| `scope` | krävs |En utrymmesavskiljande lista över [scope](v2-permissions-and-consent.md). För OpenID Connect (id_tokens) måste det `openid`innehålla scopet , som översätts till behörigheten "Logga in dig" i behörigheten medgivande. Du kan också inkludera möjligheterna `email` `profile` och omfången för att få åtkomst till ytterligare användardata. Du kan också inkludera andra scope i den här begäran om att begära samtycke till olika resurser, om en åtkomsttoken begärs. |
| `response_mode` | valfri |Anger den metod som ska användas för att skicka tillbaka den resulterande token till din app. Standard för att fråga efter bara en åtkomsttoken, men fragmentera om begäran innehåller en id_token. |
| `state` | Rekommenderas |Ett värde som ingår i begäran som också returneras i tokensvaret. Det kan vara en sträng av allt innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligtvis för [att förhindra förfalskningsattacker mellan webbplatser](https://tools.ietf.org/html/rfc6749#section-10.12). Tillståndet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på. |
| `nonce` | krävs |Ett värde som ingår i begäran, som genereras av appen, som kommer att inkluderas i den resulterande id_token som ett anspråk. Appen kan sedan verifiera det här värdet för att minska token reprisattacker. Värdet är vanligtvis en randomiserad, unik sträng som kan användas för att identifiera begärans ursprung. Krävs endast när en id_token begärs. |
| `prompt` | valfri |Anger vilken typ av användarinteraktion som krävs. De enda giltiga värdena just nu är "login", "none", "select_account" och "samtycke". `prompt=login`kommer att tvinga användaren att ange sina autentiseringsuppgifter på den begäran, förneka single-sign on. `prompt=none`är motsatsen - det kommer att se till att användaren inte presenteras med någon interaktiv uppmaning som helst. Om begäran inte kan slutföras tyst via en inloggning returnerar slutpunkten för Microsoft-identitetsplattformen ett fel. `prompt=select_account`skickar användaren till en kontoväljare där alla konton som kommer ihåg i sessionen visas. `prompt=consent`utlöser dialogrutan OAuth-medgivande när användaren loggar in och ber användaren att bevilja behörigheter till appen. |
| `login_hint`  |valfri |Kan användas för att förfylla fältet användarnamn/e-postadress på inloggningssidan för användaren, om du känner till deras användarnamn i förväg. Ofta använder appar den här parametern under omautentisering, efter att redan `preferred_username` ha extraherat användarnamnet från en tidigare inloggning med anspråket.|
| `domain_hint` | valfri |Om det inkluderas hoppar den över den e-postbaserade identifieringsprocessen som användaren går igenom på inloggningssidan, vilket leder till en något mer strömlinjeformad användarupplevelse. Detta används ofta för Line of Business-appar som fungerar i en enda klient, där de tillhandahåller ett domännamn i en viss klientorganisation.  Detta vidarebefordrar användaren till federationsleverantören för den klienten.  Observera att detta kommer att hindra gäster från att logga in på denna ansökan.  |

Nu uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen. Slutpunkten för Microsoft-identitetsplattformen säkerställer också att användaren har `scope` samtyckt till de behörigheter som anges i frågeparametern. Om användaren har samtyckt till **någon** av dessa behörigheter kommer den att be användaren att samtycka till de behörigheter som krävs. Mer information finns i [behörigheter, medgivande och appar för flera innehavare](v2-permissions-and-consent.md).

När användaren har autentiserat och gett sitt samtycke returnerar slutpunkten för Microsoft `redirect_uri`identity platform ett svar `response_mode` till din app med den angivna metoden med den metod som anges i parametern.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat `response_mode=fragment` `response_type=id_token+token` svar med och ser ut som följande (med radbrytningar för läsbarhet):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| `access_token` |Inkluderat `response_type` `token`om inkluderar . Åtkomsttoken som appen begärde. Åtkomsttoken ska inte avkodas eller på annat sätt inspekteras, den ska behandlas som en ogenomskinlig sträng. |
| `token_type` |Inkluderat `response_type` `token`om inkluderar . Kommer alltid `Bearer`att vara . |
| `expires_in`|Inkluderat `response_type` `token`om inkluderar . Anger hur många sekunder token är giltig för cachelagring. |
| `scope` |Inkluderat `response_type` `token`om inkluderar . Anger de scope som access_token ska vara giltiga för. Kanske inte alla scope som begärs, om de inte var tillämpliga på användaren (när azure AD-scope begärs när ett personligt konto används för att logga in). |
| `id_token` | En signerad JSON Web Token (JWT). Appen kan avkoda segmenten för den här token för att begära information om användaren som loggade in. Appen kan cachelagra värdena och visa dem, men den bör inte förlita sig på dem för några auktoriserings- eller säkerhetsgränser. Mer information om id_tokens finns i [`id_token reference`](id-tokens.md). <br> **Anm.:** Endast om `openid` omfattning begärdes. |
| `state` |Om en tillståndsparameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrollera att tillståndsvärdena i begäran och svaret är identiska. |

#### <a name="error-response"></a>Felsvar

Felsvar kan också skickas `redirect_uri` till så att appen kan hantera dem på rätt sätt:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| `error` |En felkodsträng som kan användas för att klassificera typer av fel som uppstår och som kan användas för att reagera på fel. |
| `error_description` |Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera orsaken till ett autentiseringsfel. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Hämta åtkomsttoken tyst i bakgrunden

Nu när du har signerat användaren i din ensidiga app kan du tyst få åtkomsttoken för att anropa webb-API:er som skyddas av Microsofts identitetsplattform, till exempel [Microsoft Graph](https://developer.microsoft.com/graph). Även om du redan har `token` fått en token med hjälp av response_type kan du använda den här metoden för att hämta token till ytterligare resurser utan att behöva omdirigera användaren för att logga in igen.

I det normala OpenID Connect/OAuth-flödet gör du detta genom `/token` att göra en begäran till slutpunkten för Microsoft-identitetsplattformen. Slutpunkten för Microsoft-identitetsplattform stöder dock inte CORS-begäranden, så det är uteslutet att göra AJAX-anrop för att hämta och uppdatera token. I stället kan du använda det implicita flödet i en dold iframe för att hämta nya token för andra webb-API:er: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Mer information om frågeparametrarna i URL:en finns [i skicka inloggningsbegäran](#send-the-sign-in-request).

> [!TIP]
> Prova att kopiera & klistra in nedanstående begäran i en webbläsarflik! (Glöm inte att ersätta `login_hint` värdena med rätt värde för din användare)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Tack vare `prompt=none` parametern kommer den här begäran antingen att lyckas eller misslyckas omedelbart och återgå till ditt program. Ett lyckat `redirect_uri`svar skickas till din app med den angivna `response_mode` , med den metod som anges i parametern.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat `response_mode=fragment` svar med ser ut som:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parameter | Beskrivning |
| --- | --- |
| `access_token` |Inkluderat `response_type` `token`om inkluderar . Åtkomsttoken som appen begärde, i det här fallet för Microsoft Graph. Åtkomsttoken ska inte avkodas eller på annat sätt inspekteras, den ska behandlas som en ogenomskinlig sträng. |
| `token_type` | Kommer alltid `Bearer`att vara . |
| `expires_in` | Anger hur många sekunder token är giltig för cachelagring. |
| `scope` | Anger de scope som access_token ska vara giltiga för. Kanske inte alla scope som begärs, om de inte var tillämpliga på användaren (när azure AD-scope begärs när ett personligt konto används för att logga in). |
| `id_token` | En signerad JSON Web Token (JWT). Inkluderat `response_type` `id_token`om inkluderar . Appen kan avkoda segmenten för den här token för att begära information om användaren som loggade in. Appen kan cachelagra värdena och visa dem, men den bör inte förlita sig på dem för några auktoriserings- eller säkerhetsgränser. Mer information om id_tokens finns i [ `id_token` referensen](id-tokens.md). <br> **Anm.:** Endast om `openid` omfattning begärdes. |
| `state` |Om en tillståndsparameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrollera att tillståndsvärdena i begäran och svaret är identiska. |

#### <a name="error-response"></a>Felsvar

Felsvar kan också skickas `redirect_uri` till så att appen kan hantera dem på rätt sätt. När det `prompt=none`gäller , ett förväntat fel kommer att vara:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beskrivning |
| --- | --- |
| `error` |En felkodsträng som kan användas för att klassificera typer av fel som uppstår och som kan användas för att reagera på fel. |
| `error_description` |Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera orsaken till ett autentiseringsfel. |

Om du får det här felet i iframe-begäran måste användaren logga in interaktivt igen för att hämta en ny token. Du kan välja att hantera detta fall på något sätt vettigt för din ansökan.

## <a name="refreshing-tokens"></a>Uppdatera token

Det implicita bidraget ger inte uppdateringstoken. Både `id_token`s `access_token`och s upphör att gälla efter en kort tidsperiod, så din app måste vara beredd att uppdatera dessa token med jämna mellanrum. Om du vill uppdatera någon typ av token kan du `prompt=none` utföra samma dolda iframe-begäran ovanifrån med parametern för att styra identitetsplattformens beteende. Om du vill få `id_token`en ny, `id_token` se `response_type` `scope=openid`till att använda `nonce` i och , samt en parameter.

## <a name="send-a-sign-out-request"></a>Skicka en ut signeringsbegäran

OpenID Connect `end_session_endpoint` gör att din app kan skicka en begäran till slutpunkten för Microsofts identitetsplattform för att avsluta en användares session och rensa cookies som ställts in av slutpunkten för Microsofts identitetsplattform. Om du vill signera en användare helt ur ett webbprogram bör appen avsluta sin egen session med användaren (vanligtvis genom att rensa en tokencache eller släppa cookies) och sedan omdirigera webbläsaren till:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `tenant` |krävs |Värdet `{tenant}` i sökvägen för begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna `common` `organizations`värdena är , , `consumers`och klientidentifierare. Mer information finns i [grundläggande protokoll](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Rekommenderas | URL:en som användaren ska returneras till när utloggningen är klar. Det här värdet måste matcha en av de omdirigerings-URI:er som registrerats för programmet. Om den inte ingår visas ett allmänt meddelande av slutpunkten för Microsofts identitetsplattform. |

## <a name="next-steps"></a>Nästa steg

* Gå igenom [MSAL JS-exemplen](sample-v2-code.md) för att komma igång med kodningen.
