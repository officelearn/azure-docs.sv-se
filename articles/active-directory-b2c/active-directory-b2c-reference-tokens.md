---
title: Token-referens i Azure Active Directory B2C | Microsoft Docs
description: Vilka typer av token som utfärdats i Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e2ca582c9ec767e9b810c574e3efddc6485bb6a0
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "34709316"
---
# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C: Token-referens

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) genererar flera typer av säkerhetstoken som den behandlar varje [autentiseringsflödet](active-directory-b2c-apps.md). Det här dokumentet beskriver format, säkerhet egenskaperna och innehållet i varje typ av token.

## <a name="types-of-tokens"></a>Typer av token
Azure AD B2C stöder den [OAuth 2.0-protokollet för auktorisering](active-directory-b2c-reference-protocols.md), som utnyttjar både token uppdatera token. Det stöder också autentisering och logga in via [OpenID Connect](active-directory-b2c-reference-protocols.md), som innehåller en tredje typ av token: ID-token. Var och en av dessa token representeras som ett ägartoken.

Ett ägartoken är en förenklad säkerhetstoken som ger ”ägar” åtkomst till en skyddad resurs. Innehavaren är någon part som kan ge token. Azure AD B2C måste de först autentisera en part innan det kan ta emot ett ägartoken. Men om de nödvändiga stegen inte vidtas för att skydda token i överföringen och lagringen, den kan snappas upp och används av en oavsiktlig part. Vissa säkerhetstoken har en inbyggd mekanism för att förhindra att obehöriga personer använder dem, men ägar-token har inte den här mekanismen. De måste transporteras i en säker kanal, till exempel transport layer security (HTTPS).

Om ett ägartoken överförs utanför en säker kanal, kan en skadlig part använda en man-in-the-middle-attack och hämta token som du kan använda den för att få obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper tillämpas när ägar-token lagras eller cachelagras för senare användning. Se alltid till att appen skickar och lagrar ägar-token på ett säkert sätt.

