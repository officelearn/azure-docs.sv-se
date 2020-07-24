---
title: Microsoft Identity Platform ID-token | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du använder id_tokens som har spridits av Azure AD v 1.0 och Microsoft Identity Platform (v 2.0) slut punkter.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 2fbbf5f9d01ed4a469967dac87faa3b130905757
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027112"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft Identity Platform ID-token

`id_tokens`skickas till klient programmet som en del av ett [OpenID Connect](v2-protocols-oidc.md) -flöde (OIDC). De kan skickas på sidan eller i stället för en åtkomsttoken och används av klienten för att autentisera användaren.

## <a name="using-the-id_token"></a>Använda id_token

ID-token ska användas för att verifiera att en användare är den som han eller hon ansöker för att få ytterligare värdefull information om dem – den bör inte användas för auktorisering i stället för en [åtkomsttoken](access-tokens.md). Anspråken som det tillhandahåller kan användas för UX i ditt program, som nycklar i en databas och för att ge åtkomst till klient programmet.  När du skapar nycklar för en databas `idp` bör du inte använda den, eftersom den kan förstöra gäst scenarier.  Nycklar bör utföras `sub` separat (som alltid är unika), med `tid` används för routning om det behövs.  Om du behöver dela data mellan tjänster `oid` + `sub` + `tid` fungerar det eftersom flera tjänster kommer att fungera `oid` .

## <a name="claims-in-an-id_token"></a>Anspråk i en id_token

