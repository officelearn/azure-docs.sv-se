---
title: Tokenreferens för Microsoft-identitetsplattform
description: Lär dig hur du använder id_tokens som avges av slutpunkterna Azure AD v1.0 och Microsoft Identity Platform (v2.0).
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
ms.openlocfilehash: 3ede70719c124caebc541df8578ab2720fc68cd5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885522"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft-identitetsplattforms-ID-token

`id_tokens`skickas till klientprogrammet som en del av ett [OpenID Connect-flöde.](v2-protocols-oidc.md) De kan skickas sida vid sida eller i stället för en åtkomsttoken och används av klienten för att autentisera användaren.

## <a name="using-the-id_token"></a>Använda id_token

ID-tokens ska användas för att verifiera att en användare är den de påstår sig vara och få ytterligare användbar information om dem - den ska inte användas för auktorisering i stället för en [åtkomsttoken](access-tokens.md). De anspråk som den tillhandahåller kan användas för UX i ditt program, som nycklar i en databas och ge åtkomst till klientprogrammet.  När du skapar nycklar `idp` för en databas, bör inte användas eftersom det förstör gästscenarier.  Nyckeldragning bör göras `sub` på ensam (vilket `tid` alltid är unikt), med används för routning om det behövs.  Om du behöver dela data `oid` + `sub` + `tid` mellan tjänster, kommer att `oid`fungera eftersom flera tjänster alla får samma .

## <a name="claims-in-an-id_token"></a>Fordringar i en id_token

