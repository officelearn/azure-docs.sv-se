---
title: Azure Active Directory v2.0 tokens referens | Microsoft Docs
description: Typer av token och anspråk som sänds av Azure AD v2.0-slutpunkten
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4a408fb40c976c6e06f00d074504de6a3ec29bd1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Azure Active Directory v2.0 token-referens
Azure Active Directory (AD Azure) v2.0-slutpunkten genererar flera typer av säkerhetstoken i varje [autentiseringsflödet](active-directory-v2-flows.md). Den här referensen beskriver format, säkerhet egenskaperna och innehållet i varje typ av token.

> [!NOTE]
> V2.0-slutpunkten har inte stöd för alla Azure Active Directory-scenarier och funktioner. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>Typer av token
V2.0-slutpunkten stöder den [OAuth 2.0-protokollet för auktorisering](active-directory-v2-protocols.md), som använder token uppdatera token. V2.0-slutpunkten stöder också autentisering och logga in via [OpenID Connect](active-directory-v2-protocols.md). OpenID Connect introducerar en tredje typ av token ID-token. Var och en av dessa token representeras som en *ägar* token.

Ett ägartoken är en förenklad säkerhetstoken som ger ägar-åtkomst till en skyddad resurs. Innehavaren är någon part som kan ge token. Även om en part måste autentisera med Azure AD ta emot ägartoken om stegen inte vidtas för att skydda token under överföringen och lagringen, kan de snappas upp och används av en oavsiktlig part. Vissa säkerhetstoken har en inbyggd metod för att förhindra att obehöriga personer använder dem, men inte har ägar-token. Ägar-token måste transporteras i en säker kanal, till exempel transport layer security (HTTPS). Om ett ägartoken överförs utan den här typen av säkerhet, en skadlig part kan använda en ”man-in-the-middle attack” hämta token och använder den för obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper tillämpas när lagring eller cachelagring ägar-token för senare användning. Se alltid till att din app på ett säkert sätt överför och lagrar ägar-token. Mer säkerhet för ägar-token finns [RFC 6750 avsnitt 5](http://tools.ietf.org/html/rfc6750).

Många av de token som utfärdas av v2.0-slutpunkten implementeras som JSON Web token (JWTs). En JWT är ett kompakt, URL-säkert sätt att överföra information mellan två parter. Informationen i en JWT kallas en *anspråk*. Det är ett intyg om ägar- och ämnet för token. Anspråk i en JWT är JavaScript Object Notation (JSON)-objekt som är kodade och serialiseras för överföring. Eftersom JWTs som utfärdats av v2.0-slutpunkten är signerad men inte krypterad, kan du enkelt kontrollera innehållet i en JWT för felsökning. Mer information om JWTs finns i [JWT-specifikationen](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>ID-token
En ID-token är en typ av inloggning säkerhetstoken som appen tar emot när autentisering utförs med hjälp av [OpenID Connect](active-directory-v2-protocols.md). ID-token representeras som [JWTs](#types-of-tokens), och de innehåller anspråk som du kan använda för att logga in användaren till din app. Du kan använda anspråken i en ID-token på olika sätt. Normalt använder administratörer ID-token ska visa kontoinformation eller gör besluten om åtkomstkontroll i en app. V2.0-slutpunkten utfärdar bara en typ av ID-token som har en enhetlig uppsättning anspråk, oavsett vilken typ av användare som har loggat in. Formatet och innehållet för ID-token är desamma för användare med personliga Microsoft-konton och arbets-eller skolkonton.

ID-token är för närvarande är signerad men inte krypterad. När appen tar emot en ID-token, måste [verifiera signaturen](#validating-tokens) till token för autentisering och validera några anspråk i token för att bevisa sin giltighetsperiod. De anspråk som verifieras av en app varierar beroende på krav för scenarier, men din app måste utföra vissa [vanliga anspråk verifieringar](#validating-tokens) i varje scenario.

Vi ger dig fullständig information om anspråk ID-token i följande avsnitt, förutom en exempel-ID-token. Observera att anspråk i ID-token inte returneras i en viss ordning. Dessutom kan nya anspråk vara introduceras i ID-token när som helst. Din app bryter bör inte när nya anspråk som införs. Följande lista innehåller krav som din app för närvarande kan på ett tillförlitligt sätt tolka. Du hittar mer information finns i den [OpenID Connect specifikationen](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Exempel-ID-token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> Klistra in exempel ID-token till om du vill granska anspråk i exempel-ID-token för övningen [jwt.ms](http://jwt.ms/).
>
>

#### <a name="claims-in-id-tokens"></a>Anspråk i ID-token
| Namn | Begär | Exempelvärde | Beskrivning |
| --- | --- | --- | --- |
| målgrupp |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |Identifierar den avsedda mottagaren av token. I ID-token är den din app program-ID som tilldelats din app i portalen för registrering av Microsoft-program. Din app ska verifiera det här värdet och avvisa token om värdet inte matchar. |
| Utfärdaren |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Identifierar den säkerhetstokentjänst (STS) som skapar och återställer token och Azure AD-klient som användaren autentiserades. Appen bör verifiera utfärdaren anspråk så att token som kommer från v2.0-slutpunkten. Det bör också använda GUID-delen av anspråket för att begränsa antalet klienter som kan logga in i appen. Det GUID som anger att användaren är en konsument användare från ett Microsoft-konto är `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Utfärdat till |`iat` |`1452285331` |Den tid då token utfärdats representeras i epok tid. |
| Förfallotid |`exp` |`1452289231` |Den tid då token blir ogiltig representeras i epok tid. Din app ska använda detta anspråk för att kontrollera giltigheten för livslängd för token. |
| Inte före |`nbf` |`1452285331` |Den tid då token börjar gälla, representeras i epok tid. Det är vanligtvis samma som den utfärdande-tid. Din app ska använda detta anspråk för att kontrollera giltigheten för livslängd för token. |
| version |`ver` |`2.0` |Versionen av ID-token, som definieras av Azure AD. Värdet är för v2.0-slutpunkten `2.0`. |
| klientorganisations-ID |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Ett GUID som representerar Azure AD-klient som användaren är från. För arbets- och skolkonton konton är GUID oföränderliga klient-ID på den organisation som användaren tillhör. Värdet är för personliga konton `9188040d-6c67-4c5b-b112-36a304b66dad`. Den `profile` omfattning krävs för att ta emot denna begäran. |
| Koden hash |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Koden hash ingår i ID-token endast när ID-token utfärdas med en OAuth 2.0-auktoriseringskod. Den kan användas för att validera en Auktoriseringskoden är äkta. Mer information om hur du utför den här verifieringen finns i [OpenID Connect specifikationen](http://openid.net/specs/openid-connect-core-1_0.html). |
| Åtkomst-token hash |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Åtkomst-token hash ingår i ID tokens endast när ID-token utfärdas med en OAuth 2.0-åtkomsttoken. Den kan användas för att validera en åtkomst-token är äkta. Mer information om hur du utför den här verifieringen finns i [OpenID Connect specifikationen](http://openid.net/specs/openid-connect-core-1_0.html). |
| temporärt ID |`nonce` |`12345` |Temporärt ID är en strategi för att minimera token replay-attacker. Appen kan ange ett temporärt ID i en auktoriseringsbegäran om med hjälp av den `nonce` Frågeparametern. Värdet du anger i begäran har genererats i token ID `nonce` anspråk som ska ändras. Din app kan kontrollera värdet mot det värde som det anges på begäran, som associerar appens session med en specifik ID-token. Din app ska utföra den här verifieringen under verifieringsprocessen ID-token. |
| namn |`name` |`Babe Ruth` |Anspråk namn innehåller ett läsbart värde som identifierar föremål för token. Värdet är inte säkert att vara unika, är det föränderliga och den har utformats för att användas endast för visning. Den `profile` omfattning krävs för att ta emot denna begäran. |
| e-post |`email` |`thegreatbambino@nyy.onmicrosoft.com` |Den primära e-postadress som är associerade med användarkontot, om sådan finns. Värdet är föränderliga och kan ändras med tiden. Den `email` omfattning krävs för att ta emot denna begäran. |
| prioriterade användarnamn |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |Primära användarnamnet som representerar användaren i v2.0-slutpunkten. Det kan vara en e-postadress, telefonnummer och ett allmänt användarnamn utan angivet format. Värdet är föränderliga och kan ändras med tiden. Eftersom det föränderliga, får du inte använda det här värdet vid auktoriseringsbeslut. Den `profile` omfattning krävs för att ta emot denna begäran. |
| Ämne |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Den användare som token Assert information, till exempel användare av en app. Det här värdet är oföränderlig och kan inte tilldela om eller återanvänds. Den kan användas för att utföra auktoriseringskontroller på ett säkert sätt, till exempel när token som används för att komma åt en resurs och kan användas som en nyckel i databastabeller. Eftersom ämne är alltid finns i token att Azure AD-problem, bör du använda det här värdet i ett system med allmänna tillstånd. Ämne är dock en pairwise identifierare – det är unikt för ett visst program-ID.  Därför om en användare loggar in på två olika appar som använder två olika klient-ID: N, får de apparna som två olika värden för ämne anspråket.  Detta kan eller inte vara önskvärt beroende på dina krav arkitektur och sekretess. |
| Objekt-ID |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Oåterkalleliga identifierare för ett objekt i Microsoft identitetssystem, i det här fallet ett användarkonto.  Det kan också användas för att utföra auktoriseringskontroller på ett säkert sätt och som en nyckel i databastabeller. Detta ID identifierar användaren i program - två olika program som loggar in samma användare får samma värde i den `oid` anspråk.  Det innebär att den kan användas när du skapar frågor till Microsoft online services, till exempel Microsoft Graph.  Microsoft Graph returnerar detta ID som den `id` -egenskapen för ett givet användarkonto.  Eftersom den `oid` tillåter flera apparna att korrelera användare, den `profile` omfattning krävs för att ta emot denna begäran. Observera att om en användare finns i flera klienter kan användaren innehåller ett annat objekt-ID i varje klient - de anses vara olika konton, även om användaren loggar in på varje konto med samma autentiseringsuppgifter. |

### <a name="access-tokens"></a>Åtkomst-token

V2.0-slutpunkten kan appar från tredje part som har registrerats med Azure AD för att utfärda åtkomsttoken för skyddade resurser, till exempel webb-API: er. Mer information om hur du konfigurerar ett program att utfärda åtkomsttoken finns [hur du registrerar en app med v2.0-slutpunkten](active-directory-v2-app-registration.md). Vid registreringen programmet med v2.0-slutpunkten utvecklaren kan ange åtkomstnivåer, kallas **scope**, för vilka åtkomst token kan utfärdas. Till exempel den **calendars.read** räckvidden i Microsoft Graph API ger behörighet att läsa användarens kalender. När programmet tar emot en åtkomst-token från v2.0-slutpunkten, måste du verifiera token signatur, utfärdare, målgrupp, förfallotid och andra anspråk beroende på ditt scenario. 

När du begär en åtkomst-token från v2.0-slutpunkten returnerar metadata om åtkomsttoken för din app att använda även v2.0-slutpunkten. Informationen omfattar förfallotiden för åtkomst-token och scope som är giltig. Din app använder dessa metadata för att utföra intelligent cachelagring av åtkomsttoken utan att behöva öppna att parsa den åtkomst-token.

### <a name="refresh-tokens"></a>Uppdatera token
Uppdatera token är säkerhetstokens som din app kan använda för att få nya åtkomst-token i en OAuth 2.0-flödet. Din app kan använda uppdaterings-tokens för att uppnå långsiktiga åtkomst till resurser för en användares räkning utan interaktion med användaren.

Uppdaterings-tokens är flera resurs. En uppdateringstoken som togs emot under en Tokenbegäran för en resurs kan lösas för åtkomst-token till en helt annan resurs.

Om du vill få en uppdatering i en token svar din app måste begära och beviljas den `offline_acesss` omfång. Mer information om den `offline_access` omfång, finns i [medgivande och scope](active-directory-v2-scopes.md) artikel.

Uppdatera token är och alltid kommer att vara, helt ogenomskinlig till din app. De kan har utfärdats av Azure AD v2.0-slutpunkten och bara kontrolleras och tolkas av v2.0-slutpunkten. De är långlivade, men din app inte att skriva kan förvänta sig att en uppdateringstoken ska gälla för en viss tidsperiod. Uppdaterings-tokens kan vara inaktuella när som helst av olika skäl - mer information finns i [token återkallade](active-directory-token-and-claims.md#token-revocation). Det enda sättet för din app att veta om en uppdateringstoken är giltig är att försöka lösa genom att göra en tokenbegäran v2.0-slutpunkten.

När du lösa in en uppdateringstoken för en ny åtkomsttoken (och om din app har beviljats av `offline_access` omfattning), du får en ny uppdateringstoken i token svaret. Spara den nyligen utfärdade uppdateringstoken för att ersätta den som du använde i begäran. Detta garanterar att uppdaterings-tokens vara giltigt så länge som möjligt.

## <a name="validating-tokens"></a>Verifiera token
För närvarande verifieras endast token valideringen dina appar behöver utföra ID-token. Om du vill validera en ID-token bör appen verifiera både signatur för ID-token och anspråk i ID-token.

<!-- TODO: Link -->
Microsoft tillhandahåller bibliotek och kodexempel som visar hur du kan hantera token validering. I nästa avsnitt beskrivs underliggande processen. Flera från tredje part öppen källkod bibliotek är tillgängliga för JWT-verifiering. Det finns minst ett bibliotek alternativ för nästan alla plattformar och språk.

### <a name="validate-the-signature"></a>Verifiera signaturen
En JWT innehåller tre segment som avgränsas med den `.` tecken. Det första segmentet kallas den *huvud*, det andra segmentet är den *brödtext*, och det tredje segmentet är den *signatur*. Signaturen segment kan användas för att kontrollera äkthet ID-token så att den kan vara betrott av din app.

ID-token är signerade med branschstandard asymmetriska krypteringsalgoritmer, till exempel RSA-256. Rubriken på ID-token har information om metoden nyckel och kryptering används för att signera token. Exempel:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Den `alg` anspråk anger algoritmen som används för att signera token. Den `kid` anspråk anger den offentliga nyckeln som användes för att signera token.

När som helst kan v2.0-slutpunkten logga en ID-token med hjälp av någon av en specifik uppsättning privat-offentligt nyckelpar. V2.0-slutpunkten roterar regelbundet möjliga uppsättning nycklar, så att din app ska skrivas till att hantera dessa viktiga ändringar automatiskt. En rimlig frekvens för att söka efter uppdateringar för offentliga nycklar som används av v2.0-slutpunkten är 24 timmar.

Du kan skaffa signering viktiga data som behövs för att verifiera signaturen med OpenID Connect Metadatadokumentet finns på:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> URL-Adressen i en webbläsare kan du prova!
>
>

Metadatadokumentet är en JSON-objekt som har flera användbara delar av information, t.ex platsen för de olika slutpunkter som krävs för OpenID Connect-autentisering.  Dokumentet innehåller också en *jwks_uri*, vilket ger platsen för en uppsättning offentliga nycklar som används för att signera token. JSON-dokument som finns på jwks_uri har alla offentlig nyckelinformation som används för närvarande. Din app kan använda den `kid` anspråk i JWT-huvudet för att välja vilka offentliga nyckeln i det här dokumentet har använts för att logga en token. Signaturverifiering utför sedan genom att använda rätt offentlig nyckel och den angivna algoritmen.

Utför signaturverifiering ligger utanför omfånget för det här dokumentet. Det finns många bibliotek med öppen källkod som hjälper dig med detta.

### <a name="validate-the-claims"></a>Validera anspråk
När appen tar emot en ID-token när användaren loggar in, bör det också utföra några kontroller mot anspråk i ID-token. Dessa inkludera, men är inte begränsade till:

* **målgruppen** anspråk för att verifiera att ID-token är avsedd för att tilldelas till din app
* **inte före** och **förfallotid** anspråk för att verifiera att token ID inte har gått ut
* **utfärdaren** anspråk för att verifiera att token har utfärdats till din app av v2.0-slutpunkten
* **temporärt ID**, som en token replay-attacker lösning

En fullständig lista över anspråk verifieringar som din app ska utföra finns i [OpenID Connect specifikationen](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Information om de förväntade värdena för dessa anspråk som ingår i den [ID-token](# ID tokens) avsnitt.

## <a name="token-lifetimes"></a>Tokenlivslängder
Vi ger följande token livslängd endast i informationssyfte. Informationen kan hjälpa dig när du utvecklar och felsöka appar. Dina appar skrivs bör inte ser något av dessa livslängd förblir konstant. Token kan livslängd och kommer att ändras när som helst.

| Token | Livslängd | Beskrivning |
| --- | --- | --- |
| ID-token (arbets- eller skolkonto konton) |1 timme |ID-token är oftast giltigt för 1 timme. Ditt webbprogram kan använda den här samma livslängd för att upprätthålla sin egen session med användaren (rekommenderas) eller så kan du välja en helt annan session livslängd. Om din app måste hämta ett nytt ID-token, måste det att begära en ny inloggning till v2.0 godkänna endpoint. Om användaren har en giltig webbläsarsession med v2.0-slutpunkten kan behöva användaren inte ange sina autentiseringsuppgifter igen. |
| ID-token (personliga konton) |24 timmar |ID-token för personliga konton är oftast giltigt under 24 timmar. Ditt webbprogram kan använda den här samma livslängd för att upprätthålla sin egen session med användaren (rekommenderas) eller så kan du välja en helt annan session livslängd. Om din app måste hämta ett nytt ID-token, måste det att begära en ny inloggning till v2.0 godkänna endpoint. Om användaren har en giltig webbläsarsession med v2.0-slutpunkten kan behöva användaren inte ange sina autentiseringsuppgifter igen. |
| Åtkomst-token (arbets- eller skolkonto konton) |1 timme |Visas i token svar som en del av token metadata. |
| Åtkomst-token (personliga konton) |1 timme |Visas i token svar som en del av token metadata. Åtkomsttoken som utfärdas åt personliga konton kan konfigureras för ett annat livstid, men 1 timme är vanliga. |
| Uppdatera token (arbets- eller skolkonto konto) |Upp till 14 dagar |En enskild uppdateringstoken är giltig för 14 dagar. Uppdateringstoken som kan bli ogiltiga när som helst av olika skäl, så att din app ska fortsätta att försöka använda en uppdateringstoken förrän det misslyckas, eller din app ersätts med en ny uppdateringstoken. En uppdateringstoken blir ogiltig om det har gått 90 dagar sedan användaren har angett sina autentiseringsuppgifter. |
| Uppdatera token (personliga konton) |Upp till 1 år |En enskild uppdateringstoken är giltig för högst 1 år. Uppdateringstoken som kan bli ogiltiga när som helst av olika skäl, så att din app ska fortsätta att försöka använda en uppdateringstoken tills det misslyckas. |
| Auktoriseringskoder (arbets- eller skolkonto konton) |10 minuter |Auktoriseringskoder är ändamålsenligt tillfällig och bör omedelbart lösas för åtkomst-token och uppdateringstoken när token som tas emot. |
| Auktoriseringskoder (personliga konton) |5 minuter |Auktoriseringskoder är ändamålsenligt tillfällig och bör omedelbart lösas för åtkomst-token och uppdateringstoken när token som tas emot. Auktoriseringskoder som har utfärdats för personliga konton är för enstaka användning. |
