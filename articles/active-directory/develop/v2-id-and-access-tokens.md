---
title: Azure Active Directory v2.0 tokens referens | Microsoft Docs
description: Vilka typer av token och anspråk som orsakats av Azure AD v2.0-slutpunkten
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 23d041311c33110bf11efc78d162243a4bb25778
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997768"
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Azure Active Directory v2.0 token-referens

V2.0-slutpunkten Azure Active Directory (Azure AD) genererar flera typer av säkerhetstoken i varje [autentiseringsflödet](v2-app-types.md). Den här referensen beskriver format, security egenskaperna och innehållet i varje typ av token.

> [!NOTE]
> V2.0-slutpunkten har inte stöd för alla Azure Active Directory-scenarier och funktioner. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, [v2.0 begränsningar](active-directory-v2-limitations.md).

## <a name="types-of-tokens"></a>Typer av token

V2.0-slutpunkten har stöd för den [OAuth 2.0-protokollet för auktorisering](active-directory-v2-protocols.md), som använder åtkomsttoken och uppdateringstoken. V2.0-slutpunkten stöder också autentisering och inloggning via [OpenID Connect](active-directory-v2-protocols.md). OpenID Connect introducerar en tredje typ av token, ID-token. Var och en av dessa token representeras som en *ägar* token.