`id_tokens`för en Microsoft-identitet är [JWTs](https://tools.ietf.org/html/rfc7519) (JSON Web tokens), vilket innebär att de består av en rubrik, en nytto last och en signatur. Du kan använda rubriken och signaturen för att verifiera tokens äkthet, medan nytto lasten innehåller information om användaren som begärdes av din klient. Om inget annat anges visas alla JWT-anspråk som anges här i både v 1.0-och v 2.0-token.

### <a name="v10"></a>V1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Visa den här v 1.0-exempel-token i [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Visa denna v 2.0-exempel-token i [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Huvud anspråk

|Begär | Format | Beskrivning |
|-----|--------|-------------|
|`typ` | Sträng-Always-JWT | Anger att token är en JWT-token.|
|`alg` | Sträng | Anger algoritmen som användes för att signera token. Exempel: "RS256" |
|`kid` | Sträng | Tumavtryck för den offentliga nyckel som används för att signera denna token. Genereras i både v 1.0 och v 2.0 `id_tokens` . |
|`x5t` | Sträng | Samma (används och värdet) som `kid` . Detta är dock ett äldre anspråk som endast har genererats i v 1.0 `id_tokens` för kompatibilitet. |

### <a name="payload-claims"></a>Nytto Last anspråk

I den här listan visas de JWT-anspråk som är i de flesta id_tokens som standard (utom där antecknas).  Din app kan dock använda [valfria anspråk](active-directory-optional-claims.md) för att begära ytterligare JWT-anspråk i id_token.  Detta kan vara ett intervall från `groups` anspråk till information om användarens namn.

|Begär | Format | Beskrivning |
|-----|--------|-------------|
|`aud` |  Sträng, en app-ID-URI | Identifierar den avsedda mottagaren för token. I `id_tokens` är mål gruppen appens program-ID som har tilldelats din app i Azure Portal. Din app bör validera det här värdet och avvisa token om värdet inte matchar. |
|`iss` |  Sträng, en STS-URI | Identifierar säkerhetstokentjänst som konstruerar och returnerar token och Azure AD-klienten där användaren autentiserades. Om token har utfärdats av v 2.0-slutpunkten avslutas URI: n `/v2.0` .  GUID som anger att användaren är en konsument användare från en Microsoft-konto `9188040d-6c67-4c5b-b112-36a304b66dad` . Din app ska använda en GUID-del av anspråket för att begränsa den uppsättning innehavare som kan logga in på appen, om tillämpligt. |
|`iat` |  int, en UNIX-tidsstämpel | "Utfärdat vid" anger när autentiseringen för denna token ägde rum.  |
|`idp`|Sträng, vanligt vis en STS-URI | Registrerar den identitetsprovider som har autentiserat subjektet för token. Värdet är identiskt med värdet för Issuer-anspråket om inte användar kontot inte finns i samma klient organisation som utfärdaren-gäster, till exempel. Om anspråket inte finns, innebär det att värdet för `iss` kan användas i stället.  För personliga konton som används i en organisations kontext (till exempel ett personligt konto som bjudits in till en Azure AD-klient) `idp` kan anspråk vara "Live.com" eller en STS-URI som innehåller Microsoft-konto klient `9188040d-6c67-4c5b-b112-36a304b66dad` . |
|`nbf` |  int, en UNIX-tidsstämpel | Anspråket "NBF" (inte före) anger hur lång tid som JWT inte får godkännas för bearbetning.|
|`exp` |  int, en UNIX-tidsstämpel | Anspråket "EXP" (förfallo tid) anger förfallo tid för eller efter vilken JWT inte får godkännas för bearbetning.  Det är viktigt att Observera att en resurs kanske avvisar token före den här tiden, till exempel om en ändring i autentisering krävs eller om en åter kallelse av token har identifierats. |
| `c_hash`| Sträng |Kod-hash ingår endast i ID-token när ID-token utfärdas med en OAuth 2,0-auktoriseringskod. Den kan användas för att verifiera äktheten för en auktoriseringskod. Mer information om hur du utför den här verifieringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Sträng |Hashen för åtkomsttoken ingår bara i ID-tokens när ID-token utfärdas med en OAuth 2,0-åtkomsttoken. Den kan användas för att verifiera äktheten på en åtkomsttoken. Mer information om hur du utför den här verifieringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure AD för att registrera data för åter användning av token. Ignoreras.|
|`preferred_username` | Sträng | Det primära användar namnet som representerar användaren. Det kan vara en e-postadress, ett telefonnummer eller ett generiskt användar namn utan angivet format. Värdet är föränderligt och kan ändras med tiden. Eftersom det är föränderligt får inte det här värdet användas för att fatta auktoriseringsbeslut. `profile`Omfånget krävs för att ta emot det här anspråket.|
|`email` | Sträng | `email`Anspråket finns som standard för gäst konton som har en e-postadress.  Din app kan begära e-postanspråk för hanterade användare (från samma klient som resursen) med hjälp av det `email` [valfria anspråket](active-directory-optional-claims.md).  På v 2.0-slutpunkten kan din app även begära `email` OpenID Connect-omfånget – du behöver inte begära både det valfria anspråket och omfattningen för att hämta anspråket.  E-postanspråket stöder bara adresser bara e-post från användarens profil information. |
|`name` | Sträng | `name`Anspråket ger ett läsligt värde som identifierar ämnet för token. Värdet är inte garanterat unikt, det är föränderligt och har utformats för att endast användas i visnings syfte. `profile`Omfånget krävs för att ta emot det här anspråket. |
|`nonce`| Sträng | Nonce matchar den parameter som ingår i den ursprungliga/Authorize-begäran till IDP. Om den inte matchar bör ditt program avvisa token. |
|`oid` | Sträng, ett GUID | Det oföränderliga ID: t för ett objekt i Microsoft Identity system, i det här fallet ett användar konto. Det här ID: t identifierar unikt användaren för alla program – två olika program som loggar in på samma användare får samma värde i `oid` anspråket. Microsoft Graph returnerar detta ID som `id` egenskap för ett angivet användar konto. Eftersom `oid` tillåter flera appar att korrelera användare måste `profile` omfånget ta emot detta anspråk. Observera att om en enskild användare finns i flera klienter, kommer användaren att innehålla ett annat objekt-ID i varje klient – de betraktas som olika konton, även om användaren loggar in på varje konto med samma autentiseringsuppgifter. `oid`Anspråket är ett GUID och kan inte återanvändas. |
|`roles`| Strängmatris | Den uppsättning roller som har tilldelats användaren som loggar in. |
|`rh` | Ogenomskinlig sträng |Ett internt anspråk som används av Azure för att omverifiera token. Ignoreras. |
|`sub` | Sträng, ett GUID | Den huvudprincip som token förutsätter information för, t. ex. användaren av en app. Värdet är oföränderligt och kan inte tilldelas om eller återanvändas. Ämnet är en identifierad identifierare – den är unik för ett visst program-ID. Om en enskild användare loggar in i två olika appar med två olika klient-ID: n, får dessa appar två olika värden för ämnes anspråket. Detta kan vara så eller kanske inte önskas beroende på dina krav på arkitektur och sekretess. |
|`tid` | Sträng, ett GUID | Ett GUID som representerar den Azure AD-klient som användaren är från. För arbets-och skol konton är GUID det oåterkalleliga klient-ID: t för den organisation som användaren tillhör. För personliga konton är värdet `9188040d-6c67-4c5b-b112-36a304b66dad` . `profile`Omfånget krävs för att ta emot det här anspråket. |
|`unique_name` | Sträng | Innehåller ett läsbart värde som identifierar subjektet för token. Det här värdet är unikt vid varje viss tidpunkt, men när e-postmeddelanden och andra identifierare kan återanvändas kan det här värdet visas igen på andra konton och bör därför endast användas för visning. Endast utfärdat i v 1.0 `id_tokens` . |
|`uti` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure för att omverifiera token. Ignoreras. |
|`ver` | Sträng, antingen 1,0 eller 2,0 | Anger versionen för id_token. |

> [!NOTE]
> V 1.0-och v-22.0 id_token har skillnader i mängden information som de kommer att ha som visas i exemplen ovan. Versionen anger i princip slut punkten för Azure AD-plattformen varifrån den utfärdades. [Azure AD OAuth-implementeringen](about-microsoft-identity-platform.md) har utvecklats genom åren. Det finns för närvarande två olika outh-slutpunkter för Azure AD-program. Du kan använda någon av de nya slut punkterna som kategoriseras som v 2.0 eller v 1.0. OAuth-slutpunkter för båda dessa är olika. V 2.0-slutpunkten är nyare och funktionerna i v 1.0-slutpunkten migreras till den här slut punkten. Nya utvecklare bör använda v 2.0-slutpunkten.
>
> - v 1.0: Azure AD-slut punkter:`https://login.microsoftonline.com/common/oauth2/authorize`
> - v 2.0: Microsoft identitypPlatform-slutpunkter:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Verifiera en id_token

Verifiering `id_token` av en liknar det första steget i att [Verifiera en åtkomsttoken](access-tokens.md#validating-tokens) – klienten bör verifiera att rätt utfärdare har skickat tillbaka token och att den inte har manipulerats. Eftersom `id_tokens` är alltid en JWT-token finns det många bibliotek för att validera dessa tokens – vi rekommenderar att du använder någon av dessa i stället för att göra det själv.

Information om hur du validerar token manuellt finns i avsnittet om hur du [verifierar en](access-tokens.md#validating-tokens)åtkomsttoken. När signaturen för token har verifierats ska följande JWT-anspråk val IDE ras i id_token (de kan också utföras av ditt verifierings bibliotek för token):

* Tidsstämplar: `iat` -, `nbf` -och- `exp` tidsstämplar bör alla falla före eller efter den aktuella tiden, efter behov.
* Mål grupp: `aud` anspråket ska matcha app-ID för ditt program.
* Nonce: `nonce` anspråk i nytto lasten måste matcha nonce-parametern som överförts till/Authorize-slutpunkten under den första begäran.

## <a name="next-steps"></a>Nästa steg

* Läs mer [om åtkomsttoken](access-tokens.md)
* Anpassa JWT-anspråk i id_token med [valfria anspråk](active-directory-optional-claims.md).