`id_tokens`för en Microsoft-identitet är [JWTs](https://tools.ietf.org/html/rfc7519), vilket innebär att de består av en rubrik, nyttolast och signatur del. Du kan använda sidhuvudet och signaturen för att verifiera tokens äkthet, medan nyttolasten innehåller information om användaren som begärs av klienten. Om inget annat anges visas alla anspråk som anges här i både v1.0- och v2.0-tokens.

### <a name="v10"></a>V1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Visa den här v1.0-exempeltoken i [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Visa den här v2.0-exempeltoken i [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Huvudanspråk

|Begär | Format | Beskrivning |
|-----|--------|-------------|
|`typ` | String - alltid "JWT" | Anger att token är en JWT.|
|`alg` | Sträng | Anger algoritmen som användes för att signera token. Exempel: "RS256" |
|`kid` | Sträng | Tumavtryck för den offentliga nyckeln som används för att signera den här token. Ut avges i både v1.0 `id_tokens`och v2.0 . |
|`x5t` | Sträng | Samma (i bruk och `kid`värde) som . Detta är dock ett äldre anspråk som endast `id_tokens` släpps ut i v1.0 för kompatibilitetsändamål. |

### <a name="payload-claims"></a>Anspråk på nyttolast

Den här listan visar de anspråk som finns i de flesta id_tokens som standard (utom om det anges).  Appen kan dock använda [valfria anspråk](active-directory-optional-claims.md) för att begära ytterligare anspråk i id_token.  Dessa kan vara `groups` allt från anspråk till information om användarens namn.

|Begär | Format | Beskrivning |
|-----|--------|-------------|
|`aud` |  Sträng, ett app-ID URI | Identifierar den avsedda mottagaren av token. I `id_tokens`är målgruppen appens program-ID som tilldelats din app i Azure-portalen. Din app ska validera det här värdet och avvisa token om värdet inte matchar. |
|`iss` |  Sträng, en STS URI | Identifierar säkerhetstokentjänsten (STS) som konstruerar och returnerar token och Azure AD-klienten där användaren autentiserades. Om token har utfärdats av v2.0-slutpunkten, slutar `/v2.0`URI i .  GUID som anger att användaren är en konsumentanvändare `9188040d-6c67-4c5b-b112-36a304b66dad`från ett Microsoft-konto är . Din app bör använda GUID-delen av anspråket för att begränsa den uppsättning klienter som kan logga in på appen, om tillämpligt. |
|`iat` |  int, en UNIX-tidsstämpel | "Utfärdat vid" anger när autentiseringen för den här token inträffade.  |
|`idp`|Sträng, vanligtvis en STS URI | Registrerar den identitetsprovider som har autentiserat subjektet för token. Det här värdet är identiskt med värdet på Utfärdaranspråket om inte användarkontot inte finns i samma klient som utfärdaren - till exempel gäster. Om anspråket inte finns betyder det att `iss` värdet på kan användas i stället.  För personliga konton som används i en organisationskontext (till exempel ett `idp` personligt konto som bjudits in till en Azure AD-klient) kan anspråket vara "live.com" eller en STS URI som innehåller Microsoft-kontoklienten `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, en UNIX-tidsstämpel | I påståendet "nbf" (ej före) anges den tid före vilken JWT INTE FÅR godkännas för behandling.|
|`exp` |  int, en UNIX-tidsstämpel | Anspråket "exp" (utgångstid) identifierar utgångstiden på eller efter vilken JWT INTE FÅR accepteras för bearbetning.  Det är viktigt att notera att en resurs kan avvisa token före denna tid också - om till exempel en ändring i autentisering krävs eller en tokenåterkallning har upptäckts. |
| `c_hash`| Sträng |Kodhhen ingår endast i ID-token när ID-token utfärdas med en OAuth 2.0-auktoriseringskod. Den kan användas för att verifiera äktheten av en auktoriseringskod. Mer information om hur du utför den här valideringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Sträng |Åtkomsttokenhh ingår endast i ID-token när ID-token utfärdas med en OAuth 2.0-åtkomsttoken. Den kan användas för att verifiera äktheten av en åtkomsttoken. Mer information om hur du utför den här valideringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure AD för att registrera data för återanvändning av token. Bör ignoreras.|
|`preferred_username` | Sträng | Det primära användarnamnet som representerar användaren. Det kan vara en e-postadress, ett telefonnummer eller ett allmänt användarnamn utan ett angivet format. Dess värde är föränderligt och kan ändras med tiden. Eftersom det är föränderligt får det här värdet inte användas för att fatta auktoriseringsbeslut. Omfattningen `profile` krävs för att ta emot detta anspråk.|
|`email` | Sträng | Anspråket `email` finns som standard för gästkonton som har en e-postadress.  Din app kan begära e-postanspråk för hanterade användare (de `email` från samma klientorganisation som resursen) med hjälp av det [valfria anspråket](active-directory-optional-claims.md).  På v2.0-slutpunkten kan din app `email` också begära OpenID Connect-scopet – du behöver inte begära både det valfria anspråket och omfånget för att få anspråket.  E-postanspråket stöder endast adresserbar e-post från användarens profilinformation. |
|`name` | Sträng | Anspråket `name` ger ett läsbart värde som identifierar tokens ämne. Värdet är inte garanterat att vara unikt, det är föränderligt och det är utformat för att användas endast för visningsändamål. Omfattningen `profile` krävs för att ta emot detta anspråk. |
|`nonce`| Sträng | Nonce matchar parametern som ingår i den ursprungliga /authorize-begäran till IDP. Om det inte matchar, bör ditt program avvisa token. |
|`oid` | Sträng, ett GUID | Den oföränderliga identifieraren för ett objekt i Microsofts identitetssystem, i det här fallet ett användarkonto. Det här ID:t identifierar användaren unikt i alla program – två olika `oid` program som loggar in samma användare får samma värde i anspråket. Microsoft Graph returnerar detta ID som `id` egenskap för ett visst användarkonto. Eftersom `oid` flera appar kan korrelera `profile` användare krävs omfånget för att ta emot det här anspråket. Observera att om en enskild användare finns i flera klienter, kommer användaren att innehålla ett annat objekt-ID i varje klient - de betraktas som olika konton, även om användaren loggar in på varje konto med samma autentiseringsuppgifter. Anspråket `oid` är ett GUID och kan inte återanvändas. |
|`roles`| Matris med strängar | Den uppsättning roller som har tilldelats den användare som loggar in. |
|`rh` | Ogenomskinlig sträng |Ett internt anspråk som används av Azure för att förnya token. Bör ignoreras. |
|`sub` | Sträng, ett GUID | Huvudbeloppet som token bekräftar information om, till exempel användaren av en app. Det här värdet är oföränderligt och kan inte tilldelas om eller återanvändas. Ämnet är en parvis identifierare - den är unik för ett visst program-ID. Om en enskild användare loggar in på två olika appar med två olika klient-ID:er får dessa appar två olika värden för ämnesanspråket. Detta kanske eller kanske inte är önskat beroende på din arkitektur och dina sekretesskrav. |
|`tid` | Sträng, ett GUID | Ett GUID som representerar Azure AD-klienten som användaren kommer från. För arbets- och skolkonton är GUID det oföränderliga klient-ID:et för den organisation som användaren tillhör. För personliga konton är `9188040d-6c67-4c5b-b112-36a304b66dad`värdet . Omfattningen `profile` krävs för att ta emot detta anspråk. |
|`unique_name` | Sträng | Innehåller ett läsbart värde som identifierar subjektet för token. Det här värdet är unikt vid en viss tidpunkt, men eftersom e-postmeddelanden och andra identifierare kan återanvändas kan det här värdet visas på andra konton och bör därför endast användas för visningsändamål. Utfärdas endast i v1.0 `id_tokens`. |
|`uti` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure för att förnya token. Bör ignoreras. |
|`ver` | Sträng, antingen 1,0 eller 2,0 | Anger versionen av id_token. |


> [!NOTE]
> V1- och V2-id_token har skillnader i mängden information som de kommer att ha enligt exemplen ovan. Versionen anger i huvudsak slutpunkten för Azure AD-plattformen från den plats där den utfärdades. [Azure AD Oauth-implementeringen](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) har utvecklats genom åren. För närvarande har vi två olika oAuth-slutpunkter för AzureAD-program. Du kan använda någon av de nya slutpunkterna som kategoriseras som v2 eller den gamla som sägs vara v1. Oauth-slutpunkterna för båda är olika. V2-slutpunkten är den nyare där vi försöker migrera alla funktioner i v1-slutpunkten och rekommendera nya utvecklare att använda v2-slutpunkten. 
> - V1: Azure Active Directory-slutpunkter:`https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: Slutpunkter för Microsoft Identity Platform:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Validera en id_token

Validera en `id_token` liknar det första steget för att [validera en åtkomsttoken](access-tokens.md#validating-tokens) - din klient bör verifiera att rätt utfärdare har skickat tillbaka token och att den inte har manipulerats. Eftersom `id_tokens` det alltid är en JWT finns det många bibliotek för att validera dessa tokens - vi rekommenderar att du använder en av dessa i stället för att göra det själv.

Om du vill validera token manuellt läser du steginformationen för [att validera en åtkomsttoken](access-tokens.md#validating-tokens). När du har validerat signaturen på token bör följande anspråk valideras i id_token (dessa kan också göras av ditt tokenvalideringsbibliotek):

* Tidsstämplar: `iat`, `nbf`och `exp` tidsstämplar bör alla falla före eller efter den aktuella tiden, beroende på vad som är lämpligt. 
* Målgrupp: `aud` anspråket ska matcha app-ID:t för ditt program.
* Nonce: `nonce` anspråket i nyttolasten måste matcha nonce-parametern som skickas till slutpunkten /authorize under den första begäran.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [åtkomsttoken](access-tokens.md)
* Anpassa anspråken i din id_token med [valfria anspråk](active-directory-optional-claims.md).
