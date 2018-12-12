---
title: Token-referens i Azure Active Directory B2C | Microsoft Docs
description: Vilka typer av token som utfärdas i Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d1c9101f10342f98803a4ace420abbed5d49ba23
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52880122"
---
# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C: Token-referens

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) genererar flera typer av säkerhetstoken när den går igenom varje [autentiseringsflödet](active-directory-b2c-apps.md). Det här dokumentet beskriver format, security egenskaperna och innehållet i varje typ av token.

## <a name="types-of-tokens"></a>Typer av token
Azure AD-B2C stöder den [OAuth 2.0-protokollet för auktorisering](active-directory-b2c-reference-protocols.md), som utnyttjar både åtkomsttoken och uppdateringstoken. Det stöder också autentisering och inloggning via [OpenID Connect](active-directory-b2c-reference-protocols.md), som introducerar en tredje typ av token: ID-token. Var och en av dessa token representeras som en ägartoken.

En ägartoken är en förenklad säkerhetstoken som ger ”ägar” åtkomst till en skyddad resurs. Innehavaren är en part som kan utgöra en token. Azure AD B2C måste först autentisera en part innan det kan ta emot en ägartoken. Men om steg som krävs inte vidtas för att skydda token i överföring och lagring, den kan fångas upp och används av en oavsiktlig part. Vissa säkerhetstoken har en inbyggd mekanism för att förhindra obehöriga personer från att använda dem, men ägar-token har inte den här mekanismen. De måste transporteras i en säker kanal, till exempel transport layer security (HTTPS).

Om en ägartoken överförs utanför en säker kanal, kan en obehörig part använda en man-in-the-middle-attack för att hämta token och använda den för att få obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper gäller när ägar-token lagras eller cachen för senare användning. Se alltid till att appen överför och lagrar ägar-token på ett säkert sätt.