Ytterligare information om säkerhet på ägar-token finns [RFC 6750 avsnitt 5](http://tools.ietf.org/html/rfc6750).

Många av de token som Azure AD B2C utfärdar implementeras som JSON web token (JWTs). En JWT är ett kompakt, URL-säkert sätt att överföra information mellan två parter. JWTs innehåller information som kallas anspråk. Dessa är intyg för information om innehavaren och föremål för token. Anspråk i JWTs är JSON-objekt som är kodade och serialiseras för överföring. Eftersom JWTs som utfärdats av Azure AD B2C signerad men inte krypterad, kan du enkelt kontrollerar du innehållet i en JWT för felsökning av den. Det finns flera verktyg som kan göra detta, inklusive [jwt.ms](https://jwt.ms). Mer information om JWTs avser [JWT specifikationer](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>ID-token

En ID-token är en typ av säkerhetstoken som din app som tar emot från Azure AD B2C `/authorize` och `/token` slutpunkter. ID-token representeras som [JWTs](#types-of-tokens), och de innehåller anspråk som du kan använda för att identifiera användare i din app. När du har köpt ID-token från den `/authorize` slutpunkt och de görs det med hjälp av den [implicita flödet](active-directory-b2c-reference-spa.md), som ofta används för användare som loggar in till javascript baserat webbprogram. När du har köpt ID-token från den `/token` slutpunkt och de görs det med hjälp av den [kod flödet](active-directory-b2c-reference-oidc.md), håller det token dolda från webbläsaren. På så sätt kan token som skickas på ett säkert sätt i HTTP-begäranden för kommunikation mellan två komponenter av samma program eller tjänst. Du kan använda anspråken i en ID-token som du vill. De används ofta att visa kontoinformation eller gör besluten om åtkomstkontroll i en app.  

ID-token är signerat, men de för närvarande är krypterade inte. När din app eller API får du en ID-token, måste [verifiera signaturen](#token-validation) att bevisa att token är autentiska. Din app eller ett API måste du också verifiera några anspråk i token för att bevisa att de är giltiga. De anspråk som verifieras av en app kan variera beroende på krav för scenarier, men din app måste utföra vissa [vanliga anspråk verifieringar](#token-validation) i varje scenario.

#### <a name="sample-id-token"></a>Exempel-ID-token
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Åtkomsttoken

En åtkomst-token är också en typ av säkerhetstoken som din app som tar emot från Azure AD B2C `/authorize` och `/token` slutpunkter. Åtkomsttoken även visas i form av [JWTs](#types-of-tokens), och de innehåller anspråk som du kan använda för att identifiera de beviljar behörigheterna att dina API: er. Åtkomsttoken är signerat, men de för närvarande är krypterade inte. Åtkomst-token ska användas för att ge åtkomst till API: er och resurs-servrar. Mer information om hur du [använder åtkomsttoken](active-directory-b2c-access-tokens.md). 

När din API tar emot en åtkomst-token, måste [verifiera signaturen](#token-validation) att bevisa att token är autentiska. Din API måste också verifiera några anspråk i token för att bevisa att de är giltiga. De anspråk som verifieras av en app kan variera beroende på krav för scenarier, men din app måste utföra vissa [vanliga anspråk verifieringar](#token-validation) i varje scenario.

### <a name="claims-in-id-and-access-tokens"></a>Anspråk i ID och åtkomst-token

När du använder Azure AD B2C har detaljerad kontroll över innehållet i dina token. Du kan konfigurera [principer](active-directory-b2c-reference-policies.md) att skicka vissa uppsättningar av informationen i anspråk som din app krävs för åtgärderna. Dessa anspråk kan innehålla standardegenskaper, till exempel användarens `displayName` och `emailAddress`. De kan även inkludera [anpassade användarattribut](active-directory-b2c-reference-custom-attr.md) som du kan definiera i din B2C-katalog. Varje ID och åtkomst-token som visas innehåller en viss uppsättning säkerhetsrelaterade anspråk. Dina program kan använda dessa anspråk för att autentisera användare och förfrågningar på ett säkert sätt.

Observera att anspråk i ID-token inte returneras i någon särskild ordning. Dessutom kan du introduceras nya anspråk i ID-token när som helst. Din app bryter bör inte när nya anspråk introduceras. Här är de anspråk som du förväntar dig att finns i ID och åtkomst-token som utfärdats av Azure AD B2C. Ytterligare anspråk bestäms av principer. Försök undersöks anspråk i exempel-ID-token genom att klistra in det i övningen [jwt.ms](https://jwt.ms). Mer information finns i den [OpenID Connect specifikationen](http://openid.net/specs/openid-connect-core-1_0.html).

| Namn | Begär | Exempelvärde | Beskrivning |
| --- | --- | --- | --- |
| Målgrupp |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |En målgrupp anspråk identifierar den avsedda mottagaren av token. Azure AD B2C är målgruppen din app program-ID som tilldelats din app i portalen för registrering av app. Din app ska verifiera det här värdet och avvisa token om det inte matchar. |
| Utfärdare |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |Det här anspråket identifierar den säkerhetstokentjänst (STS) som skapar och returnerar token. Du identifierar även Azure AD-katalog där användaren autentiserades. Appen bör verifiera utfärdaren anspråk så att token som kommer från Azure Active Directory v2.0-slutpunkten. |
| Utfärdat till |`iat` |`1438535543` |Detta anspråk är den tid då token har utfärdats, representeras i epok tid. |
| Förfallotid |`exp` |`1438539443` |Förfallotiden anspråk är den tid då token blir ogiltigt, som representeras i epok tid. Din app ska använda detta anspråk för att kontrollera giltigheten för livslängd för token. |
| inte före |`nbf` |`1438535543` |Detta anspråk är den tid som token blir giltigt, representeras i epok tid. Detta är vanligtvis samma som den tid som token har utfärdats. Din app ska använda detta anspråk för att kontrollera giltigheten för livslängd för token. |
| Version |`ver` |`1.0` |Detta är versionen av ID-token som definierats av Azure AD. |
| koden hash |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |En kod hash ingår i en ID-token endast när token som utfärdas tillsammans med en kod för auktorisering av OAuth 2.0. En kod hash kan användas för att bekräfta en Auktoriseringskoden är äkta. Mer information om hur du utför den här verifieringen finns i [OpenID Connect specifikationen](http://openid.net/specs/openid-connect-core-1_0.html).  |
| åtkomst-token hash |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |En åtkomst-token hash ingår i en ID-token endast när token som utfärdas tillsammans med en OAuth 2.0-åtkomsttoken. En åtkomst-token-hash kan användas för att bekräfta en åtkomst-token är äkta. Mer information om hur du utför den här verifieringen finns i [OpenID Connect-specifikationen](http://openid.net/specs/openid-connect-core-1_0.html)  |
| temporärt ID |`nonce` |`12345` |Ett temporärt ID är en strategi som används för att minimera token replay-attacker. Appen kan ange ett temporärt ID i en auktoriseringsbegäran om med hjälp av den `nonce` Frågeparametern. Värdet du anger i begäran kommer orsakat ska ändras i den `nonce` anspråk för en ID-token. Detta kan din app att verifiera värdet mot det värde som det anges på begäran, som associerar appens session med en viss ID-token. Din app ska utföra den här verifieringen under verifieringsprocessen ID-token. |
| Ämne |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Detta är principal om vilka token Assert information, till exempel användare av en app. Det här värdet är oföränderlig och kan inte tilldela om eller återanvänds. Det kan användas för att utföra auktoriseringskontroller på ett säkert sätt, till exempel när token som används för att komma åt en resurs. Som standard fylls ämne anspråk med objekt-ID för användaren i katalogen. Läs mer i [Azure Active Directory B2C: Token, session och enkel inloggning konfiguration](active-directory-b2c-token-session-sso.md). |
| Autentisering kontexten klassreferens |`acr` |Inte tillämpligt |Inte används för närvarande, med undantag för äldre principer. Läs mer i [Azure Active Directory B2C: Token, session och enkel inloggning konfiguration](active-directory-b2c-token-session-sso.md). |
| Förtroende framework-princip |`tfp` |`b2c_1_sign_in` |Detta är namnet på den princip som användes för att hämta ID-token. |
| Autentisering |`auth_time` |`1438535543` |Detta anspråk är den tid då en sista angivna autentiseringsuppgifter, som representeras i epok tid. |

### <a name="refresh-tokens"></a>Uppdatera token
Uppdatera token är säkerhetstokens som din app kan använda för att hämta nya ID-token och åtkomst till token i en OAuth 2.0-flödet. De ger din app långsiktiga åtkomst till resurser åt användare utan interaktion med användare.

Ta emot en uppdatering token i svaret token din app måste begära den `offline_acesss` omfång. Mer information om den `offline_access` omfång, referera till den [protokollreferens för Azure AD B2C](active-directory-b2c-reference-protocols.md).

Uppdatera token är och alltid ska vara, helt ogenomskinlig till din app. De kan har utfärdats av Azure AD och granskas och tolkas bara av Azure AD. De är långlivade, men din app ska inte skrivas med förväntningen som en uppdateringstoken ska gälla för en viss tidsperiod. Uppdaterings-tokens kan vara inaktuella när som helst för en mängd olika skäl. Det enda sättet för din app att veta om en uppdateringstoken är giltig är att försöka lösa genom att göra en tokenbegäran till Azure AD.

När du lösa in en uppdateringstoken för en ny token (och om din app har beviljats av `offline_access` omfattning), du får en ny uppdateringstoken i token svaret. Du bör spara den nyligen utfärdade uppdateringstoken. Den ska ersätta den uppdateringstoken som du tidigare använt i begäran. Detta hjälper garantera att uppdaterings-tokens vara giltigt så länge som möjligt.

## <a name="token-validation"></a>Token-verifiering
För att validera en token, bör du kontrollera både signatur och anspråk av token din app.

Många bibliotek med öppen källkod är tillgängliga för att validera JWTs, beroende på ditt språk. Vi rekommenderar att du utforska alternativen i stället för att implementera din egen valideringslogik. Informationen i den här guiden hjälper dig att lära dig hur du använder dessa bibliotek korrekt.

### <a name="validate-the-signature"></a>Verifiera signaturen
En JWT innehåller tre segment, avgränsade med den `.` tecken. Det första segmentet är den *huvud*, andra är den *brödtext*, och tredje är den *signatur*. Signaturen segment kan användas för att kontrollera äkthet token så att den kan vara betrott av din app.

Azure AD B2C-token är signerade med branschstandard asymmetriska krypteringsalgoritmer, till exempel RSA-256. Rubriken för token innehåller information om metoden nyckel och kryptering används för att signera token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Den `alg` anspråk anger algoritmen som används för att signera token. Den `kid` anspråk anger särskild offentlig nyckel som användes för att signera token.

Azure AD kan logga en token med hjälp av någon av en viss uppsättning privat-offentligt nyckelpar vid en given tidpunkt. Azure AD roterar möjliga uppsättning nycklar med jämna mellanrum, så att din app ska skrivas till att hantera dessa viktiga ändringar automatiskt. En rimlig frekvens för att söka efter uppdateringar för offentliga nycklar som används av Azure AD är 24 timmar.

Azure AD B2C har en slutpunkt för OpenID Connect metadata. Detta gör att appar att hämta information om Azure AD B2C vid körning. Informationen omfattar slutpunkter, token innehåll och nycklar för tokensignering. Din B2C-katalog innehåller en JSON-dokumentet för metadata för varje princip. Till exempel Metadatadokumentet för den `b2c_1_sign_in` princip i `fabrikamb2c.onmicrosoft.com` finns på:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` är B2C-katalog som används för att autentisera användaren, och `b2c_1_sign_in` är den princip som användes för att hämta token. Att avgöra vilken princip som användes för att logga en token (och var du kan hämta metadata), har du två alternativ. Först principnamnet ingår i den `acr` anspråk i token. Du kan parsa anspråk från innehållet i JWT av Base64-avkodning brödtexten och avserialisering av JSON-strängen som är ett resultat. Den `acr` anspråk ska vara namnet på den princip som användes för att utfärda en token.  Ett annat alternativ är att koda principen i värdet för den `state` parameter när du skickar en begäran och avkoda det för att avgöra vilken princip som har använts. Antingen metoden är giltig.

Metadatadokumentet är ett JSON-objekt som innehåller flera användbara uppgifter. Dessa inkluderar platsen för slutpunkter som krävs för att utföra OpenID Connect-autentisering. De omfattar också `jwks_uri`, vilket ger platsen för en uppsättning offentliga nycklar som används för att signera token. Att plats som anges här, men det är bäst att hämta platsen dynamiskt med hjälp av Metadatadokumentet och parsning ut `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

JSON-dokumentet på URL: en innehåller alla offentliga viktig information som används på en viss tidpunkt. Din app kan använda den `kid` anspråk i JWT-huvudet för att välja den offentliga nyckeln i JSON-dokument som används för att logga en viss token. Den kan utföra signaturverifiering genom att använda rätt offentlig nyckel och den angivna algoritmen.

En beskrivning av hur du utför signaturverifiering ligger utanför omfånget för det här dokumentet. Det finns många bibliotek med öppen källkod som hjälper dig med detta om du behöver det.

### <a name="validate-the-claims"></a>Validera anspråk
När din app eller API får du en ID-token, bör det också utföra flera kontroller mot anspråk i ID-token. Dessa inkludera, men är inte begränsade till:

* Den **målgruppen** anspråk: Detta verifierar att token ID är avsedd för att tilldelas till din app.
* Den **inte före** och **förfallotid** anspråk: dessa verifierar att token ID inte har gått ut.
* Den **utfärdaren** anspråk: Detta verifierar att token har utfärdats till din app av Azure AD.
* Den **temporärt ID**: Detta är en strategi för tokenrepetition attack lösning.

En fullständig lista över verifieringar som din app ska utföra avser den [OpenID Connect specifikationen](https://openid.net). Information om de förväntade värdena för dessa anspråk som ingår i den föregående [token avsnittet](#types-of-tokens).  

## <a name="token-lifetimes"></a>Livslängder för token
Följande token livslängd som ytterligare din vetskap. De kan hjälpa dig när du utvecklar och felsöka appar. Observera att dina appar inte att skriva till räknar med någon av dessa livslängd förblir konstant. De kan och kommer att ändras. Läs mer om den [anpassning av token livslängd](active-directory-b2c-token-session-sso.md) i Azure AD B2C.

| Token | Livslängd | Beskrivning |
| --- | --- | --- |
| ID-token |En timma |ID-token gäller vanligtvis i en timme. Ditt webbprogram kan använda den här livslängd för att underhålla en egen sessioner med användare (rekommenderas). Du kan också välja en annan session livslängd. Om din app måste hämta ett nytt ID-token, behöver den bara begära en ny inloggning till Azure AD. Om en användare har en giltig webbläsarsession med Azure AD, kan användaren inte krävas att ange autentiseringsuppgifter igen. |
| Uppdatera token |Upp till 14 dagar |En enskild uppdateringstoken är giltig för 14 dagar. En uppdateringstoken kan dock bli ogiltiga när som helst för en rad orsaker. Din app ska fortsätta att försöka använda en uppdateringstoken förrän misslyckas begäran eller din app ersätter uppdateringstoken med en ny. En uppdateringstoken kan också blir ogiltiga om 90 dagar har gått sedan användaren senast angivna autentiseringsuppgifter. |
| Auktoriseringskoder |Fem minuter |Auktoriseringskoder är avsiktligt tillfällig. De bör lösas omedelbart för åtkomst-token, ID-token eller uppdateringstoken när de tas emot. |

