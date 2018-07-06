---
title: Azure Active Directory v2.0- och OpenID Connect-protokollet | Microsoft Docs
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
ms.openlocfilehash: 747ba9c51181c62b45bb060810391ca54f4c044e
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869110"
---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>Azure Active Directory v2.0 och OpenID Connect-protokoll
OpenID Connect är ett autentiseringsprotokoll som bygger på OAuth 2.0 som du kan använda för att på ett säkert sätt logga in en användare till ett webbprogram. När du använder v2.0-slutpunkten implementeringen av OpenID Connect kan du lägga till in- och API-åtkomst till dina webbaserade appar. I den här artikeln har visar vi hur du gör den här oberoende av språk. Vi beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda alla Microsoft open source-bibliotek.

> [!NOTE]
> V2.0-slutpunkten har inte stöd för alla Azure Active Directory-scenarier och funktioner. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) utökar OAuth 2.0 *auktorisering* protokoll som ska användas som en *autentisering* protokoll, så att du kan utföra enkel inloggning med OAuth. OpenID Connect introducerar konceptet för en *ID-token*, vilket är en säkerhetstoken som gör att klienten att verifiera användarens identitet. ID-token får du också grundläggande profilinformation om användaren. Eftersom OpenID Connect utökar OAuth 2.0, appar på ett säkert sätt kan hämta *åtkomsttoken*, som kan användas för att komma åt resurser som skyddas av en [auktoriseringsservern](active-directory-v2-protocols.md#the-basics). V2.0-slutpunkten kan också appar från tredje part som är registrerade i Azure AD för att utfärda åtkomsttoken för skyddade resurser, till exempel webb-API: er. Mer information om hur du konfigurerar ett program att utfärda åtkomsttoken finns i [hur du registrerar en app med v2.0-slutpunkten](active-directory-v2-app-registration.md). Vi rekommenderar att du använder att OpenID Connect om du skapar en [webbprogram](active-directory-v2-flows.md#web-apps) som är finns på en server och kan nås via en webbläsare.

## <a name="protocol-diagram-sign-in"></a>Diagram över protokollet: inloggning
Mest grundläggande inloggning flödet har de steg som visas i nästa diagram. Vi beskriver varje steg i detalj i den här artikeln.

![OpenID Connect-protokollet: inloggning](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Hämta metadatadokument OpenID Connect
OpenID Connect beskriver ett metadatadokument som innehåller de flesta av information som krävs för en app för att utföra logga in. Detta omfattar information, till exempel URL: er att använda och platsen för tjänstens offentliga Signeringsnycklar. Detta är metadatadokument OpenID Connect, bör du använda för v2.0-slutpunkten:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP] 
> Prova! Klicka på [ https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration ](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) att se den `common` konfiguration för klienter. 
>

Den `{tenant}` kan ha något av fyra värden:

| Värde | Beskrivning |
| --- | --- |
| `common` |Användare med både ett personligt microsoftkonto och ett arbets- eller skolkonto konto från Azure Active Directory (Azure AD) kan logga in till programmet. |
| `organizations` |Endast användare med arbets- eller skolkonton från Azure AD kan logga in till programmet. |
| `consumers` |Endast användare med ett personligt microsoftkonto kan logga in till programmet. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` |Endast användare med ett arbets- eller skolkonto konto från en viss Azure AD klient kan logga in till programmet. Du kan använda antingen det egna domännamnet i Azure AD-klient eller klientens GUID-identifierare. |

Metadata är ett vanligt JavaScript Object Notation (JSON)-dokument. Se följande kodavsnitt för ett exempel. Innehållet i kodfragment beskrivs ingående i den [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

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

Normalt använder du Metadatadokumentet för att konfigurera en OpenID Connect-biblioteket eller SDK; biblioteket använder metadata för att utföra sitt arbete. Om du inte använder ett före build OpenID Connect-bibliotek, kan du följa stegen i resten av den här artikeln om du vill utföra logga in i en webbapp med hjälp av v2.0-slutpunkten.

## <a name="send-the-sign-in-request"></a>Skicka begäran inloggning
När webbappen behöver autentisera användaren, den kan dirigera användare till den `/authorize` slutpunkt. Den här begäran liknar den första delen i den [OAuth 2.0-auktoriseringskodflödet](active-directory-v2-protocols-oauth-code.md), med dessa viktiga skillnader:

* Begäran måste innehålla den `openid` omfång i den `scope` parametern.
* Den `response_type` parameter måste innehålla `id_token`.
* Begäran måste innehålla den `nonce` parametern.

> [!IMPORTANT]
> Begär appregistreringen i en ID-token ska har den [registreringsportalen](https://apps.dev.microsoft.com) måste ha den **[Implicit beviljande](active-directory-v2-protocols-implicit.md)** aktiverad för webbklienten. Om den inte är aktiverad, en `unsupported_response` -felmeddelande: ”det angivna värdet för Indataparametern 'response_type” tillåts inte för den här klienten. Förväntat värde är ”code” ”

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
> Klicka på länken nedan för att köra den här begäran. När du har loggat in webbläsaren kommer att omdirigeras till https://localhost/myapp/, med en ID-token i adressfältet. Observera att denna begäran använder `response_mode=fragment` (endast i demonstrationssyfte). Vi rekommenderar att du använder `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| klient |Krävs |Du kan använda den `{tenant}` värdet i sökvägen för begäran om att kontrollera vem som kan logga in till programmet. Tillåtna värden är `common`, `organizations`, `consumers`, och klient-ID: n. Mer information finns i [protokollet grunderna](active-directory-v2-protocols.md#endpoints). |
| client_id |Krävs |Programmet med ID som den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tilldelats din app. |
| response_type |Krävs |Måste innehålla `id_token` för OpenID Connect-inloggning. Det kan även innehålla andra `response_types` värden, till exempel `code`. |
| redirect_uri |Rekommenderas |Omdirigerings-URI för din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av omdirigerings-URI: er som du registrerade i portalen, förutom att det måste vara URL-kodas. |
| omfång |Krävs |En blankstegsavgränsad lista med omfattningar. Det måste innehålla omfånget för OpenID Connect, `openid`, vilket innebär att behörigheten ”logga du in” i godkännande-UI. Du kan även innehålla andra scope i den här begäran för att begära godkännande. |
| nonce |Krävs |Ett värde som ingår i den begäran som skapats av appen, som ska tas med i det resulterande id_token-värdet som ett anspråk. Appen kan kontrollera det här värdet om du vill lösa token repetitionsattacker. Värdet är vanligtvis en slumpmässig, unik sträng som kan användas för att fastställa ursprunget för begäran. |
| response_mode |Rekommenderas |Anger den metod som ska användas för att skicka resulterande Auktoriseringskoden tillbaka till din app. Det kan vara `form_post` eller `fragment`. För webbprogram, bör du använda `response_mode=form_post`, för att kontrollera den säkraste överföringen av token för ditt program. |
| state |Rekommenderas |Ett värde som ingår i den begäran som också kommer att returneras i token-svaret. Det kan vara en sträng med innehåll. Ett slumpmässigt genererat unikt värde används normalt till [förhindra attacker med förfalskning av begäran](http://tools.ietf.org/html/rfc6749#section-10.12). Tillståndet också används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffat, till exempel sidan eller vyn som användaren har på. |
| fråga |Valfri |Anger vilken typ av interaktion från användaren som krävs. De enda giltiga värdena just nu är `login`, `none`, och `consent`. Den `prompt=login` anspråk Tvingar användaren att ange sina autentiseringsuppgifter i begäran, vilket eliminerar enkel inloggning. Den `prompt=none` anspråk är motsatsen. Det här kravet garanterar att användaren inte visas med den interaktiva prompten alls. Om begäran inte kan slutföras tyst via enkel inloggning, returneras ett fel i v2.0-slutpunkten. Den `prompt=consent` anspråk utlöser OAuth godkännande i dialogrutan när användaren loggar in. Dialogrutan ombeds användaren att bevilja behörigheter till appen. |
| login_hint |Valfri |Du kan använda den här parametern förifylld i fälten användarnamn och e-post adressfältet i inloggningssidan för användaren, om du känner till användarnamnet förbereds i förväg. Ofta appar att använda den här parametern under återautentiseringen när du har redan extraherar användarnamnet från en tidigare logga in med hjälp av den `preferred_username` anspråk. |
| domain_hint |Valfri |Det här värdet kan vara `consumers` eller `organizations`. Om inkluderat, hoppar den över e-postbaserad identifieringsprocessen som användaren som passerar på v2.0 logga in sidan för en något mer effektiv användarupplevelse. Ofta appar att använda den här parametern under återautentiseringen genom att extrahera den `tid` anspråk från ID-token. Om den `tid` anspråk värdet är `9188040d-6c67-4c5b-b112-36a304b66dad` (Account konsument klienten), Använd `domain_hint=consumers`. Annars kan du använda `domain_hint=organizations`. |

Nu uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen. V2.0-slutpunkten kontrollerar att användaren har samtyckt till behörigheterna som anges i den `scope` frågeparameter. Om användaren inte har godkänt att någon av dessa behörigheter, uppmanas användaren att godkänna behörigheterna som krävs i v2.0-slutpunkten. Du kan läsa mer om [behörigheter och samtycke fleranvändarappar](active-directory-v2-scopes.md).

När användaren autentiserar och ger medgivande v2.0-slutpunkten returnerar ett svar till din app på den angivna omdirigerings-URI genom att använda den metod som beskrivs i den `response_mode` parametern.

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
| id_token |ID-token som appen har begärt. Du kan använda den `id_token` parametern för att verifiera användarens identitet och starta en session med användaren. Mer information om ID-token och deras innehåll finns i den [v2.0-slutpunkten tokens referens](active-directory-v2-tokens.md). |
| state |Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i begäran och svar är identiska. |

### <a name="error-response"></a>Felsvar
Felsvar kan även skickas till omdirigeringen-URI så att appen kan hanteras. Ett felsvar ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträngen som du kan använda för att klassificera typer av fel som inträffar och reagera på fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett autentiseringsfel. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för slutpunkt-auktoriseringsfel
I följande tabell beskrivs felkoder som kan returneras i de `error` -parametern för felsvaret:

| Felkod | Beskrivning | Klientåtgärd |
| --- | --- | --- |
| invalid_request |Protokollfel, till exempel en saknad obligatoriska parametern. |Åtgärda och skicka begäran igen. Det här är en utvecklingsfel som normalt påträffades under första testningen. |
| unauthorized_client |Klientprogrammet kan inte begära en auktoriseringskod. |Detta inträffar vanligtvis när klientprogrammet inte har registrerats i Azure AD eller har inte lagts till användarens Azure AD-klient. Programmet kan uppmana användaren med instruktioner för att installera programmet och lägga till den till Azure AD. |
| ACCESS_DENIED |Resursägaren nekas godkännande. |Klientprogrammet kan meddela användaren om att det går inte att fortsätta om inte användaren godkänner. |
| unsupported_response_type |Auktoriseringsservern stöder inte svarstypen i begäran. |Åtgärda och skicka begäran igen. Det här är en utvecklingsfel som normalt påträffades under första testningen. |
| server_error |Ett oväntat fel inträffade på servern. |Gör om begäran. Dessa fel kan bero på tillfälliga förhållanden. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt fel. |
| temporarily_unavailable |Servern är tillfälligt för upptagen för att hantera begäran. |Gör om begäran. Klientprogrammet kan förklara för användaren att svaret är försenad på grund av ett tillfälligt tillstånd. |
| invalid_resource |Målresursen är ogiltig eftersom den inte finns, Azure AD kan inte hitta den eller det inte är korrekt konfigurerad. |Detta anger att resursen, om den finns, inte har konfigurerats i klienten. Programmet kan uppmana användaren med instruktioner för att installera programmet och lägga till den till Azure AD. |

## <a name="validate-the-id-token"></a>Verifiera ID-token
Ta emot ett ID-token är inte tillräckliga för att autentisera användaren. Du måste också verifiera signaturen för ID-token och verifiera anspråken i token enligt krav för din app. V2.0-slutpunkten använder [JSON Web token (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentlig nyckel att signera token och kontrollera att de är giltiga.

Du kan välja att verifiera ID-token i klientkoden, men en vanlig metod är att skicka ID-token till en backend-server och utföra valideringen det. När du har verifierat signaturen för ID-token, måste du kontrollera några anspråk. Mer information, inklusive information om [verifiera token](active-directory-v2-tokens.md#validating-tokens) och [viktig information om signeringsnyckel](active-directory-v2-tokens.md#validating-tokens), finns i den [v2.0 tokens referens](active-directory-v2-tokens.md). Vi rekommenderar att du använder ett bibliotek parsa och validera token. Det finns minst en av dessa bibliotek för de flesta språk och plattformar.
<!--TODO: Improve the information on this-->

Du kanske också vill validera ytterligare anspråk, beroende på ditt scenario. Vissa vanliga verifieringar är:

* Se till att användaren eller organisationen har registrerat dig för appen.
* Se till att användaren kräver auktorisering eller behörigheter.
* Se till att en viss styrkan hos autentisering har inträffat, till exempel multifaktorautentisering.

Mer information om anspråk i en ID-token finns i den [v2.0-slutpunkten tokens referens](active-directory-v2-tokens.md).

När du har helt godkänt ID-token, kan du börja en session med användaren. Använda anspråk i ID-token för att hämta information om användaren i din app. Du kan använda den här informationen för visning, poster, tillstånd och så vidare.

## <a name="send-a-sign-out-request"></a>Skicka en förfrågan om utloggning
Om du vill logga ut användaren från din app kan den inte är tillräckliga för att rensa cookies för din app eller på annat sätt avsluta användarens session. Du måste också omdirigera användaren till v2.0-slutpunkten för att logga ut. Om du inte gör detta kommer autentiseras användaren igen till din app utan att behöva ange sina autentiseringsuppgifter igen, eftersom de inte har en giltig inloggning session med v2.0-slutpunkten.

Du kan omdirigera användaren till den `end_session_endpoint` som beskrivs i dokumentet för OpenID Connect metadata:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | Tillstånd | Beskrivning |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Rekommenderas | Den URL som användaren omdirigeras till när du har loggat ut. Om parametern inte är inkluderat visas användaren ett allmänt meddelande som genereras av v2.0-slutpunkten. Den här URL: en måste matcha en av omdirigerings-URI: er som har registrerats för ditt program i portalen för registrering av appen. |

## <a name="single-sign-out"></a>Enkel utloggning
När du omdirigerar användaren till den `end_session_endpoint`, v2.0-slutpunkten tar bort användarens session i webbläsaren. Men kan användaren fortfarande vara inloggad till andra program som använder Microsoft-konton för autentisering. Aktivera programmen för att logga in användaren ut samtidigt, v2.0 slutpunkt skickar en HTTP GET-begäran till det registrerade `LogoutUrl` över alla program som användaren för närvarande är inloggad på. Program måste svara på den här begäran genom att avmarkera alla sessioner som identifierar användaren och returnera en `200` svar. Om du vill stödja enkel inloggning ut i ditt program måste du implementera, till exempel en `LogoutUrl` i programkoden. Du kan ange den `LogoutUrl` från portalen för registrering av appen.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokollet diagram: komma åt tokenförvärv
Många webbprogram måste du inte bara registrera användaren i, men också att få åtkomst till en webbtjänst för användarens räkning genom att använda OAuth. Det här scenariot kombinerar OpenID Connect för autentisering av användare vid hämtning av en auktoriseringskod som du kan använda för att få åtkomst-token om du använder OAuth-auktoriseringskodflöde samtidigt.

Fullständig OpenID Connect-inloggningen och token förvärv flödet ser ut ungefär så i nästa diagram. Vi beskriver varje steg i detalj i nästa avsnitt av artikeln.

![OpenID Connect-protokoll: Token förvärv](../../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>Få åtkomst-token
Ändra inloggning-begäran för att hämta åtkomsttoken:

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
> Klicka på länken nedan för att köra den här begäran. När du har loggat in webbläsaren omdirigeras till https://localhost/myapp/, med ett ID-token och koden i adressfältet. Observera att denna begäran använder `response_mode=fragment` (endast i demonstrationssyfte). Vi rekommenderar att du använder `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

Genom att inkludera behörighetsomfattningar i begäran och med hjälp av `response_type=id_token code`, v2.0-slutpunkten garanterar att användaren har samtyckt till behörigheterna som anges i den `scope` frågeparameter. Returnerar en auktoriseringskod till din app till exchange för en åtkomsttoken.

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
| id_token |ID-token som appen har begärt. Du kan använda ID-token för att verifiera användarens identitet och starta en session med användaren. Du hittar mer information om ID-token och deras innehåll i den [v2.0-slutpunkten tokens referens](active-directory-v2-tokens.md). |
| Kod |Auktoriseringskod som appen har begärt. Appen kan använda Auktoriseringskoden för att begära en åtkomsttoken för målresursen. En auktoriseringskod är mycket kortvariga. Vanligtvis en auktoriseringskod upphör att gälla om ungefär 10 minuter. |
| state |Om en parametern state ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att värdena i begäran och svar är identiska. |

### <a name="error-response"></a>Felsvar
Felsvar kan även skickas till omdirigeringen-URI så att appen kan hantera dem på rätt sätt. Ett felsvar ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En felkodsträngen som du kan använda för att klassificera typer av fel som inträffar och reagera på fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett autentiseringsfel. |

En beskrivning av felkoder och rekommenderade klientens svar finns i [felkoder för slutpunkt-auktoriseringsfel](#error-codes-for-authorization-endpoint-errors).

När du har en auktoriseringskod och ett ID-token kan du logga in användaren och få åtkomst-token för deras räkning. Om du vill registrera användare i, måste du verifiera ID-token [exakt enligt](#validate-the-id-token). Om du vill få åtkomst-token, följer du stegen som beskrivs i vår [dokumentationen för OAuth-protokollet](active-directory-v2-protocols-oauth-code.md#request-an-access-token).