Ytterligare säkerhetsåtgärder på ägar-token, se [RFC 6750 avsnitt 5](https://tools.ietf.org/html/rfc6750).

Många av de token som Azure AD B2C utfärdar implementeras som JSON web token (JWTs). En JWT är ett kompakt, URL-säkert sätt att överföra information mellan två parter. JWTs innehåller information som kallas anspråk. Det här är intyg om innehavaren och ämnet för token. Anspråk i JWTs är JSON-objekt som är kodade och serialiseras för överföring. Eftersom JWTs som utfärdats av Azure AD B2C är signerad, men krypteras inte, kan du enkelt granska innehållet i en JWT för att felsöka den. Det finns flera verktyg som kan göra detta, inklusive [jwt.ms](https://jwt.ms). Mer information om JWTs i [JWT-specifikationer](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>ID-tokens

Ett ID-token är en typ av säkerhetstoken som din app som tar emot från Azure AD B2C `/authorize` och `/token` slutpunkter. ID-token visas i form av [JWTs](#types-of-tokens), och de innehåller anspråk som du kan använda för att identifiera användare i din app. När ID-token är upptagna från den `/authorize` slutpunkt kan de görs det med hjälp av den [implicit flöde](active-directory-b2c-reference-spa.md), vilket ofta används för användare som loggar in till javascript-baserade webbprogram. När ID-token är upptagna från den `/token` slutpunkt kan de görs det med hjälp av den [kod flow](active-directory-b2c-reference-oidc.md), som ser till att den token som är dolda från webbläsaren. På så sätt kan token som skickas på ett säkert sätt i HTTP-begäranden för kommunikation mellan två komponenter i samma programmet eller tjänsten. Du kan använda anspråken i en ID-token som du vill. De används ofta att visa kontoinformation eller gör besluten om åtkomstkontroll i en app.  

ID-token är signerade, men de för närvarande är krypterad inte. När din app eller API får du en ID-token, måste den [källpaketets signatur](#token-validation) att bevisa att token är autentiska. Din app eller API: et måste du också bekräfta några anspråken i token för att bevisa att den är giltig. De anspråk som verifierats av en app kan variera beroende på scenariokrav, men din app måste utföra några [vanliga anspråk verifieringar](#token-validation) i alla scenarier.

#### <a name="sample-id-token"></a>Exemplet ID-token
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

### <a name="access-tokens"></a>Åtkomsttokens

En åtkomsttoken är också en typ av säkerhetstoken som din app som tar emot från Azure AD B2C `/authorize` och `/token` slutpunkter. Åtkomsttoken även visas i form av [JWTs](#types-of-tokens), och de innehåller anspråk som du kan använda för att identifiera de beviljade behörigheterna till dina API: er. Åtkomsttoken är signerade, men de för närvarande är krypterad inte. Åtkomsttoken ska användas för att ge åtkomst till API: er och resurs-servrar. Mer information om hur du [använder åtkomsttoken](active-directory-b2c-access-tokens.md). 

När ditt API får en åtkomst-token, måste den [källpaketets signatur](#token-validation) att bevisa att token är autentiska. Ditt API måste också bekräfta några anspråken i token för att bevisa att den är giltig. De anspråk som verifierats av en app kan variera beroende på scenariokrav, men din app måste utföra några [vanliga anspråk verifieringar](#token-validation) i alla scenarier.

### <a name="claims-in-id-and-access-tokens"></a>Anspråk i token-ID och åtkomst

När du använder Azure AD B2C har detaljerad kontroll över innehållet i dina token. Du kan konfigurera [användarflöden](active-directory-b2c-reference-policies.md) och anpassade principer för att skicka vissa uppsättningar av användardata i anspråk som din app kräver för driften. Dessa anspråk kan innehålla standardegenskaper, till exempel användarens `displayName` och `emailAddress`. De kan också innehålla [anpassade användarattribut](active-directory-b2c-reference-custom-attr.md) som du kan definiera i din B2C-katalog. Varje ID och tillgång till token som visas innehåller en viss uppsättning säkerhetsrelaterade anspråk. Dina program kan använda de här anspråken att på ett säkert sätt autentisera användare och förfrågningar.

Observera att anspråk i ID-token inte returneras i någon särskild ordning. Dessutom kan du introduceras nya anspråk i ID-token när som helst. Din app ska inte bryter när nya anspråk introduceras. Här följer de anspråk som du förväntar dig att finnas i ID och åtkomst-token som utfärdas av Azure AD B2C. Ytterligare anspråk bestäms av principer. Du bör testa i Granska anspråk i exemplet ID-token genom att klistra in den i [jwt.ms](https://jwt.ms). Mer information finns i den [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html).

| Namn | Begäran | Exempelvärde | Beskrivning |
| --- | --- | --- | --- |
| Målgrupp |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |En målgrupp anspråket identifierar den avsedda mottagaren av token. Målgruppen är appens program-ID, som tilldelats din app i portalen för registrering av appen för Azure AD B2C. Din app ska verifiera det här värdet och avvisa token om det inte matchar. Målgruppen är synonyma med resursen. |
| Utfärdare |`iss` |`https://{tenantname}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |Det här anspråket identifierar den säkerhetstokentjänst (STS) som skapar och returnerar token. Den identifierar också Azure AD-katalog där användaren autentiserades. Din app bör verifiera utfärdare anspråk så att token kom från Azure Active Directory v2.0-slutpunkten. |
| Utfärdat på |`iat` |`1438535543` |Det här kravet är den tid då token utfärdats, representeras i epoktid. |
| Förfallotid |`exp` |`1438539443` |Förfallotiden anspråk är den tid då token blir ogiltig, som representeras i epoktid. Din app ska använda detta anspråk för att kontrollera giltigheten för livslängd för token. |
| Inte före |`nbf` |`1438535543` |Det här kravet är den tid då token blir giltigt, representeras i epoktid. Detta är vanligtvis samma som den tid som token utfärdats. Din app ska använda detta anspråk för att kontrollera giltigheten för livslängd för token. |
| Version |`ver` |`1.0` |Det här är versionen av ID-token som definieras av Azure AD. |
| Kod hash |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |En kod hash ingår i en ID-token endast när token utfärdas tillsammans med en OAuth 2.0-auktoriseringskod. En kod hash kan användas för att bekräfta en auktoriseringskod är äkta. Mer information om hur du utför den här verifieringen finns i den [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Åtkomst-token-hash |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |En åtkomst-token-hash som ingår i en ID-token endast när token utfärdas tillsammans med en OAuth 2.0-åtkomsttoken. En åtkomst-token-hash kan användas för att bekräfta en åtkomst-token är äkta. Mer information om hur du utför den här verifieringen finns i den [OpenID Connect-specifikation](https://openid.net/specs/openid-connect-core-1_0.html)  |
| nonce |`nonce` |`12345` |En nonce är en strategi för att åtgärda token repetitionsattacker. Din app kan ange en nonce i en begäran om godkännande med hjälp av den `nonce` frågeparameter. Värdet du anger i begäran kommer avges ska ändras i den `nonce` anspråk för en endast ID-token. På så sätt kan din app för att kontrollera värden mot det värde som det angetts på begäran, som associerar appens session med en viss ID-token. Din app ska utföra den här verifieringen under verifieringsprocessen för ID-token. |
| Subjekt |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Det här är huvudnamn som token kontrollerar information, t.ex användare av en app. Det här värdet kan inte ändras och det går inte att tilldela om eller återanvänds. Det kan användas för att utföra auktoriseringskontroller på ett säkert sätt, till exempel när token används för att komma åt en resurs. Som standard fylls anspråk för ämne med objekt-ID för användaren i katalogen. Mer information finns i [Azure Active Directory B2C: Token, session och konfiguration för enkel inloggning](active-directory-b2c-token-session-sso.md). |
| Referens för autentisering kontext-klass |`acr` |Inte tillämpligt |Inte används för närvarande, utom när det gäller äldre principer. Mer information finns i [Azure Active Directory B2C: Token, session och konfiguration för enkel inloggning](active-directory-b2c-token-session-sso.md). |
| Lita på framework-princip |`tfp` |`b2c_1_sign_in` |Det här är namnet på den princip som användes för att hämta ID-token. |
| Autentisering |`auth_time` |`1438535543` |Det här kravet är den tid då en användare senast angivna autentiseringsuppgifter, som representeras i epoktid. |

### <a name="refresh-tokens"></a>Uppdatera token
Uppdatera token är säkerhetstokens som din app kan använda för att hämta nya ID-token och åtkomsttoken i ett OAuth 2.0-flöde. Du får din app långsiktig åtkomst till resurser för användare utan interaktion med användare.

Att ta emot en uppdatering av token i svaret token din app måste begära den `offline_access` omfång. Mer information om den `offline_access` omfång, referera till den [protokollreferens för Azure AD B2C](active-directory-b2c-reference-protocols.md).

Uppdatera token är det alltid är, helt täckande för din app. De kan har utfärdats av Azure AD och granskas och tolkas bara av Azure AD. De är långlivade, men din app ska inte skrivas med förväntan om att en uppdateringstoken varar en viss tidsperiod. Uppdateringstoken kan vara inaktuella när som helst av olika skäl. Det enda sättet för din app att veta om en uppdateringstoken är giltig är att försöka lösa in den genom att göra en begäran om token till Azure AD.

När du har löst in en uppdateringstoken för en ny token (och om din app har beviljats den `offline_access` omfång), du får en ny uppdateringstoken i token-svaret. Du bör spara nyligen utfärdade uppdateringstoken. Det bör ersätta den uppdateringstoken som du tidigare använt i begäran. Detta hjälper till att garantera att dina uppdateringstoken förblir giltigt så länge som möjligt.

## <a name="token-validation"></a>Verifieringen av åtkomsttoken
För att verifiera en token kan ska din app Kontrollera både signatur och anspråk i token.

Många bibliotek med öppen källkod är tillgängliga för att verifiera JWTs, beroende på det språk du föredrar. Vi rekommenderar att du utforska dessa alternativ i stället för att implementera dina egna validering av logik. Informationen i den här guiden hjälper dig att använda dessa bibliotek normalt.

### <a name="validate-the-signature"></a>Verifiera signaturen
En JWT innehåller tre segment, avgränsade med den `.` tecken. Det första segmentet är den *rubrik*, andra är den *brödtext*, och tredje är den *signatur*. Signatur-segment kan användas för att verifiera äkthet token så att det kan vara betrott av din app.

Azure AD B2C-token signeras med hjälp av branschstandard asymmetriska krypteringsalgoritmer, till exempel RSA-256. Rubriken för token innehåller information om metoden nyckel och kryptering används för att signera token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Den `alg` anspråk anger algoritmen som används för att signera token. Den `kid` anspråk anger den specifika offentliga nyckel som användes för att signera token.

Vid en given tidpunkt logga Azure AD en token med hjälp av någon av en viss uppsättning offentligt / privat nyckelpar. Azure AD roterar möjliga uppsättningen med nycklar med jämna mellanrum, så att din app ska skrivas till hanterar dessa viktiga ändringar automatiskt. En rimlig frekvens för att söka efter uppdateringar till de offentliga nycklar som används av Azure AD är 24 timmar.

Azure AD B2C har en slutpunkt för OpenID Connect metadata. På så sätt kan appar att hämta information om Azure AD B2C vid körning. Informationen omfattar slutpunkter, token innehåll och nycklar för tokensignering. Din B2C-katalog innehåller ett JSON-dokument för metadata för varje princip. Till exempel metadatadokument för den `b2c_1_sign_in` principen i `fabrikamb2c.onmicrosoft.com` finns på:

```
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` är B2C-katalog som används för att autentisera användaren, och `b2c_1_sign_in` är den princip som används för att hämta token. Att avgöra vilken princip som användes för att registrera en token (och var du kan hämta metadata), har du två alternativ. Först namnet på principen som ingår i den `acr` anspråk i token. Du kan parsa anspråk från innehållet i JWT av Base64-avkodning brödtexten och avserialisering av JSON-strängen som är ett resultat. Den `acr` anspråk kommer att namnet på den princip som användes för att utfärda token.  Ett annat alternativ är att koda principen i värdet för den `state` parameter när du skickar en begäran och avkoda det för att avgöra vilken princip som har använts. Någon av metoderna är giltig.

Metadatadokumentet är ett JSON-objekt som innehåller flera användbara uppgifter för. Dessa inkluderar platsen för slutpunkter som krävs för att utföra autentisering med OpenID Connect. De omfattar också `jwks_uri`, vilket ger platsen för en uppsättning offentliga nycklar som används för att signera token. Att platsen anges här, men det är bäst att hämta platsen dynamiskt med hjälp av webbplatsen för metadata och tolka ut `jwks_uri`:

```
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

JSON-dokument som finns på denna URL innehåller alla offentliga viktig information som används på en viss tidpunkt. Din app kan använda den `kid` anspråk i JWT-huvudet för att välja den offentliga nyckeln i JSON-dokument som används för att logga en viss token. Den kan sedan utföra signaturverifiering genom att använda rätt offentlig nyckel och den angivna algoritmen.

En beskrivning av hur du utför signaturverifiering ligger utanför omfånget för det här dokumentet. Många bibliotek med öppen källkod är tillgängliga som hjälper dig med detta om du behöver den.

### <a name="validate-the-claims"></a>Validera anspråk
När din app eller API får du en ID-token, bör det också utföra flera kontroller mot anspråken i ID-token. Dessa inkludera, men är inte begränsade till:

* Den **målgrupp** anspråk: Detta verifierar att ID-token ska tilldelas till din app.
* Den **inte före** och **förfallotid** anspråk: dessa Kontrollera att ID-token inte har gått ut.
* Den **utfärdare** anspråk: Detta verifierar att token har utfärdats till din app av Azure AD.
* Den **nonce**: det här är en strategi för token repetitionsattacker nätverksattacker.

En fullständig lista över verifieringar som din app ska utföra, finns det [OpenID Connect-specifikationen](https://openid.net). Information om de förväntade värdena för dessa anspråk som ingår i det föregående [token avsnittet](#types-of-tokens).  

## <a name="token-lifetimes"></a>Livslängder för token
Följande tokenlivslängder anges för att vidareutveckla dina kunskaper. De kan hjälpa dig när du utvecklar och felsöker appar. Observera att dina appar inte att skriva som händer något av dessa livslängd ska ändras. De kan och kommer att ändras. Läs mer om den [anpassning av tokenlivslängder](active-directory-b2c-token-session-sso.md) i Azure AD B2C.

| Token | Livslängd | Beskrivning |
| --- | --- | --- |
| ID-tokens |En timma |ID-token gäller vanligtvis i en timme. Din webbapp kan använda den här livslängd för att underhålla en egen sessioner med användare (rekommenderas). Du kan också välja en annan session livslängd. Om din app behöver att hämta ett nytt ID-token, måste den helt enkelt skapa en ny inloggning-begäran till Azure AD. Om en användare har en giltig webbläsarsession med Azure AD, kan användaren inte krävas att ange autentiseringsuppgifter igen. |
| Uppdatera token |Upp till 14 dagar |En enda uppdateringstoken är giltig för upp till 14 dagar. En uppdateringstoken kan dock bli ogiltiga när som helst för en rad orsaker. Din app ska fortsätta att försöka använda en uppdateringstoken tills begäran misslyckas, eller din app ersätter uppdateringstoken med ett nytt lösenord. En uppdateringstoken kan också bli ogiltiga om 90 dagar har passerat sedan du senast angivna autentiseringsuppgifter. |
| Auktoriseringskoder |Fem minuter |Auktoriseringskoder är avsiktligt tillfällig. De bör lösas omedelbart för åtkomsttoken, ID-token eller uppdateringstoken när de tas emot. |

