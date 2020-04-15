---
title: Referens för åtkomst till microsoft-identitetsplattform | Azure
description: Lär dig mer om åtkomsttoken som avges av slutpunkterna Azure AD v1.0 och Microsoft Identity Platform (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: ed583abc8f60f3d367bf75254807e3f28cd0f1c9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309714"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Åtkomsttoken för Microsoft-identitetsplattform

Åtkomsttoken gör det möjligt för klienter att anropa API:er som skyddas av Azure på ett säkert sätt. Microsoft identity platform access tokens är [JWTs](https://tools.ietf.org/html/rfc7519), Base64 kodade JSON-objekt signerade av Azure. Klienter bör behandla åtkomsttoken som ogenomskinliga strängar, eftersom innehållet i token endast är avsett för resursen. För validering och felsökning kan utvecklare avkoda JWTs med hjälp av en webbplats som [jwt.ms](https://jwt.ms). Klienten kan få en åtkomsttoken från slutpunkten v1.0 eller v2.0 med hjälp av en mängd olika protokoll.

När klienten begär en åtkomsttoken returnerar Azure AD även vissa metadata om åtkomsttoken för appens förbrukning. Den här informationen omfattar utgångstiden för åtkomsttoken och de scope som den är giltig för. Dessa data gör att din app kan göra intelligent cachelagring av åtkomsttoken utan att behöva tolka själva åtkomsttoken.

Om ditt program är en resurs (webb-API) som klienter kan begära åtkomst till, ger åtkomsttoken användbar information för användning i autentisering och auktorisering, till exempel användare, klient, utfärdare, behörigheter med mera.

Se följande avsnitt för att lära dig hur en resurs kan validera och använda anspråken i en åtkomsttoken.

> [!IMPORTANT]
> Åtkomsttoken skapas baserat på *målgruppen* för token, vilket innebär att programmet som äger scope i token.  Så här gör `accessTokenAcceptedVersion` en resursinställning `2` i [appmanifestet](reference-app-manifest.md#manifest-reference) att en klient som anropar v1.0-slutpunkten kan ta emot en v2.0-åtkomsttoken.  På samma sätt är det därför som ändring av [valfria anspråk](active-directory-optional-claims.md) för åtkomsttoken `user.read`för klienten inte ändrar åtkomsttoken som tas emot när en token begärs för , som ägs av resursen.
> Av samma anledning kan det hända att åtkomsttoken som tas emot av klienten är en ogenomskinlig sträng när du testar klientprogrammet med ett personligt konto (till exempel hotmail.com eller outlook.com). Detta beror på att den resurs som används har begärt äldre MSA-biljetter (Microsoft-konto) som är krypterade och inte kan förstås av klienten.

## <a name="sample-tokens"></a>Exempeltoken

v1.0 och v2.0 tokens ser likadana ut och innehåller många av samma anspråk. Ett exempel på varje ges här.

### <a name="v10"></a>V1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Visa den här v1.0-token i [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Visa den här v2.0-token i [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Anspråk i åtkomsttoken

JWTs är uppdelade i tre delar:

* **Header** - Ger information om hur du [validerar token](#validating-tokens) inklusive information om typen av token och hur den signerades.
* **Nyttolast** – Innehåller alla viktiga data om användaren eller appen som försöker ringa din tjänst.
* **Signatur** - Används råmaterialet för att validera token.

Varje del avgränsas med`.`en punkt ( ) och separat Base64 kodad.

Anspråk finns bara om det finns ett värde för att fylla det. Appen bör därför inte vara beroende av att det finns ett anspråk. Exempel `pwd_exp` är (inte alla klienter kräver `family_name` lösenord för att upphöra att gälla) eller (klientautentiseringsuppgifter ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) flöden är på uppdrag av program, som inte har namn). Anspråk som används för validering av åtkomsttoken kommer alltid att finnas.

> [!NOTE]
> Vissa anspråk används för att hjälpa Azure AD-säkra token vid återanvändning. Dessa markeras som inte för offentlig konsumtion i beskrivningen som "Ogenomskinlig". Dessa anspråk kan visas i en token eller inte, och nya kan läggas till utan föregående meddelande.

### <a name="header-claims"></a>Huvudanspråk

|Begär | Format | Beskrivning |
|--------|--------|-------------|
| `typ` | String - alltid "JWT" | Anger att token är en JWT.|
| `nonce` | Sträng | En unik identifierare som används för att skydda mot token reprisattacker. Din resurs kan spela in det här värdet för att skydda mot repriser. |
| `alg` | Sträng | Anger algoritmen som användes för att signera token, till exempel "RS256" |
| `kid` | Sträng | Anger tumavtrycket för den offentliga nyckel som används för att signera den här token. Avges i både v1.0 och v2.0-åtkomsttoken. |
| `x5t` | Sträng | Fungerar på samma sätt (används `kid`och värde) som . `x5t`är ett äldre anspråk som endast släpps ut i v1.0-åtkomsttoken för kompatibilitetsändamål. |

### <a name="payload-claims"></a>Anspråk på nyttolast

| Begär | Format | Beskrivning |
|-----|--------|-------------|
| `aud` | Sträng, ett app-ID URI | Identifierar den avsedda mottagaren av token. I ID-token är målgruppen appens program-ID som tilldelas din app i Azure-portalen. Din app ska validera det här värdet och avvisa token om värdet inte matchar. |
| `iss` | Sträng, en STS URI | Identifierar säkerhetstokentjänsten (STS) som konstruerar och returnerar token och Azure AD-klienten där användaren autentiserades. Om token som utfärdas är en v2.0-token (se anspråket) `ver` slutar URI i `/v2.0`. GUID som anger att användaren är en konsumentanvändare `9188040d-6c67-4c5b-b112-36a304b66dad`från ett Microsoft-konto är . Din app bör använda GUID-delen av anspråket för att begränsa den uppsättning klienter som kan logga in på appen, om tillämpligt. |
|`idp`| Sträng, vanligtvis en STS URI | Registrerar den identitetsprovider som har autentiserat subjektet för token. Det här värdet är identiskt med värdet på Utfärdaranspråket om inte användarkontot inte finns i samma klient som utfärdaren - till exempel gäster. Om anspråket inte finns betyder det att `iss` värdet på kan användas i stället.  För personliga konton som används i en organisationskontext (till exempel ett `idp` personligt konto som bjudits in till en Azure AD-klient) kan anspråket vara "live.com" eller en STS URI som innehåller Microsoft-kontoklienten `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| `iat` | int, en UNIX-tidsstämpel | "Utfärdat vid" anger när autentiseringen för den här token inträffade. |
| `nbf` | int, en UNIX-tidsstämpel | I påståendet "nbf" (ej före) anges den tid före vilken JWT inte får godkännas för behandling. |
| `exp` | int, en UNIX-tidsstämpel | Påståendet "exp" (utgångstid) identifierar utgångstiden på eller efter vilken JWT inte får accepteras för bearbetning. Det är viktigt att notera att en resurs kan avvisa token före denna tid också, till exempel när en ändring i autentisering krävs eller en tokenåterkallning har upptäckts. |
| `aio` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure AD för att registrera data för återanvändning av token. Resurser bör inte använda det här anspråket. |
| `acr` | Sträng, "0" eller "1" | Finns endast i v1.0-tokens. Anspråket "Autentiseringskontextklass". Värdet "0" anger att slutanvändarens autentisering inte uppfyllde kraven i ISO/IEC 29115. |
| `amr` | JSON-matris med strängar | Finns endast i v1.0-tokens. Identifierar hur ämnet för token autentiserades. Se [avsnittet amr anspråk](#the-amr-claim) för mer information. |
| `appid` | Sträng, ett GUID | Finns endast i v1.0-tokens. Program-ID för klienten med hjälp av token. Programmet kan fungera som sig själv eller för en användares räkning. Program-ID:t representerar vanligtvis ett programobjekt, men det kan också representera ett huvudobjekt för tjänsten i Azure AD. |
| `appidacr` | "0", "1" eller "2" | Finns endast i v1.0-tokens. Anger hur klienten autentiserades. För en offentlig klient är värdet "0". Om klient-ID och klienthemlighet används är värdet "1". Om ett klientcertifikat användes för autentisering är värdet "2". |
| `azp` | Sträng, ett GUID | Endast finns i v2.0 tokens, en ersättning för `appid`. Program-ID för klienten med hjälp av token. Programmet kan fungera som sig själv eller för en användares räkning. Program-ID:t representerar vanligtvis ett programobjekt, men det kan också representera ett huvudobjekt för tjänsten i Azure AD. |
| `azpacr` | "0", "1" eller "2" | Endast finns i v2.0 tokens, en ersättning för `appidacr`. Anger hur klienten autentiserades. För en offentlig klient är värdet "0". Om klient-ID och klienthemlighet används är värdet "1". Om ett klientcertifikat användes för autentisering är värdet "2". |
| `preferred_username` | Sträng | Det primära användarnamnet som representerar användaren. Det kan vara en e-postadress, ett telefonnummer eller ett allmänt användarnamn utan ett angivet format. Dess värde är föränderligt och kan ändras med tiden. Eftersom det är föränderligt får det här värdet inte användas för att fatta auktoriseringsbeslut.  Den kan användas för användarnamn tips ändå. Omfattningen `profile` krävs för att få detta anspråk. |
| `name` | Sträng | Ger ett läsbart värde som identifierar ämnet för token. Värdet är inte garanterat att vara unikt, det är föränderligt och det är utformat för att användas endast för visningsändamål. Omfattningen `profile` krävs för att få detta anspråk. |
| `scp` | String, en utrymmesavskiljad lista över scope | Den uppsättning scope som exponeras av ditt program som klientprogrammet har begärt (och tagit emot) medgivande för. Appen bör kontrollera att dessa scope är giltiga som exponeras av din app och fatta auktoriseringsbeslut baserat på värdet av dessa scope. Endast inkluderad för [användartoken .](#user-and-application-tokens) |
| `roles` | Matris med strängar, en lista med behörigheter | Den uppsättning behörigheter som exponeras av ditt program som det begärande programmet eller användaren har fått tillstånd att ringa. För [programtoken](#user-and-application-tokens)används detta under klientautentiseringsflödet ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) i stället för användaromfattningar.  För [användartoken fylls](#user-and-application-tokens) detta med de roller som användaren tilldelades i målprogrammet. |
| `wids` | Matris med [RollTemplateID GUIDs](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) | Betecknar de klientomfattande roller som tilldelats den här användaren, från det avsnitt av roller som finns på [sidan administratörsroller](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids).  Det här anspråket konfigureras per program, `groupMembershipClaims` via egenskapen för [programmanifestet](reference-app-manifest.md).  Ställa in den på "Alla" eller "DirectoryRole" krävs.  Kanske inte finns i token som erhållits via det implicita flödet på grund av tokenlängdsproblem. |
| `groups` | JSON-matris av GUIDs | Tillhandahåller objekt-ID:er som representerar ämnets gruppmedlemskap. Dessa värden är unika (se Object ID) och kan användas på ett säkert sätt för att hantera åtkomst, till exempel tvinga tillstånd att komma åt en resurs. De grupper som ingår i gruppanspråket konfigureras per `groupMembershipClaims` program, via egenskapen [för programmanifestet](reference-app-manifest.md). Värdet null utesluter alla grupper, värdet "SecurityGroup" innehåller endast Medlemskap i Active Directory Security Group och värdet "Alla" innehåller både säkerhetsgrupper och Distributionslistor för Office 365. <br><br>Se `hasgroups` anspråket nedan för `groups` information om hur du använder anspråket med det implicita bidraget. <br>Om antalet grupper som användaren befinner sig i överskrider en gräns (150 för SAML, 200 för JWT) läggs ett överkörningsanspråk till i anspråkskällorna som pekar på Microsoft Graph-slutpunkten som innehåller listan över grupper för användaren. |
| `hasgroups` | Boolesk | Om det `true`finns, alltid , betecknar användaren är i minst en grupp. Används i stället `groups` för anspråket på gemensamma tillsynsgrupper i implicita bidragsflöden om det fullständiga gruppanspråket skulle utöka URI-fragmentet utöver url-längdgränserna (för närvarande 6 eller fler grupper). Anger att klienten ska använda Microsoft Graph API för`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`att fastställa användarens grupper ( ). |
| `groups:src1` | JSON-objekt | För tokenbegäranden som inte `hasgroups` är begränsade längden (se ovan) men fortfarande för stora för token, inkluderas en länk till den fullständiga grupplistan för användaren. För gemensamma tillsynsmaskiner som en utdelad fordran, för `groups` SAML som en ny fordran i stället för fordran. <br><br>**Exempel JWT Värde:** <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | Sträng, ett GUID | Huvudbeloppet som token bekräftar information om, till exempel användaren av en app. Det här värdet är oföränderligt och kan inte tilldelas om eller återanvändas. Den kan användas för att utföra auktoriseringskontroller på ett säkert sätt, till exempel när token används för att komma åt en resurs och kan användas som en nyckel i databastabeller. Eftersom ämnet alltid finns i de token som Azure AD utfärdar rekommenderar vi att du använder det här värdet i ett allmänt auktoriseringssystem. Ämnet är dock en parvis identifierare - den är unik för ett visst program-ID. Om en enskild användare loggar in på två olika appar med två olika klient-ID:er får dessa appar därför två olika värden för ämnesanspråket. Detta kanske eller kanske inte önskas beroende på din arkitektur och sekretesskrav. Se även `oid` anspråket (som förblir detsamma i appar i en klientorganisation). |
| `oid` | Sträng, ett GUID | Den oföränderliga identifieraren för ett objekt i Microsofts identitetsplattform, i det här fallet ett användarkonto. Den kan också användas för att utföra auktoriseringskontroller på ett säkert sätt och som en nyckel i databastabeller. Det här ID:t identifierar användaren unikt i alla program – två olika `oid` program som loggar in samma användare får samma värde i anspråket. Således `oid` kan användas när du gör frågor till Microsoft online-tjänster, till exempel Microsoft Graph. Microsoft Graph returnerar detta ID som egenskap för `id` ett visst [användarkonto](/graph/api/resources/user). Eftersom `oid` flera appar kan korrelera `profile` användare krävs omfånget för att ta emot det här anspråket. Observera att om en enskild användare finns i flera klienter, kommer användaren att innehålla ett annat objekt-ID i varje klient - de betraktas som olika konton, även om användaren loggar in på varje konto med samma autentiseringsuppgifter. |
| `tid` | Sträng, ett GUID | Representerar Azure AD-klienten som användaren kommer från. För arbets- och skolkonton är GUID det oföränderliga klient-ID:et för den organisation som användaren tillhör. För personliga konton är `9188040d-6c67-4c5b-b112-36a304b66dad`värdet . Omfattningen `profile` krävs för att få detta anspråk. |
| `unique_name` | Sträng | Finns endast i v1.0-tokens. Innehåller ett läsbart värde som identifierar subjektet för token. Det här värdet är inte garanterat att vara unikt inom en klient och bör endast användas för visningsändamål. |
| `uti` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure för att förnya token. Resurser bör inte använda det här anspråket. |
| `rh` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure för att förnya token. Resurser bör inte använda det här anspråket. |
| `ver` | Sträng, `1.0` antingen eller`2.0` | Anger versionen av åtkomsttoken. |

**Anspråk på övermatning av grupper**

Azure AD begränsar antalet objekt-ID:er som den innehåller i gruppanspråket för att säkerställa att tokenstorleken inte överskrider HTTP-huvudstorleksbegränsningar. Om en användare är medlem i fler grupper än överkörningsgränsen (150 för SAML-token, 200 för JWT-token) avger Azure AD inte gruppanspråket i token. I stället innehåller den ett överkörningsanspråk i token som anger för programmet att fråga Microsoft Graph API för att hämta användarens gruppmedlemskap.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
 }
 ```

Du kan `BulkCreateGroups.ps1` använda den som finns i mappen App Creation Scripts för att testa överanvändningsscenarier. [App Creation Scripts](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts)

#### <a name="v10-basic-claims"></a>v1.0 grundläggande fordringar

Följande anspråk inkluderas i v1.0-token om det är tillämpligt, men ingår inte i v2.0-token som standard. Om du använder v2.0 och behöver något av dessa anspråk kan du begära dem med [valfria anspråk](active-directory-optional-claims.md).

| Begär | Format | Beskrivning |
|-----|--------|-------------|
| `ipaddr`| Sträng | IP-adressen som användaren autentiserade från. |
| `onprem_sid`| Sträng, i [SID-format](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | I de fall där användaren har en lokal autentisering tillhandahåller det här anspråket deras SID. Du kan `onprem_sid` använda för auktorisering i äldre program.|
| `pwd_exp`| int, en UNIX-tidsstämpel | Anger när användarens lösenord upphör att gälla. |
| `pwd_url`| Sträng | En URL där användare kan skickas för att återställa sitt lösenord. |
| `in_corp`| boolean | Signalerar om klienten loggar in från företagsnätverket. Om de inte är det ingår inte anspråket. |
| `nickname`| Sträng | Ytterligare ett namn för användaren, separat från för- eller efternamn.|
| `family_name` | Sträng | Anger användarens efternamn, efternamn eller efternamn enligt definitionen i användarobjektet. |
| `given_name` | Sträng | Anger användarens för- eller förnamn enligt användarobjektet. |
| `upn` | Sträng | Användarens användarnamn. Kan vara ett telefonnummer, e-postadress eller oformaterad sträng. Bör endast användas för visningsändamål och ge användarnamn tips i reauthentication scenarier. |

#### <a name="the-amr-claim"></a>Påståendet `amr`

Microsoft-identiteter kan autentiseras på olika sätt, vilket kan vara relevant för ditt program. Anspråket `amr` är en matris som kan `["mfa", "rsa", "pwd"]`innehålla flera objekt, till exempel för en autentisering som använde både ett lösenord och autentiseringsappen.

| Värde | Beskrivning |
|-----|-------------|
| `pwd` | Lösenordsautentisering, antingen en användares Microsoft-lösenord eller en apps klienthemlighet. |
| `rsa` | Autentiseringen baserades på bevis på en RSA-nyckel, till exempel med [Microsoft Authenticator-appen](https://aka.ms/AA2kvvu). Detta inkluderar om autentiseringen utfördes av en självsignerad JWT med ett tjänsteägt X509-certifikat. |
| `otp` | Engångslösenord med hjälp av ett e-postmeddelande eller ett sms. |
| `fed` | Ett federerat autentiseringspåstående (till exempel JWT eller SAML) användes. |
| `wia` | Windows-integrerad autentisering |
| `mfa` | Multifaktorautentisering användes. När detta finns kommer även andra autentiseringsmetoder att inkluderas. |
| `ngcmfa` | Motsvarar `mfa`, används för etablering av vissa avancerade autentiseringsuppgifter typer. |
| `wiaormfa`| Användaren använde Windows eller en MFA-autentiseringsuppgifter för att autentisera. |
| `none` | Ingen autentisering gjordes. |

## <a name="validating-tokens"></a>Validera token

Om du vill validera en id_token eller en access_token bör appen validera både tokens signatur och anspråken. För att validera åtkomsttoken bör din app också validera utfärdaren, målgruppen och signeringstoken. Dessa måste valideras mot värdena i OpenID-identifieringsdokumentet. Den klientoberoende versionen av dokumentet finns [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)till exempel på .

Azure AD-mellanprogram har inbyggda funktioner för att validera åtkomsttoken, och du kan bläddra igenom våra [exempel](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) för att hitta en på det språk du väljer.

Vi tillhandahåller bibliotek och kodexempel som visar hur du hanterar tokenvalidering. Nedanstående information ges till dem som vill förstå den underliggande processen. Det finns också flera tredjepartsbibliotek med öppen källkod tillgängliga för JWT-validering - det finns minst ett alternativ för nästan varje plattform och språk där ute. Mer information om Azure AD-autentiseringsbibliotek och kodexempel finns i [v1.0-autentiseringsbibliotek](../azuread-dev/active-directory-authentication-libraries.md) och [v2.0-autentiseringsbibliotek](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Validera signaturen

En JVT innehåller tre segment som avgränsas med tecknet. `.` Det första segmentet kallas **rubrik**, den andra som **brödtext**och det tredje som **signaturen**. Signatursegmentet kan användas för att verifiera äktheten av token så att den kan lita på din app.

Token som utfärdas av Azure AD signeras med asymmetriska krypteringsalgoritmer av branschstandard, till exempel RS256. Huvudet på JWT innehåller information om nyckel- och krypteringsmetoden som används för att signera token:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

Anspråket `alg` anger algoritmen som användes för att `kid` signera token, medan anspråket anger den specifika offentliga nyckeln som användes för att validera token.

Vid en viss tidpunkt kan Azure AD signera en id_token med någon av en viss uppsättning offentliga-privata nyckelpar. Azure AD roterar den möjliga uppsättningen nycklar regelbundet, så att din app ska skrivas för att hantera dessa nyckeländringar automatiskt. En rimlig frekvens för att söka efter uppdateringar av de offentliga nycklar som används av Azure AD är var 24:e timme.

Du kan hämta de signeringsnyckeldata som krävs för att validera signaturen med hjälp av [metadatadokumentet OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) som finns på:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Prova den här [webbadressen](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) i en webbläsare!

Detta metadatadokument:

* Är ett JSON-objekt som innehåller flera användbara informationsdelar, till exempel platsen för de olika slutpunkter som krävs för att utföra OpenID Connect-autentisering.
* Innehåller `jwks_uri`en , som anger platsen för den uppsättning offentliga nycklar som används för att signera token. JSON Web Key (JWK) `jwks_uri` som finns vid innehåller all offentlig viktig information som används vid den aktuella tidpunkten.  JVM-formatet beskrivs i [RFC 7517](https://tools.ietf.org/html/rfc7517).  Din app kan `kid` använda anspråket i JWT-huvudet för att välja vilken offentlig nyckel i det här dokumentet som har använts för att signera en viss token. Det kan sedan göra signatur validering med rätt offentlig nyckel och den angivna algoritmen.

> [!NOTE]
> V1.0-slutpunkten returnerar `x5t` `kid` både och anspråk, medan v2.0-slutpunkten svarar med endast anspråket. `kid` Framöver rekommenderar vi att `kid` du använder anspråket för att validera din token.

Att göra signaturvalidering ligger utanför det här dokumentets omfattning - det finns många bibliotek med öppen källkod tillgängliga för att hjälpa dig att göra det om det behövs.  Microsoft Identity-plattformen har dock ett tillägg för tokensignering till standarderna – anpassade signeringsnycklar.

Om appen har anpassade signeringsnycklar som ett resultat av att `appid` du använder funktionen [för anspråksmappning](active-directory-claims-mapping.md) måste du lägga till en frågeparameter som innehåller app-ID:et för att få en `jwks_uri` pekar på appens information om signeringsnyckeln, som ska användas för validering. Till exempel: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` innehåller `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`en av .

### <a name="claims-based-authorization"></a>Anspråksbaserad auktorisering

Programmets affärslogik kommer att diktera det här steget, några vanliga auktoriseringsmetoder anges nedan.

* Kontrollera `scp` eller `roles` anspråk för att kontrollera att alla aktuella scope matchar dem som exponeras av ditt API och tillåter klienten att utföra den begärda åtgärden.
* Se till att den anropande klienten tillåts anropa ditt API med anspråket. `appid`
* Verifiera autentiseringsstatusen för `appidacr` den anropande klienten med - det bör inte vara 0 om offentliga klienter inte tillåts anropa ditt API.
* Kontrollera mot en `nonce` lista över tidigare anspråk för att verifiera att token inte spelas upp igen.
* Kontrollera att `tid` matchar en klient som tillåts anropa ditt API.
* Använd `acr` anspråket för att verifiera att användaren har utfört MFA. Detta bör verkställas med [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Om du har `roles` begärt `groups` eller anspråk i åtkomsttoken kontrollerar du att användaren finns i den grupp som tillåts utföra den här åtgärden.
  * För token som hämtas med det implicita flödet måste du förmodligen fråga [Microsoft Graph](https://developer.microsoft.com/graph/) för dessa data, eftersom det ofta är för stort för att passa i token.

## <a name="user-and-application-tokens"></a>Användar- och programtoken

Ditt program kan ta emot token för en användares räkning (det vanliga flödet) eller direkt från ett program (via klientautentiseringsflödet ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)). Dessa app-bara token indikerar att det här samtalet kommer från ett program och inte har en användare som stöder det. Dessa tokens hanteras i stort sett samma, med vissa skillnader:

* Endast apptokens har `scp` inget anspråk och kan `roles` i stället ha ett anspråk. Det är här programbehörighet (i motsats till delegerade behörigheter) kommer att registreras. Mer information om delegerade behörigheter och programbehörigheter finns i behörighet och medgivande ([v1.0](../azuread-dev/v1-permissions-consent.md), [v2.0](v2-permissions-and-consent.md)).
* Många mänskliga-specifika påståenden kommer att `name` `upn`saknas, till exempel eller .
* Och `sub` `oid` fordringar kommer att vara desamma.

## <a name="token-revocation"></a>Återkalla token

Uppdateringstoken kan ogiltigförklaras eller återkallas när som helst, av olika skäl. Dessa kan delas in i två huvudkategorier: timeout och återkallelser.

### <a name="token-timeouts"></a>Tidsutgångar för token

Med hjälp av [tokenlivstidskonfiguration](active-directory-configurable-token-lifetimes.md)kan livslängden för uppdateringstoken ändras.  Det är normalt och förväntas för vissa tokens att gå utan användning (t.ex. användaren inte öppnar appen i 3 månader) och därför löper ut.  Appar kommer att stöta på scenarier där inloggningsservern avvisar en uppdateringstoken på grund av dess ålder. 

* MaxInactiveTime: Om uppdateringstoken inte har använts inom den tid som dikteras av MaxInactiveTime, kommer uppdateringstoken inte längre att vara giltig.
* MaxSessionAge: Om MaxAgeSessionMultiFactor eller MaxAgeSessionSingleFactor har ställts in på något annat än standard (Tills återkallats) krävs omautentisering efter den tid som angetts i MaxAgeSession* förfluten.
* Exempel:
  * Klienten har en MaxInactiveTime på fem dagar och användaren åkte på semester i en vecka och därför har Azure AD inte sett en ny tokenbegäran från användaren på 7 dagar. Nästa gång användaren begär en ny token hittar de att deras uppdateringstoken har återkallats och de måste ange sina autentiseringsuppgifter igen.
  * Ett känsligt program har en MaxAgeSessionSingleFactor på en dag. Om en användare loggar in på måndag och på tisdag (efter 25 timmar har förflutit) måste han eller hon autentisering.

### <a name="revocation"></a>Återkallande

Uppdatera token kan återkallas av servern på grund av en ändring av autentiseringsuppgifter, eller på grund av användning eller administratörsåtgärd.  Uppdatera token delas in i två klasser - de som utfärdas till konfidentiella klienter (kolumnen längst till höger) och de som utfärdas till offentliga klienter (alla andra kolumner).   

|   | Lösenordsbaserad cookie | Lösenordsbaserad token | Icke-lösenordsbaserad cookie | Token som inte är lösenordsbaserad | Konfidentiell klienttoken |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Lösenordet upphör att gälla | Håller sig vid liv | Håller sig vid liv | Håller sig vid liv | Håller sig vid liv | Håller sig vid liv |
| Lösenord ändrat av användaren | Revoked | Revoked | Håller sig vid liv | Håller sig vid liv | Håller sig vid liv |
| Användaren gör SSPR | Revoked | Revoked | Håller sig vid liv | Håller sig vid liv | Håller sig vid liv |
| Administratör återställer lösenord | Revoked | Revoked | Håller sig vid liv | Håller sig vid liv | Håller sig vid liv |
| Användaren återkallar sina uppdateringstoken [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revoked | Revoked | Revoked | Revoked | Revoked |
| Admin återkallar alla uppdateringstoken för en användare [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revoked | Revoked |Revoked | Revoked | Revoked |
| Enkel utloggning ([v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v2.0](v2-protocols-oidc.md#single-sign-out) ) på webben | Revoked | Håller sig vid liv | Revoked | Håller sig vid liv | Håller sig vid liv |

> [!NOTE]
> En "Icke-lösenordsbaserad" inloggning är en där användaren inte skrev in ett lösenord för att få det. Om du till exempel använder ansiktet med Windows Hello, en FIDO2-nyckel eller en PIN-kod.
>
> Primära uppdateringstoken (PRT) på Windows 10 är segregerade baserat på autentiseringsuppgifterna. Windows Hello och lösenord har till exempel sina respektive PRT:er, isolerade från varandra. När en användare loggar in med en Hello-autentiseringsuppgifter (PIN-kod eller biometri) och sedan ändrar lösenordet, kommer den lösenordsbaserade PRT som erhållits tidigare att återkallas. Om du loggar in igen med ett lösenord ogiltigförklaras den gamla PRT:n och en ny begärs.
>
> Uppdateringstoken är inte ogiltiga eller återkallade när de används för att hämta en ny åtkomsttoken och uppdatera token.  Appen ska dock ignorera den gamla så fort den används och ersätta den med den nya, eftersom den nya token har en ny förfallotid i den. 

## <a name="next-steps"></a>Nästa steg

* Lär [ `id_tokens` ](id-tokens.md)dig mer om i Azure AD .
* Läs mer om behörighet och samtycke ( [v1.0](../azuread-dev/v1-permissions-consent.md), [v2.0](v2-permissions-and-consent.md)).