En ägartoken är en förenklad säkerhetstoken som ger ägar-åtkomst till en skyddad resurs. Innehavaren är en part som kan utgöra en token. Även om en part måste autentisera med Azure AD för att ta emot ägartoken om stegen inte vidtas för att skydda token under överföringen och lagringen, kan den fångas upp och används av en oavsiktlig part. Vissa säkerhetstoken har en inbyggd mekanism för att förhindra att obehöriga personer från att använda dem, men inte ägar-token. Ägar-token måste transporteras i en säker kanal, till exempel transport layer security (HTTPS). Om en ägartoken överförs utan den här typen av säkerhet, en obehörig part kan använda en ”man-in-the-middle attack” hämta token och använder den för obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper gäller när du lagrar eller cachelagring ägar-token för senare användning. Se alltid till att din app på ett säkert sätt skickar och lagrar ägar-token. Mer säkerhet för ägartoken, finns i [RFC 6750 avsnitt 5](http://tools.ietf.org/html/rfc6750).

Många av de token som utfärdas av v2.0-slutpunkten är implementerade som JSON Web token (JWTs). En JWT är ett kompakt, URL-säkert sätt att överföra information mellan två parter. Informationen i en JWT kallas en *anspråk*. Det är ett intyg om ägar- och ämnet för token. Anspråk i en JWT är JavaScript Object Notation (JSON)-objekt som är kodade och serialiseras för överföring. Eftersom JWTs som utfärdats av v2.0-slutpunkten är signerad, men krypteras inte, kan du enkelt granska innehållet i en JWT för felsökning. Mer information om JWTs finns i den [JWT-specifikationen](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>ID-token

Ett ID-token är en typ av inloggning säkerhetstoken som din app tar emot när autentisering utförs med hjälp av [OpenID Connect](active-directory-v2-protocols.md). ID-token visas i form av [JWTs](#types-of-tokens), och de innehåller anspråk som du kan använda för att logga in användaren till din app. Du kan använda anspråken i en ID-token på olika sätt. Normalt använder administratörer ID-token att visa kontoinformation eller gör besluten om åtkomstkontroll i en app. V2.0-slutpunkten utfärdar bara en typ av ID-token som har en konsekvent uppsättning anspråk, oavsett vilken typ av användare som är inloggad. Formatera och innehållet i ID-token är samma för användare med personliga Microsoft-konton och arbets-eller skolkonto.

ID-token är för närvarande registrerat men inte är krypterade. När din app tar emot ett ID-token, måste den [källpaketets signatur](#validating-tokens) token för autentisering och validera några anspråken i token för att bevisa dess giltighet. De anspråk som verifierats av en app varierar beroende på krav, men din app måste utföra några [vanliga anspråk verifieringar](#validating-tokens) i alla scenarier.

Vi ger dig fullständig information om anspråk i ID-token i följande avsnitt, förutom en exempel-ID-token. Observera att anspråk i ID-token inte returneras i en viss ordning. Dessutom kan du introduceras nya anspråk i ID-token när som helst. Din app ska inte bryter när nya anspråk introduceras. Följande lista innehåller de anspråk som för närvarande på ett tillförlitligt sätt tolka din app. Du hittar mer information finns i den [OpenID Connect-specifikationen](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Exemplet ID-token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> Klistra in exemplet ID-token till om du vill kontrollera anspråk i exemplet ID-token för bör [jwt.ms](http://jwt.ms/).

#### <a name="claims-in-id-tokens"></a>Anspråk i ID-token

| Namn | Begär | Exempelvärde | Beskrivning |
| --- | --- | --- | --- |
| Målgrupp |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |Identifierar den avsedda mottagaren av token. I ID-token är målgruppen appens program-ID som tilldelats din app i portalen för registrering av Microsoft-program. Din app ska verifiera det här värdet och avvisa token om värdet inte matchar. |
| Utfärdare |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Identifierar den säkerhetstokentjänst (STS) som skapar och återställer token och Azure AD-klient där användaren autentiserades. Din app bör verifiera utfärdare anspråk så att token kom från v2.0-slutpunkten. Det bör också använda anspråket GUID-del för att minska antalet klienter som kan logga in på appen. Det GUID som anger att användaren är en konsument användare från ett Microsoft-konto är `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Utfärdat på |`iat` |`1452285331` |Den tid då token utfärdats representeras i epoktid. |
| Förfallotid |`exp` |`1452289231` |Den tid då token blir ogiltig, representeras i epoktid. Din app ska använda detta anspråk för att kontrollera giltigheten för livslängd för token. |
| Inte före |`nbf` |`1452285331` |Den tid då token som börjar gälla, representeras i epoktid. Det är vanligtvis samma som den utfärdande-tid. Din app ska använda detta anspråk för att kontrollera giltigheten för livslängd för token. |
| version |`ver` |`2.0` |Versionen av ID-token, som definieras av Azure AD. Värdet är för v2.0-slutpunkten `2.0`. |
| klientorganisations-ID |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Ett GUID som representerar den Azure AD-klient som användaren är från. För arbets- och skolkonton-konton är GUID oföränderligt klient-ID för den organisation som användaren tillhör. Värdet är för personliga konton `9188040d-6c67-4c5b-b112-36a304b66dad`. Den `profile` omfattning krävs för att ta emot det här anspråket. |
| Kod hash |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Kod hash ingår i ID-token endast när ID-token utfärdas med en OAuth 2.0-auktoriseringskod. Det kan användas för att bekräfta en auktoriseringskod är äkta. Mer information om hur du utför den här verifieringen, finns i den [OpenID Connect-specifikationen](http://openid.net/specs/openid-connect-core-1_0.html). |
| Åtkomst-token-hash |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Åtkomst-token-hash som ingår i ID token endast när den ID-token som utfärdas med en OAuth 2.0-åtkomsttoken. Det kan användas för att bekräfta en åtkomst-token är äkta. Mer information om hur du utför den här verifieringen, finns i den [OpenID Connect-specifikationen](http://openid.net/specs/openid-connect-core-1_0.html). |
| nonce |`nonce` |`12345` |Temporärt ID är en strategi för hur du minimerar token repetitionsattacker. Din app kan ange en nonce i en begäran om godkännande med hjälp av den `nonce` frågeparameter. Värdet du anger i begäran har genererats i ID-token `nonce` anspråk som ska ändras. Din app kan kontrollera värden mot det värde som det angetts på begäran, som associerar appens session med en specifik ID-token. Din app ska utföra den här verifieringen under verifieringsprocessen för ID-token. |
| namn |`name` |`Babe Ruth` |Anspråk namn innehåller ett läsbart värde som identifierar föremål för token. Värdet är inte säkert att vara unikt, det är föränderliga och den har utformats för att användas endast för visning. Den `profile` omfattning krävs för att ta emot det här anspråket. |
| e-post |`email` |`thegreatbambino@nyy.onmicrosoft.com` |Den primära e-postadress som är associerade med användarkontot, om sådan finns. Värdet är föränderliga och kan ändras över tid. Den `email` omfattning krävs för att ta emot det här anspråket. |
| prioriterade användarnamn |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |Primära användarnamnet som representerar användaren i v2.0-slutpunkten. Det kan vara en e-postadress, telefonnummer eller ett allmänt användarnamn utan angivet format. Värdet är föränderliga och kan ändras över tid. Eftersom det är föränderliga, måste det här värdet inte användas vid auktoriseringsbeslut. Den `profile` omfattning krävs för att ta emot det här anspråket. |
| Ämne |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Huvudkontot som token kontrollerar information, t.ex användare av en app. Det här värdet kan inte ändras och det går inte att tilldela om eller återanvänds. Den kan användas för att utföra auktoriseringskontroller på ett säkert sätt, till exempel när token används för att komma åt en resurs och kan användas som en nyckel i databastabeller. Eftersom ämne är alltid finns i token som Azure AD-problem, rekommenderar vi att använda det här värdet i ett auktoriseringssystem för allmänna. Ämnet är dock en pairwise identifierare – det är unikt för ett visst program-ID. Om en användare loggar in på två olika appar med hjälp av två olika klient-ID, därför får apparna två olika värden för ämne anspråket. Detta kan eller inte är önskvärt beroende på dina krav arkitektur och sekretess. |
| Objekt-ID |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Oföränderligt ID för ett objekt i Microsoft identitetssystem, i det här fallet ett användarkonto. Det kan också användas för att utföra auktoriseringskontroller på ett säkert sätt och som en nyckel i databastabeller. Detta ID identifierar användaren för program – två olika program som loggar in samma användare får samma värde i den `oid` anspråk. Det innebär att den kan användas vid frågor till Microsoft onlinetjänster, till exempel Microsoft Graph. Microsoft Graph returnerar detta ID som den `id` -egenskapen för ett givet användarkonto. Eftersom den `oid` tillåter flera appar att korrelera användare, den `profile` omfattning krävs för att ta emot det här anspråket. Observera att om en enskild användare finns i flera klienter, användaren innehåller ett annat objekt-ID i varje klient – de anses vara olika konton, även om användaren loggar in på varje konto med samma autentiseringsuppgifter. |

### <a name="access-tokens"></a>Åtkomsttoken

V2.0-slutpunkten kan appar från tredje part som är registrerade i Azure AD för att utfärda åtkomsttoken för skyddade resurser, till exempel webb-API: er. Mer information om hur du konfigurerar ett program att utfärda åtkomsttoken finns i [hur du registrerar en app med v2.0-slutpunkten](quickstart-v2-register-an-app.md). När du har registrerat programmet med v2.0-slutpunkten kan utvecklare kan ange åtkomstnivåer, kallas **scope**, för vilken åtkomst token utfärdas. Till exempel den **calendars.read** räckvidden i Microsoft Graph API ger behörighet att läsa användarens kalender. När ditt program tar emot en åtkomsttoken från v2.0-slutpunkten, måste du verifiera den token signatur, utfärdare, målgrupp, förfallotid och andra anspråk beroende på ditt scenario. 

När du begär en åtkomsttoken från v2.0-slutpunkten returnerar v2.0-slutpunkten även metadata om åtkomsttoken för din app ska använda. Informationen omfattar förfallotiden för åtkomst-token och de omfattningar som den är giltig. Din app använder dessa metadata för att utföra intelligent cachelagring av åtkomsttoken utan att behöva parsa öppna den åtkomst-token.

### <a name="refresh-tokens"></a>Uppdatera token

Uppdatera token är säkerhetstoken som din app kan använda för att hämta nya åtkomsttoken i ett flöde för OAuth 2.0. Din app kan använda uppdaterings-tokens för att uppnå långsiktig åtkomst till resurser för en användares räkning utan att användaren interagerar med.

Uppdateringstoken är flera resurs. En uppdateringstoken som togs emot under en Tokenbegäran för en resurs kan lösas in för åtkomst-token till en helt annan resurs.

För att få en uppdatering i ett token svar, din app måste begära och beviljas den `offline_access` omfång. Mer information om den `offline_access` omfång kan du läsa den [medgivande och scope](v2-permissions-and-consent.md) artikeln.

Uppdatera token är det alltid är, helt täckande för din app. De kan har utfärdats av Azure AD v2.0-slutpunkten och endast kontrolleras och tolkas av v2.0-slutpunkten. De är långlivade, men appen inte att skriva förväntar sig att varar en uppdateringstoken för varje tidsperiod. Uppdateringstoken kan vara inaktuella när som helst av olika anledningar - mer information finns i [token återkallade](access-tokens.md#revocation). Det enda sättet för din app att veta om en uppdateringstoken är giltig är att försöka lösa in den genom att göra en tokenbegäran till v2.0-slutpunkten.

När du har löst in en uppdateringstoken för en ny åtkomsttoken (och om din app har beviljats den `offline_access` omfång), du får en ny uppdateringstoken i token-svaret. Spara den nyligen utfärdade uppdateringstoken för att ersätta den som du använde i begäran. Detta garanterar att dina uppdateringstoken förblir giltigt så länge som möjligt.

## <a name="validating-tokens"></a>Verifiera token

För närvarande verifieras endast tokenvalidering dina appar behöver utföra ID-token. För att verifiera en ID-token kan bör din app verifiera både signatur för ID-token och anspråk i ID-token.

<!-- TODO: Link --> Microsoft tillhandahåller bibliotek och kodexempel som visar hur du enkelt hantera tokenvalidering. I nästa avsnitt beskriver vi underliggande processen. Bibliotek för flera från tredje part öppen källkod är tillgängliga för JWT-verifiering. Det finns minst ett bibliotek alternativ för nästan alla plattformar och språk.

### <a name="validate-the-signature"></a>Verifiera signaturen

En JWT innehåller tre segment som avgränsas med den `.` tecken. Det första segmentet kallas den *rubrik*, det andra segmentet är den *brödtext*, och det tredje segmentet är den *signatur*. Signatur-segment kan användas för att verifiera äkthet ID-token så att det kan vara betrott av din app.

ID-token signeras med hjälp av branschstandard asymmetriska krypteringsalgoritmer, till exempel RSA-256. Rubriken för ID-token innehåller information om metoden nyckel och kryptering används för att signera token. Exempel:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Den `alg` anspråk anger algoritmen som används för att signera token. Den `kid` anspråk anger den offentliga nyckeln som används för att signera token.

V2.0-slutpunkten loggar ID och åtkomst-token med hjälp av någon av en specifik uppsättning offentligt / privat nyckelpar. V2.0-slutpunkten roterar regelbundet möjliga uppsättningen med nycklar, så att din app ska skrivas till hanterar dessa viktiga ändringar automatiskt. En rimlig frekvens för att söka efter uppdateringar till de offentliga nycklar som används av v2.0-slutpunkten är 24 timmar.

Du kan hämta signering viktiga data som du behöver för att verifiera signaturen med hjälp av webbplatsen för OpenID Connect metadata som finns på:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Testa URL: en i en webbläsare!

Det här Metadatadokumentet är ett JSON-objekt som har flera användbara uppgifter, t.ex platsen för de olika slutpunkter som krävs för OpenID Connect-autentisering. Dokumentet innehåller också en *jwks_uri*, vilket ger platsen för en uppsättning offentliga nycklar som används för att signera token. JSON-dokument som finns på jwks_uri har alla offentlig nyckelinformation som används för närvarande. Din app kan använda den `kid` anspråk i JWT-huvudet för att välja vilka offentliga nyckeln i det här dokumentet har använts för att logga en token. Verifiera signaturen utför sedan genom att använda rätt offentlig nyckel och den angivna algoritmen.

> [!NOTE]
> Den `x5t` anspråk är inaktuell i v2.0-slutpunkten. Vi rekommenderar att du använder den `kid` anspråk som ska verifiera din token.

Utföra verifiera signaturen är utanför omfattningen för det här dokumentet. Många bibliotek med öppen källkod är tillgängliga som hjälper dig med detta.

### <a name="validate-the-claims"></a>Validera anspråk

När din app tar emot ett ID-token när användaren loggar in, bör det också utföra några kontroller mot anspråken i ID-token. Dessa inkludera, men är inte begränsade till:

* **publik** anspråk, kontrollera att ID-token ska tilldelas till din app
* **inte före** och **förfallotid** anspråk, så kontrollera att ID-token inte har gått ut
* **utfärdaren** anspråk för att verifiera att token har utfärdats till din app av v2.0-slutpunkten
* **nonce**, spela upp nätverksattacker som en token

En fullständig lista över anspråk verifieringar som din app ska utföra finns i den [OpenID Connect-specifikationen](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Information om de förväntade värdena för dessa anspråk som ingår i den [ID-token](# ID tokens) avsnittet.

## <a name="token-lifetimes"></a>Livslängder för token

Vi tillhandahåller följande tokenlivslängder endast i informationssyfte. Informationen kan hjälpa dig när du utvecklar och felsöker appar. Dina appar bör inte skrivas som händer något av dessa livslängd ska ändras. Token kan livslängd och kommer att ändras när som helst.

| Token | Livslängd | Beskrivning |
| --- | --- | --- |
| ID-token (arbets- eller skolkonto-konton) |1 timme |ID-token vanligtvis är giltiga för 1 timme. Din webbapp kan använda den här samma livslängd för att upprätthålla sin egen session med den användaren (rekommenderas) eller välja en helt annan session livslängd. Om din app behöver att hämta ett nytt ID-token, måste det att skapa en ny inloggning begäran till v2.0 tillåta slutpunkt. Om användaren har en giltig webbläsarsession med v2.0-slutpunkten kan behövas inte användaren att ange sina autentiseringsuppgifter på nytt. |
| ID-token (personliga konton) |24 timmar |ID-token för personliga konton vanligtvis är giltiga i 24 timmar. Din webbapp kan använda den här samma livslängd för att upprätthålla sin egen session med den användaren (rekommenderas) eller välja en helt annan session livslängd. Om din app behöver att hämta ett nytt ID-token, måste det att skapa en ny inloggning begäran till v2.0 tillåta slutpunkt. Om användaren har en giltig webbläsarsession med v2.0-slutpunkten kan behövas inte användaren att ange sina autentiseringsuppgifter på nytt. |
| Åtkomsttoken (arbets- eller skolkonto-konton) |1 timme |Visas i tokensvar som en del av token metadata. |
| Åtkomsttoken (personliga konton) |1 timme |Visas i tokensvar som en del av token metadata. Åtkomsttoken som utfärdas åt personliga konton kan konfigureras för en annan livslängd, men 1 timme är typiskt. |
| Uppdatera token (arbets- eller skolkonto konto) |Upp till 14 dagar |En enda uppdateringstoken är giltig för upp till 14 dagar. Uppdateringstoken kan dock bli ogiltiga när som helst av olika skäl, så att din app ska fortsätta att försöka använda en uppdateringstoken tills det misslyckas, eller tills appen ersätter den med en ny uppdateringstoken. En uppdateringstoken blir ogiltiga om det har gått 90 dagar sedan användaren har angett sina autentiseringsuppgifter. |
| Uppdatera token (personliga konton) |Upp till 1 år |En enda uppdateringstoken är giltig för högst 1 år. Uppdateringstoken kan dock bli ogiltiga när som helst av olika skäl, så att din app bör fortsätta att försöka använda en uppdateringstoken tills det misslyckas. |
| Auktoriseringskoder (arbets- eller skolkonto-konton) |10 minuter |Auktoriseringskoder är ändamålsenligt kortlivade och bör omedelbart lösas in för åtkomst-token och uppdateringstoken när token som tas emot. |
| Auktoriseringskoder (personliga konton) |5 minuter |Auktoriseringskoder är ändamålsenligt kortlivade och bör omedelbart lösas in för åtkomst-token och uppdateringstoken när token som tas emot. Auktoriseringskoder som har utfärdats för personliga konton är för enstaka användning. |
