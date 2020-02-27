---
title: Referens för Microsoft Identity Platform-åtkomsttoken | Azure
description: Läs om åtkomsttoken som har spridits av Azure AD v 1.0 och Microsoft Identity Platform (v 2.0) slut punkter.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 06a3ef7677d52ebb0d835dfed2f47fc66870f0ec
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620888"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Åtkomsttoken för Microsoft Identity Platform

Åtkomsttoken gör att klienter säkert kan anropa API: er som skyddas av Azure. Microsoft Identity Platform-åtkomsttoken är [JWTs](https://tools.ietf.org/html/rfc7519), base64-KODAde JSON-objekt som signerats av Azure. Klienter ska behandla åtkomsttoken som täckande strängar, eftersom innehållet i token endast är avsett för resursen. I validerings-och fel söknings syfte kan utvecklare avkoda JWTs med en plats som [JWT.MS](https://jwt.ms). Klienten kan få en åtkomsttoken från antingen v 1.0-slutpunkten eller v 2.0-slutpunkten med en mängd olika protokoll.

När klienten begär en åtkomsttoken, returnerar Azure AD även vissa metadata om åtkomsttoken för appens förbrukning. Den här informationen omfattar förfallo tiden för åtkomsttoken och de omfattningar som den är giltig för. Med den här informationen kan din app utföra intelligent cachelagring av åtkomsttoken utan att behöva parsa åtkomsttoken.

Om ditt program är en resurs (webb-API) som klienter kan begära åtkomst till, ger åtkomst till tokens värdefull information för användning vid autentisering och auktorisering, till exempel användare, klient, utfärdare, behörigheter med mera.

I följande avsnitt får du lära dig hur en resurs kan verifiera och använda anspråk i en åtkomsttoken.

> [!IMPORTANT]
> Åtkomsttoken skapas baserat på token för token, vilket *innebär det program* som äger omfattningarna i token.  Detta är hur en resurs inställning `accessTokenAcceptedVersion` i [appens manifest](reference-app-manifest.md#manifest-reference) till `2` tillåter att en klient som anropar en v 1.0-slutpunkt tar emot en v 2.0-åtkomsttoken.  På samma sätt är det anledningen till att ändra de [valfria anspråken](active-directory-optional-claims.md) för åtkomsttoken för din klient ändrar inte den åtkomsttoken som tas emot när en token begärs för `user.read`, som ägs av MS Graph-resursen.  
> Av samma anledning kan du, när du testar ditt klient program med ett personligt konto (t. ex. hotmail.com eller outlook.com), se till att åtkomsttoken som tas emot av klienten är en ogenomskinlig sträng. Detta beror på att den resurs som används har begärt att äldre MSA-biljetter (Microsoft-konto) ska vara krypterade och inte kan tolkas av klienten.

## <a name="sample-tokens"></a>Exempel-token

v 1.0-och v 2.0-token ser likadana ut och innehåller många av samma anspråk. Ett exempel på detta finns här.

### <a name="v10"></a>V1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Visa den här v 1.0-token i [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Visa den här v 2.0-token i [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Anspråk i åtkomsttoken

JWTs delas upp i tre delar:

* **Header** – innehåller information om hur du [validerar token](#validating-tokens) inklusive information om typen av token och hur den signerades.
* **Nytto Last** – innehåller alla viktiga data om användaren eller appen som försöker anropa tjänsten.
* **Signatur** – är det råmaterial som används för att validera token.

Varje del är avgränsat med en punkt (`.`) och en separat Base64-kodad.

Anspråk finns bara om det finns ett värde för att fylla det. Därför bör appen inte ta ett beroende på ett anspråk som finns. Exempel på detta är `pwd_exp` (inte varje klient kräver att lösen ord upphör att gälla) eller `family_name` (klient autentiseringsuppgiften ([v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) flöden är för programs räkning, som inte har namn). Anspråk som används för verifiering av åtkomst-token är alltid tillgängliga.

> [!NOTE]
> Vissa anspråk används för att hjälpa Azure AD-säkra tokens i händelse av åter användning. Dessa markeras som icke-offentliga konsumtion i beskrivningen som "täckande". Dessa anspråk kan komma att visas i en token, och nya kan läggas till utan föregående meddelande.

### <a name="header-claims"></a>Huvud anspråk

|Begär | Format | Beskrivning |
|--------|--------|-------------|
| `typ` | Sträng-Always-JWT | Anger att token är en JWT.|
| `nonce` | Sträng | En unik identifierare som används för att skydda mot repetitions attacker med token. Din resurs kan registrera det här värdet för att skydda mot uppspelningar. |
| `alg` | Sträng | Anger algoritmen som användes för att signera token, till exempel "RS256" |
| `kid` | Sträng | Anger tumavtrycket för den offentliga nyckel som används för att signera denna token. Genereras i både v 1.0-och v 2.0-åtkomsttoken. |
| `x5t` | Sträng | Fungerar på samma sätt (används och värdet) som `kid`. `x5t` är ett äldre anspråk som endast har genererats i v 1.0-åtkomsttoken för kompatibilitet. |

### <a name="payload-claims"></a>Nytto Last anspråk

| Begär | Format | Beskrivning |
|-----|--------|-------------|
| `aud` | Sträng, en app-ID-URI | Identifierar den avsedda mottagaren för token. I ID-token är mål gruppen appens program-ID som tilldelats din app i Azure Portal. Din app bör validera det här värdet och avvisa token om värdet inte matchar. |
| `iss` | Sträng, en STS-URI | Identifierar säkerhetstokentjänst som konstruerar och returnerar token och Azure AD-klienten där användaren autentiserades. Om token som utfärdas är en v 2.0-token (se `ver`-anspråk), avslutas URI: n i `/v2.0`. GUID som anger att användaren är en konsument användare från en Microsoft-konto `9188040d-6c67-4c5b-b112-36a304b66dad`. Din app ska använda en GUID-del av anspråket för att begränsa den uppsättning innehavare som kan logga in på appen, om tillämpligt. |
|`idp`| Sträng, vanligt vis en STS-URI | Registrerar den identitetsprovider som har autentiserat subjektet för token. Värdet är identiskt med värdet för Issuer-anspråket om inte användar kontot inte finns i samma klient organisation som utfärdaren-gäster, till exempel. Om anspråket inte finns, innebär det att värdet för `iss` kan användas i stället.  För personliga konton som används i en organisations kontext (till exempel ett personligt konto som bjudits in till en Azure AD-klient) kan `idp`-anspråk vara "live.com" eller en STS-URI som innehåller Microsoft-konto klient `9188040d-6c67-4c5b-b112-36a304b66dad`. |  
| `iat` | int, en UNIX-tidsstämpel | "Utfärdat vid" anger när autentiseringen för denna token ägde rum. |
| `nbf` | int, en UNIX-tidsstämpel | Anspråket "NBF" (inte före) anger hur lång tid som JWT inte får godkännas för bearbetning. |
| `exp` | int, en UNIX-tidsstämpel | Anspråket "EXP" (förfallo tid) anger förfallo tid för eller efter vilken JWT inte får godkännas för bearbetning. Det är viktigt att Observera att en resurs kanske avvisar token före den här tiden, till exempel när en ändring i autentisering krävs eller om ett token återkallning har upptäckts. |
| `aio` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure AD för att registrera data för åter användning av token. Resurserna bör inte använda detta påstående. |
| `acr` | Sträng, "0" eller "1" | Förekommer endast i v 1.0-token. Anspråket "autentiserings kontext klass". Värdet "0" anger att slutanvändaren inte uppfyller kraven i ISO/IEC 29115. |
| `amr` | JSON-matris med strängar | Förekommer endast i v 1.0-token. Anger hur ämnet för token autentiserades. Mer information finns i [avsnittet om AMR-anspråk](#the-amr-claim) . |
| `appid` | Sträng, ett GUID | Förekommer endast i v 1.0-token. Program-ID för klienten med hjälp av token. Programmet kan fungera som fristående eller för en användares räkning. Program-ID: t representerar vanligt vis ett program objekt, men det kan även representera ett tjänst objekt i Azure AD. |
| `appidacr` | "0", "1" eller "2" | Förekommer endast i v 1.0-token. Anger hur klienten autentiserades. För en offentlig klient är värdet "0". Om klient-ID och klient hemlighet används är värdet "1". Om ett klient certifikat användes för autentisering är värdet "2". |
| `azp` | Sträng, ett GUID | Finns bara i v 2.0-token, en ersättning för `appid`. Program-ID för klienten med hjälp av token. Programmet kan fungera som fristående eller för en användares räkning. Program-ID: t representerar vanligt vis ett program objekt, men det kan även representera ett tjänst objekt i Azure AD. |
| `azpacr` | "0", "1" eller "2" | Finns bara i v 2.0-token, en ersättning för `appidacr`. Anger hur klienten autentiserades. För en offentlig klient är värdet "0". Om klient-ID och klient hemlighet används är värdet "1". Om ett klient certifikat användes för autentisering är värdet "2". |
| `preferred_username` | Sträng | Det primära användar namnet som representerar användaren. Det kan vara en e-postadress, ett telefonnummer eller ett generiskt användar namn utan angivet format. Värdet är föränderligt och kan ändras med tiden. Eftersom det är föränderligt får inte det här värdet användas för att fatta auktoriseringsbeslut.  Den kan användas för användar tipsen. `profile` omfattning krävs för att ta emot detta anspråk. |
| `name` | Sträng | Ger ett läsligt värde som identifierar ämnet för token. Värdet är inte garanterat unikt, det är föränderligt och har utformats för att endast användas i visnings syfte. `profile` omfattning krävs för att ta emot detta anspråk. |
| `scp` | Sträng, en blankstegsavgränsad lista över omfång | Den uppsättning omfång som exponeras av ditt program för vilket klient programmet har begärt (och fått) medgivande. Din app bör kontrol lera att dessa omfattningar är giltiga för de som exponeras av din app och fatta auktoriseringsbeslut baserat på värdet för dessa omfång. Ingår [endast för användartoken](#user-and-application-tokens). |
| `roles` | Sträng mat ris, en lista med behörigheter | Den uppsättning behörigheter som exponeras av ditt program som det begär ande programmet eller användaren har fått behörighet att anropa. För [Application-token](#user-and-application-tokens)används detta under flödet för klientens autentiseringsuppgifter ([v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) i stället för användar omfattningar.  För [användar-tokens](#user-and-application-tokens) fylls detta med de roller som användaren har tilldelats på mål programmet. |
| `wids` | Matris med [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) -GUID | Anger de roller för klient organisationen som tilldelats den här användaren, från avsnittet av roller som finns på [sidan administratörs roller](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids).  Detta anspråk har kon figurer ATS per program, via `groupMembershipClaims`-egenskapen för [applikations manifestet](reference-app-manifest.md).  Inställningen "alla" eller "DirectoryRole" krävs.  Kanske inte finns i token som erhållits via det implicita flödet på grund av frågor om token-längd. |
| `groups` | JSON-matris med GUID | Tillhandahåller objekt-ID: n som representerar ämnets grupp medlemskap. Dessa värden är unika (se objekt-ID) och kan användas på ett säkert sätt för att hantera åtkomst, t. ex. för att tvinga behörighet att få åtkomst till en resurs. Grupperna som ingår i gruppen grupper har kon figurer ATS per program, via egenskapen `groupMembershipClaims` för [applikations manifestet](reference-app-manifest.md). Värdet null utesluter alla grupper, värdet "SecurityGroup" kommer bara att innehålla Active Directory medlemskap i säkerhets grupper och värdet "alla" omfattar både säkerhets grupper och distributions listor för Office 365. <br><br>Se `hasgroups`-anspråk nedan för information om hur du använder `groups`-anspråk med implicit beviljande. <br>För andra flöden, om antalet grupper som användaren betjänar över en gräns (150 för SAML, 200 för JWT), så läggs ett överbelastnings anspråk till i anspråks källorna som pekar på AAD-diagrammets slut punkt med listan över grupper för användaren. |
| `hasgroups` | Boolesk | Om det finns `true`ska du alltid visa att användaren finns i minst en grupp. Används i stället för `groups`-anspråket för JWTs i implicita beviljande flöden om det fullständiga grupp kravet skulle utöka URI-fragmentet utanför URL-längdens gränser (för närvarande 6 eller fler grupper). Anger att klienten ska använda grafen för att fastställa användarens grupper (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | JSON-objekt | För Tokenbegäran som inte är begränsade (se `hasgroups` ovan) men fortfarande är för stor för token kommer en länk till listan över fullständiga grupper för användaren att inkluderas. För JWTs som ett distribuerat anspråk för SAML som ett nytt anspråk i stället för `groups`-anspråket. <br><br>**Exempel-JWT-värde**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `sub` | Sträng, ett GUID | Den huvudprincip som token förutsätter information för, t. ex. användaren av en app. Värdet är oföränderligt och kan inte tilldelas om eller återanvändas. Den kan användas för att utföra verifierings kontroller på ett säkert sätt, till exempel när token används för att få åtkomst till en resurs och kan användas som en nyckel i databas tabeller. Eftersom ämnet alltid finns i de tokens som Azure AD utfärdar rekommenderar vi att du använder det här värdet i ett system för generell behörighet. Ämnet är dock unikt för ett visst program-ID. Om en enskild användare loggar in i två olika appar med två olika klient-ID: er, kommer dessa appar att få två olika värden för ämnes anspråket. Detta kan vara något behov av, beroende på dina krav på arkitektur och sekretess. Se även `oid`-anspråket (som förblir detsamma i alla appar i en klient). |
| `oid` | Sträng, ett GUID | Det oföränderliga ID: t för ett objekt i Microsoft Identity Platform, i det här fallet ett användar konto. Det kan också användas för att utföra verifierings kontroller på ett säkert sätt och som en nyckel i databas tabeller. Detta ID identifierar användaren i flera program – två olika program som loggar in på samma användare får samma värde i `oid`-anspråk. Därför kan `oid` användas när du skapar frågor till Microsoft onlinetjänster, till exempel Microsoft Graph. Microsoft Graph returnerar detta ID som `id` egenskap för ett angivet [användar konto](/graph/api/resources/user). Eftersom `oid` tillåter flera appar att korrelera användare, krävs det `profile` omfånget för att ta emot detta anspråk. Observera att om en enskild användare finns i flera klienter, kommer användaren att innehålla ett annat objekt-ID i varje klient – de betraktas som olika konton, även om användaren loggar in på varje konto med samma autentiseringsuppgifter. |
| `tid` | Sträng, ett GUID | Representerar den Azure AD-klient som användaren är från. För arbets-och skol konton är GUID det oåterkalleliga klient-ID: t för den organisation som användaren tillhör. För personliga konton är värdet `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` omfattning krävs för att ta emot detta anspråk. |
| `unique_name` | Sträng | Förekommer endast i v 1.0-token. Innehåller ett läsbart värde som identifierar subjektet för token. Det här värdet är inte garanterat unikt inom en klient organisation och bör endast användas för visning. |
| `uti` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure för att omverifiera token. Resurserna bör inte använda det här anspråket. |
| `rh` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure för att omverifiera token. Resurserna bör inte använda detta påstående. |
| `ver` | Sträng, antingen `1.0` eller `2.0` | Anger versionen för åtkomsttoken. |


> [!NOTE]
> **Gruppera överliggande anspråk**
>
> För att säkerställa att token-storleken inte överskrider storleks gränserna för HTTP-huvudet begränsar Azure AD antalet objekt-ID: n som ingår i grupp anspråket. Om en användare är medlem i fler grupper än överkvoten (150 för SAML-tokens, 200 för JWT-token), genererar inte Azure AD grupp anspråket i token. I stället innehåller den ett överanvändning-anspråk i token som indikerar att programmet ska fråga Graph API för att hämta användarens grupp medlemskap.
  ```csharp
  {
    ...
    "_claim_names": {
     "groups": "src1"
      },
      {
    "_claim_sources": {
      "src1": {
          "endpoint":"[Graph Url to get this user's group membership from]"
          }
         }
       }
    ...
   }
   ```
> Du kan använda den `BulkCreateGroups.ps1` som finns i mappen skript för att [Skapa appar](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims/blob/master/AppCreationScripts/) för att testa överanvändnings scenarier.

#### <a name="v10-basic-claims"></a>v 1.0 Basic-anspråk

Följande anspråk kommer att ingå i v 1.0-token om det är tillämpligt, men inte ingår i v 2.0-token som standard. Om du använder v 2.0 och behöver någon av dessa anspråk kan du begära dem med [valfria anspråk](active-directory-optional-claims.md).

| Begär | Format | Beskrivning |
|-----|--------|-------------|
| `ipaddr`| Sträng | IP-adressen som användaren autentiseras från. |
| `onprem_sid`| Sträng, i [sid-format](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | I de fall där användaren har en lokal autentisering, ger detta anspråk sitt SID. Du kan använda `onprem_sid` för auktorisering i äldre program.|
| `pwd_exp`| int, en UNIX-tidsstämpel | Anger när användarens lösen ord upphör att gälla. |
| `pwd_url`| Sträng | En URL där användare kan skickas för att återställa sina lösen ord. |
| `in_corp`| boolesk | Signalerar om klienten loggar in från företags nätverket. Om de inte är det inkluderas inte anspråket. |
| `nickname`| Sträng | Ett ytterligare namn för användaren, separat från förnamn eller efter namn.|
| `family_name` | Sträng | Innehåller användarens efter namn, efter namn eller familje namn som definierats på användarobjektet. |
| `given_name` | Sträng | Innehåller det första eller det angivna namnet på användaren, som anges på användarobjektet. |
| `upn` | Sträng | Användarens användar namn. Kan vara ett telefonnummer, en e-postadress eller en oformaterad sträng. Bör endast användas för att visa och tillhandahålla användar tips i omautentiserings scenarier. |

#### <a name="the-amr-claim"></a>`amr`-anspråk

Microsoft-identiteter kan autentiseras på olika sätt, vilket kan vara relevant för ditt program. `amr`-anspråket är en matris som kan innehålla flera objekt, till exempel `["mfa", "rsa", "pwd"]`, för en autentisering som har använt både ett lösen ord och Authenticator-appen.

| Värde | Beskrivning |
|-----|-------------|
| `pwd` | Lösenordsautentisering, antingen användarens Microsoft-lösenord eller en Apps klient hemlighet. |
| `rsa` | Autentiseringen baseras på beviset för en RSA-nyckel, till exempel med [Microsoft Authenticator-appen](https://aka.ms/AA2kvvu). Detta inkluderar om autentiseringen utfördes av ett självsignerat JWT med ett tjänst ägda X509-certifikat. |
| `otp` | Eng ång slö sen ord med ett e-postmeddelande eller ett SMS. |
| `fed` | En försäkrad federerad autentisering (till exempel JWT eller SAML) användes. |
| `wia` | Windows-integrerad autentisering |
| `mfa` | Multi-Factor Authentication användes. När detta är tillgängligt kommer även andra autentiseringsmetoder att tas med. |
| `ngcmfa` | Motsvarar `mfa`som används för etablering av vissa avancerade autentiseringstyper. |
| `wiaormfa`| Användaren använde Windows eller en MFA-autentiseringsuppgift för att autentisera. |
| `none` | Ingen autentisering har gjorts. |

## <a name="validating-tokens"></a>Validerar token

För att verifiera en id_token eller ett access_token bör din app validera både token signatur och anspråk. För att verifiera åtkomsttoken bör din app även validera utfärdaren, mål gruppen och signerings-token. Dessa måste verifieras mot värdena i OpenID identifierings dokument. Till exempel finns den klient oberoende versionen av dokumentet på [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Azure AD-har inbyggda funktioner för att verifiera åtkomsttoken och du kan bläddra igenom våra [exempel](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) för att hitta ett på valfritt språk. Mer information om hur du uttryckligen validerar en JWT-token finns i det [manuella JWT-verifierings exemplet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).

Vi tillhandahåller bibliotek och kod exempel som visar hur du enkelt hanterar verifiering av token. Informationen nedan tillhandahålls för de som vill förstå den underliggande processen. Det finns också flera tredjeparts bibliotek med öppen källkod som är tillgängliga för JWT-validering. det finns minst ett alternativ för nästan alla plattformar och språk som finns där. Mer information om Azure AD-autentiseringspaket och kod exempel finns i [v 1.0 Authentication libraries](../azuread-dev/active-directory-authentication-libraries.md) och [v 2.0-autentiseringsinställningar](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Verifiera signaturen

En JWT innehåller tre segment, som avgränsas med `.`-tecknen. Det första segmentet kallas **sidhuvud**, det andra som **bröd texten**och det tredje som **signaturen**. Du kan använda signatur segmentet för att verifiera tokens äkthet så att den kan vara betrodd av din app.

Token som utfärdas av Azure AD är signerade med algoritmer för asymmetrisk kryptering i bransch standard, till exempel RS256. Huvud-i-JWT-filen innehåller information om nyckeln och krypterings metoden som används för att signera token:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg`-anspråk anger algoritmen som användes för att signera token, medan `kid`-anspråket anger den specifika offentliga nyckel som användes för att validera token.

Vid en viss tidpunkt kan Azure AD signera en id_token med en viss uppsättning offentliga privata nyckel par. Azure AD roterar den möjliga uppsättningen nycklar regelbundet, så att din app kan skrivas för att hantera dessa ändringar automatiskt. En rimlig frekvens för att söka efter uppdateringar av de offentliga nycklar som används av Azure AD är var 24: e timme.

Du kan hämta de signerings nyckel data som krävs för att validera signaturen med hjälp av [OpenID Connect-Metadatadokumentet](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) som finns på:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Prova denna [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) i en webbläsare!

Detta dokument för metadata:

* Är ett JSON-objekt som innehåller flera användbara informations delar, till exempel platsen för de olika slut punkter som krävs för att utföra OpenID Connect-autentisering.
* Innehåller en `jwks_uri`som ger platsen för uppsättningen offentliga nycklar som används för att signera token. JSON-webbnyckeln (JWK) som finns på `jwks_uri` innehåller all information om den offentliga nyckeln som används just nu.  JWK-formatet beskrivs i [RFC 7517](https://tools.ietf.org/html/rfc7517).  Din app kan använda `kid`-anspråk i JWT-huvudet för att välja vilken offentlig nyckel i det här dokumentet som har använts för att signera en viss token. Den kan sedan utföra signaturverifiering med rätt offentlig nyckel och angiven algoritm.

> [!NOTE]
> V 1.0-slutpunkten returnerar både `x5t`-och `kid`-anspråk, medan v 2.0-slutpunkten svarar med endast `kid`-anspråk. Vi rekommenderar att du använder `kid`-anspråk för att validera din token.

Verifieringen av signaturen är utanför det här dokumentets omfång – det finns många bibliotek med öppen källkod som hjälper dig att göra det om det behövs.  Men Microsoft Identity Platform har ett tillägg för Token-signering till standard-anpassade signerings nycklar.  

Om din app har anpassade signerings nycklar som ett resultat av funktionen för [anspråks mappning](active-directory-claims-mapping.md) måste du lägga till en `appid` frågeparameter som innehåller app-ID: t för att få ett `jwks_uri` som pekar på appens signerings nyckel information som ska användas för verifiering. Exempel: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` innehåller en `jwks_uri` av `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Anspråksbaserad auktorisering

Ditt programs affärs logik kommer att diktera det här steget, några vanliga autentiseringsmetoder anges nedan.

* Kontrol lera `scp`-eller `roles`-anspråk för att kontrol lera att alla befintliga omfattningar matchar de som exponeras av ditt API och Tillåt att klienten utför den begärda åtgärden.
* Se till att anrops klienten kan anropa ditt API med hjälp av `appid`-anspråk.
* Verifiera verifierings status för den anropande klienten med hjälp av `appidacr` – den får inte vara 0 om offentliga klienter inte tillåts anropa ditt API.
* Kontrol lera mot en lista över tidigare `nonce` anspråk för att kontrol lera att token inte spelas upp igen.
* Kontrol lera att `tid` matchar en klient som tillåts anropa ditt API.
* Använd `acr`-anspråk för att verifiera att användaren har utfört MFA. Detta bör tillämpas med [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Om du har begärt `roles`-eller `groups`-anspråk i åtkomst-token, kontrollerar du att användaren finns i gruppen som har behörighet att utföra åtgärden.
  * För token som hämtats med det implicita flödet måste du förmodligen fråga [Microsoft Graph](https://developer.microsoft.com/graph/) efter dessa data, eftersom det ofta är för stort för att få plats i token.

## <a name="user-and-application-tokens"></a>Token för användare och program

Ditt program kan ta emot token för en användares räkning (det vanliga flödet) eller direkt från ett program (via klientens Credential Flow ([v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)). Dessa app-only-token anger att det här anropet kommer från ett program och inte har en användare som säkerhetskopierar det. Dessa tokens hanteras i stor utsträckning, med vissa skillnader:

* Endast app-token kommer att ha ett `scp`-anspråk och kan i stället ha ett `roles`-anspråk. Det är här som program behörigheten (till skillnad från delegerade behörigheter) ska registreras. Mer information om delegerade och program behörigheter finns i behörighet och medgivande ([v 1.0](../azuread-dev/v1-permissions-consent.md), [v 2.0](v2-permissions-and-consent.md)).
* Många Human-/regionsspecifika anspråk kommer att saknas, till exempel `name` eller `upn`.
* `sub`-och `oid`-anspråk är desamma. 

## <a name="token-revocation"></a>Återkalla token

Uppdaterings-token kan inaktive ras eller återkallas av olika skäl. De delas in i två huvud kategorier: tids gränser och åter kallelser.

### <a name="token-timeouts"></a>Timeout för token

* MaxInactiveTime: om uppdateringstoken inte har använts inom den tid som bestäms av MaxInactiveTime, kommer uppdateringstoken inte längre att vara giltig.
* MaxSessionAge: om MaxAgeSessionMultiFactor eller MaxAgeSessionSingleFactor har angetts till något annat än standardvärdet (tills det har återkallats) krävs omautentisering efter att den tid som anges i MaxAgeSession * går ut.
* Exempel:
  * Klienten har en MaxInactiveTime på fem dagar och användaren gick på semester i en vecka, och därför har Azure AD inte sett en ny Tokenbegäran från användaren om 7 dagar. Nästa gång användaren begär en ny token hittar de en uppdateringstoken som har återkallats, och de måste ange sina autentiseringsuppgifter igen.
  * Ett känsligt program har en MaxAgeSessionSingleFactor på en dag. Om en användare loggar in på måndag och tisdag (efter 25 timmar har förflutit) måste de autentiseras igen.

### <a name="revocation"></a>Återkallade

|   | Lösenordsbaserad cookie | Lösenordsbaserad token | Icke-lösenordsbaserad cookie | Icke-lösenordsbaserad token | Konfidentiell klient-token |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Lösen ordet upphör att gälla | Förblir Alive | Förblir Alive | Förblir Alive | Förblir Alive | Förblir Alive |
| Lösen ordet har ändrats av användaren | Återkallats | Återkallats | Förblir Alive | Förblir Alive | Förblir Alive |
| Användaren SSPR | Återkallats | Återkallats | Förblir Alive | Förblir Alive | Förblir Alive |
| Administratör återställer lösen ordet | Återkallats | Återkallats | Förblir Alive | Förblir Alive | Förblir Alive |
| Användaren återkallar sina Refresh-token [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Återkallats | Återkallats | Återkallats | Återkallats | Återkallats |
| Administratören återkallar alla uppdateringstoken för klienten [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Återkallats | Återkallats |Återkallats | Återkallats | Återkallats |
| Enkel utloggning ([v 1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v 2.0](v2-protocols-oidc.md#single-sign-out) ) på webben | Återkallats | Förblir Alive | Återkallats | Förblir Alive | Förblir Alive |

> [!NOTE]
> En "icke-lösenords-baserad" inloggning är en där användaren inte skrev något lösen ord för att hämta det. Du kan till exempel använda din ansikte med Windows Hello, en FIDO2-nyckel eller en PIN-kod.
>
> Primära uppdateringstoken (PRT) i Windows 10 åtskiljs utifrån autentiseringsuppgifterna. Till exempel har Windows Hello och lösen ordet sina respektive PRTs, isolerade från varandra. När en användare loggar in med en hälsnings information (PIN-kod eller biometrik) och sedan ändrar lösen ordet, kommer lösen ordsbaserade PRT som hämtades tidigare att återkallas. Om du loggar in igen med ett lösen ord avverifierar du det gamla PRT och begär ett nytt.
>
> Uppdateringstoken är inte ogiltiga eller återkallade när de används för att hämta en ny åtkomsttoken och en uppdateringstoken.  

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [`id_tokens` i Azure AD](id-tokens.md).
* Läs mer om behörighet och medgivande ( [v 1.0](../azuread-dev/v1-permissions-consent.md), [v 2.0](v2-permissions-and-consent.md)).
