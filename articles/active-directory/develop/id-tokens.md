---
title: Referens för Microsoft Identity Platform-ID-token | Microsoft Docs
description: Lär dig hur du använder id_tokens som har spridits av Azure AD v 1.0 och Microsoft Identity Platform (v 2.0) slut punkter.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7e910faaf9875b6791135c8721090fa801a7e08
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294192"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft Identity Platform ID-token

`id_tokens` skickas till klient programmet som en del av ett [OpenID Connect](v1-protocols-openid-connect-code.md) -flöde. De kan skickas på sidan eller i stället för en åtkomsttoken och används av klienten för att autentisera användaren.

## <a name="using-the-id_token"></a>Använda id_token

ID-token ska användas för att verifiera att en användare är den som han eller hon ansöker för att få ytterligare värdefull information om dem – den bör inte användas för auktorisering i stället för en [åtkomsttoken](access-tokens.md). Anspråken som det tillhandahåller kan användas för UX i ditt program, som nycklar i en databas och för att ge åtkomst till klient programmet.  När du skapar nycklar för en databas bör `idp` inte användas eftersom den tar upp gäst scenarier.  Du bör utföra en nyckel på `sub` ensam (som alltid är unik), med `tid` som används för routning om det behövs.  Om du behöver dela data mellan tjänster `oid`+`sub`+`tid` fungerar eftersom flera tjänster får samma `oid`.

## <a name="claims-in-an-id_token"></a>Anspråk i en id_token

`id_tokens` för en Microsoft-identitet är [JWTs](https://tools.ietf.org/html/rfc7519), vilket innebär att de består av en rubrik, en nytto last och en signatur. Du kan använda rubriken och signaturen för att verifiera tokens äkthet, medan nytto lasten innehåller information om användaren som begärdes av din klient. Om inget annat anges visas alla anspråk som anges här i både v 1.0-och v 2.0-token.

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
|`typ` | Sträng-Always-JWT | Anger att token är en JWT.|
|`alg` | String | Anger algoritmen som användes för att signera token. Exempel: "RS256" |
|`kid` | String | Tumavtryck för den offentliga nyckel som används för att signera denna token. Genereras i både v 1.0 och v 2.0 `id_tokens`. |
|`x5t` | String | Samma (används och värdet) som `kid`. Detta är dock ett äldre anspråk som endast har genererats i v 1.0 `id_tokens` för kompatibilitet. |

### <a name="payload-claims"></a>Nytto Last anspråk

I den här listan visas de anspråk som finns i de flesta id_tokens som standard (förutom där antecknas).  Din app kan dock använda [valfria anspråk](active-directory-optional-claims.md) för att begära ytterligare anspråk i id_token.  Dessa kan variera från `groups`-anspråk till information om användarens namn.

|Begär | Format | Beskrivning |
|-----|--------|-------------|
|`aud` |  Sträng, en app-ID-URI | Identifierar den avsedda mottagaren för token. I `id_tokens`är mål gruppen appens program-ID som tilldelats din app i Azure Portal. Din app bör validera det här värdet och avvisa token om värdet inte matchar. |
|`iss` |  Sträng, en STS-URI | Identifierar säkerhetstokentjänst som konstruerar och returnerar token och Azure AD-klienten där användaren autentiserades. Om token utfärdades av v 2.0-slutpunkten slutar URI: n i `/v2.0`.  GUID som anger att användaren är en konsument användare från en Microsoft-konto `9188040d-6c67-4c5b-b112-36a304b66dad`. Din app ska använda en GUID-del av anspråket för att begränsa den uppsättning innehavare som kan logga in på appen, om tillämpligt. |
|`iat` |  int, en UNIX-tidsstämpel | "Utfärdat vid" anger när autentiseringen för denna token ägde rum.  |
|`idp`|Sträng, vanligt vis en STS-URI | Registrerar den identitetsprovider som har autentiserat subjektet för token. Värdet är identiskt med värdet för Issuer-anspråket om inte användar kontot inte finns i samma klient organisation som utfärdaren-gäster, till exempel. Om anspråket inte finns, innebär det att värdet för `iss` kan användas i stället.  För personliga konton som används i en organisations kontext (till exempel ett personligt konto som bjudits in till en Azure AD-klient) kan `idp`-anspråk vara "live.com" eller en STS-URI som innehåller Microsoft-konto klient `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, en UNIX-tidsstämpel | Anspråket "NBF" (inte före) anger hur lång tid som JWT inte får godkännas för bearbetning.|
|`exp` |  int, en UNIX-tidsstämpel | Anspråket "EXP" (förfallo tid) anger förfallo tid för eller efter vilken JWT inte får godkännas för bearbetning.  Det är viktigt att Observera att en resurs kanske avvisar token före den här tiden, till exempel om en ändring i autentisering krävs eller om en åter kallelse av token har identifierats. |
| `c_hash`| String |Kod-hash ingår endast i ID-token när ID-token utfärdas med en OAuth 2,0-auktoriseringskod. Den kan användas för att verifiera äktheten för en auktoriseringskod. Mer information om hur du utför den här verifieringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String |Hashen för åtkomsttoken ingår bara i ID-tokens när ID-token utfärdas med en OAuth 2,0-åtkomsttoken. Den kan användas för att verifiera äktheten på en åtkomsttoken. Mer information om hur du utför den här verifieringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure AD för att registrera data för åter användning av token. Ignoreras.|
|`preferred_username` | String | Det primära användar namnet som representerar användaren. Det kan vara en e-postadress, ett telefonnummer eller ett generiskt användar namn utan angivet format. Värdet är föränderligt och kan ändras med tiden. Eftersom det är föränderligt får inte det här värdet användas för att fatta auktoriseringsbeslut. `profile` omfattning krävs för att ta emot det här anspråket.|
|`email` | String | `email`-anspråk finns som standard för gäst konton som har en e-postadress.  Din app kan begära e-postanspråk för hanterade användare (från samma klient som resursen) med hjälp av `email` [valfritt anspråk](active-directory-optional-claims.md).  På v 2.0-slutpunkten kan din app även begära `email` OpenID Connect-omfånget – du behöver inte begära både det valfria anspråket och omfattningen för att hämta anspråket.  E-postanspråket stöder bara adresser bara e-post från användarens profil information. |
|`name` | String | `name`-anspråk ger ett läsbart värde som identifierar ämnet för token. Värdet är inte garanterat unikt, det är föränderligt och har utformats för att endast användas i visnings syfte. `profile` omfattning krävs för att ta emot det här anspråket. |
|`nonce`| String | Nonce matchar den parameter som ingår i den ursprungliga/Authorize-begäran till IDP. Om den inte matchar bör ditt program avvisa token. |
|`oid` | Sträng, ett GUID | Det oföränderliga ID: t för ett objekt i Microsoft Identity system, i det här fallet ett användar konto. Detta ID identifierar användaren i flera program – två olika program som loggar in på samma användare får samma värde i `oid`-anspråk. Microsoft Graph returnerar detta ID som `id` egenskap för ett angivet användar konto. Eftersom `oid` tillåter flera appar att korrelera användare måste `profile` omfånget ta emot detta anspråk. Observera att om en enskild användare finns i flera klienter, kommer användaren att innehålla ett annat objekt-ID i varje klient – de betraktas som olika konton, även om användaren loggar in på varje konto med samma autentiseringsuppgifter. `oid`-anspråk är ett GUID och kan inte återanvändas. |
|`roles`| Matris med strängar | Den uppsättning roller som har tilldelats användaren som loggar in. |
|`rh` | Ogenomskinlig sträng |Ett internt anspråk som används av Azure för att omverifiera token. Ignoreras. |
|`sub` | Sträng, ett GUID | Den huvudprincip som token förutsätter information för, t. ex. användaren av en app. Värdet är oföränderligt och kan inte tilldelas om eller återanvändas. Ämnet är en identifierad identifierare – den är unik för ett visst program-ID. Om en enskild användare loggar in i två olika appar med två olika klient-ID: n, får dessa appar två olika värden för ämnes anspråket. Detta kan vara så eller kanske inte önskas beroende på dina krav på arkitektur och sekretess. |
|`tid` | Sträng, ett GUID | Ett GUID som representerar den Azure AD-klient som användaren är från. För arbets-och skol konton är GUID det oåterkalleliga klient-ID: t för den organisation som användaren tillhör. För personliga konton är värdet `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` omfattning krävs för att ta emot det här anspråket. |
|`unique_name` | String | Innehåller ett läsbart värde som identifierar subjektet för token. Det här värdet är unikt vid varje viss tidpunkt, men när e-postmeddelanden och andra identifierare kan återanvändas kan det här värdet visas igen på andra konton och bör därför endast användas för visning. Endast utfärdat i v 1.0 `id_tokens`. |
|`uti` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure för att omverifiera token. Ignoreras. |
|`ver` | Sträng, antingen 1,0 eller 2,0 | Anger versionen för id_token. |


> [!NOTE]
> V1-och v2-id_token har skillnader i mängden information som de kommer att ha som visas i exemplen ovan. Versionen anger i princip slut punkten för Azure AD-plattformen varifrån den utfärdades. [Azure AD OAuth-implementeringen](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) har utvecklats genom åren. För närvarande har vi två olika oAuth-slutpunkter för AzureAD-program. Du kan använda någon av de nya slut punkterna som är kategoriserade som v2 eller den gamla som är v1. OAuth-slutpunkterna för båda är olika. V2-slutpunkten är den nyare där vi försöker migrera alla funktioner i v1-slutpunkt och rekommendera nya utvecklare att använda v2-slutpunkten. 
> - V1: Azure Active Directory slut punkter: `https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: slut punkter för Microsoft Identity Platform: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Verifiera en id_token

Validering av en `id_token` liknar det första steget i att [Verifiera en åtkomsttoken](access-tokens.md#validating-tokens) – klienten bör verifiera att rätt utfärdare har skickat tillbaka token och att den inte har manipulerats. Eftersom `id_tokens` alltid är en JWT, finns det många bibliotek för att validera dessa tokens – vi rekommenderar att du använder någon av dessa i stället för att göra det själv.

Information om hur du validerar token manuellt finns i avsnittet om hur du [verifierar en](access-tokens.md#validating-tokens)åtkomsttoken. När signaturen för token har verifierats ska följande anspråk val IDE ras i id_token (de kan också utföras av ditt verifierings bibliotek för token):

* Tidsstämplar: `iat`, `nbf`och `exp` tidsstämplar måste vara före eller efter den aktuella tiden, efter vad som är tillämpligt. 
* Mål grupp: `aud`-anspråk ska matcha program-ID: t för ditt program.
* Nonce: `nonce`-anspråk i nytto lasten måste matcha nonce-parametern som överförts till/Authorize-slutpunkten under den första begäran.

## <a name="next-steps"></a>Nästa steg

* Läs mer [om åtkomsttoken](access-tokens.md)
* Anpassa anspråk i id_token med [valfria anspråk](active-directory-optional-claims.md).
