---
title: Tokenreferens för Azure Active Directory-ID | Microsoft Docs
description: Lär dig hur du använder id_tokens som orsakats av slutpunkterna v1.0 och v2.0 av Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 687b25ea5d792edf2f582c9929a0ae5f0c2426db
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442081"
---
# <a name="id-tokens"></a>ID-tokens

`id_tokens` skickas till klientprogrammet som en del av en [OpenID Connect](v1-protocols-openid-connect-code.md) flöde. De kan skickas längs sida eller i stället för en åtkomsttoken och används av klienten för att autentisera användaren.

## <a name="using-the-idtoken"></a>Med hjälp av id_token

ID-token som ska användas för att verifiera att en användare är den som de påstår sig vara och få ytterligare användbar information om dem – det ska inte användas för auktorisering i stället för en [åtkomsttoken](access-tokens.md). De anspråk som den innehåller kan användas för UX i ditt program, uppdatering av en databas och åtkomst till klientprogrammet.

## <a name="claims-in-an-idtoken"></a>Anspråk i en id_token

`id_tokens` för ett Microsoft-identitet är [JWTs](https://tools.ietf.org/html/rfc7519), vilket innebär att de består av en rubrik, nyttolast och signatur del. Du kan använda rubrik och signaturen för att kontrollera att token, medan nyttolasten innehåller information om användaren som begärs av klienten. Om inget annat anges visas alla anspråk som visas här i både v1.0 och v2.0-token.

### <a name="v10"></a>V1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Visa den här v1.0 exempel token i [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Visa den här v2.0 exempel token i [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Huvud-anspråk

|Begäran | Format | Beskrivning |
|-----|--------|-------------|
|`typ` | String - always "JWT" | Anger att token är en JWT.|
|`alg` | String | Anger den algoritm som användes för att signera token. Exempel: "RS256" |
|`kid` | String | Tumavtryck för den offentliga nyckeln som används för att signera den här token. Släpps i både v1.0 och v2.0 `id_tokens`. |
|`x5t` | String | Samma (i användning och värde) som `kid`. Detta är ett äldre anspråk som släpps endast i v1.0 `id_tokens` för kompatibilitet. |

### <a name="payload-claims"></a>Nyttolasten anspråk

|Begäran | Format | Beskrivning |
|-----|--------|-------------|
|`aud` |  Sträng, ett App-ID-URI | Identifierar den avsedda mottagaren av token. I `id_tokens`, målgruppen är appens program-ID som tilldelats din app i Azure-portalen. Din app ska verifiera det här värdet och avvisa token om värdet inte matchar. |
|`iss` |  Sträng, en STS-URI | Identifierar den säkerhetstokentjänst (STS) som skapar och återställer token och Azure AD-klient där användaren autentiserades. Om token har utfärdats av v2.0-slutpunkten, URI: N ska avslutas i `/v2.0`.  Det GUID som anger att användaren är en konsument användare från ett Microsoft-konto är `9188040d-6c67-4c5b-b112-36a304b66dad`. Din app ska använda GUID-del av kravet för att minska antalet klienter som kan logga in på appen, om tillämpligt. |
|`iat` |  int, en UNIX-tidsstämpel | ”Utfärdat till” anger när autentisering för den här token inträffade.  |
|`idp`|String, usually an STS URI | Registrerar den identitetsprovider som har autentiserat subjektet för token. Det här värdet är identiskt med utfärdaren anspråkets värde såvida inte användarkontot inte i samma klient som utfärdaren - gäster, till exempel. Om anspråket inte är tillgänglig, innebär det att värdet för `iss` kan användas i stället.  För personliga konton som används i en organisations kontext (till exempel ett personligt konto bjudits in till en Azure AD-klient), den `idp` anspråk kan vara ”live.com” eller en STS-URI som innehåller Microsoft-kontots klientorganisation `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, en UNIX-tidsstämpel | ”Nbf” (inte före)-anspråket identifierar den tid som JWT inte måste godkännas för bearbetning.|
|`exp` |  int, en UNIX-tidsstämpel | ”Exp” (upphör att gälla)-anspråket identifierar den upphör att gälla på eller efter vilket den JWT måste inte accepteras för bearbetning.  Det är viktigt att Observera att en resurs kan avvisa token innan du nu även - om till exempel en ändring i autentisering krävs eller en återkallningen av token har identifierats. |
| `c_hash`| String |Kod hash ingår i ID-token endast när ID-token utfärdas med en OAuth 2.0-auktoriseringskod. Det kan användas för att bekräfta en auktoriseringskod är äkta. Mer information om hur du utför den här verifieringen, finns i den [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String |Åtkomst-token-hash som ingår i ID token endast när den ID-token som utfärdas med en OAuth 2.0-åtkomsttoken. Det kan användas för att bekräfta en åtkomst-token är äkta. Mer information om hur du utför den här verifieringen, finns i den [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Täckande sträng | Ett internt anspråk som används av Azure AD för att registrera data för token återanvändning. Ska ignoreras.|
|`preferred_username` | String | Primära användarnamnet som representerar användaren. Det kan vara en e-postadress, telefonnummer eller ett allmänt användarnamn utan angivet format. Värdet är föränderliga och kan ändras över tid. Eftersom det är föränderliga, måste det här värdet inte användas vid auktoriseringsbeslut. Den `profile` omfattning krävs för att ta emot det här anspråket.|
|`email` | String | Den `email` finns finns som standard gästkonton som har en e-postadress.  Din app kan begära e-anspråk för hanterade användare (de från samma klient som resursen) med hjälp av den `email` [valfria anspråk](active-directory-optional-claims.md).  På v2.0-slutpunkten din app kan också begära den `email` OpenID Connect scope - du behöver inte begära både valfria anspråk och omfånget för att få anspråket.  E-anspråket har endast stöd för adresserbara e-post från användarens profilinformation. |
|`name` | String | Den `name` anspråk innehåller ett läsbart värde som identifierar föremål för token. Värdet är inte säkert att vara unikt, det är föränderliga och den har utformats för att användas endast för visning. Den `profile` omfattning krävs för att ta emot det här anspråket. |
|`nonce`| String | Temporärt ID matchar den parameter som ingår i ursprungligt / godkänna begäran om att IDP: N. Om det inte matchar bör ditt program avvisa token. |
|`oid` | Sträng, ett GUID | Oföränderligt ID för ett objekt i Microsoft identitetssystem, i det här fallet ett användarkonto. Detta ID identifierar användaren för program – två olika program som loggar in samma användare får samma värde i den `oid` anspråk. Microsoft Graph returnerar detta ID som den `id` -egenskapen för ett givet användarkonto. Eftersom den `oid` tillåter flera appar att korrelera användare, den `profile` omfattning krävs för att ta emot det här anspråket. Observera att om en enskild användare finns i flera klienter, användaren innehåller ett annat objekt-ID i varje klient – de anses vara olika konton, även om användaren loggar in på varje konto med samma autentiseringsuppgifter. |
|`rh` | Täckande sträng |Ett internt anspråk som används av Azure för att verifiera token. Ska ignoreras. |
|`sub` | Sträng, ett GUID | Huvudkontot som token kontrollerar information, t.ex användare av en app. Det här värdet kan inte ändras och det går inte att tilldela om eller återanvänds. Ämnet är en av pairwise identifierare – det är unikt för ett visst program-ID. Om en användare loggar in på två olika appar med hjälp av två olika klient-ID, därför får apparna två olika värden för ämne anspråket. Detta kan eller inte är önskvärt beroende på dina krav arkitektur och sekretess. |
|`tid` | Sträng, ett GUID | Ett GUID som representerar den Azure AD-klient som användaren är från. För arbets- och skolkonton-konton är GUID oföränderligt klient-ID för den organisation som användaren tillhör. Värdet är för personliga konton `9188040d-6c67-4c5b-b112-36a304b66dad`. Den `profile` omfattning krävs för att ta emot det här anspråket. |
|`unique_name` | String | Innehåller ett läsbart värde som identifierar subjektet för token. Det här värdet är inte säkert att vara unika inom en klient och bör endast användas för visning. Endast utfärdats i v1.0 `id_tokens`. |
|`uti` | Täckande sträng | Ett internt anspråk som används av Azure för att verifiera token. Ska ignoreras. |
|`ver` | Sträng, 1.0 eller 2.0 | Anger versionen av id_token. |

## <a name="validating-an-idtoken"></a>Verifiera en id_token

Verifiera en `id_token` liknar det första steget i [verifierar en åtkomsttoken](access-tokens.md#validating-tokens) – klienten bör kontrollera att rätt utfärdaren skickade tillbaka token och att den inte har ändrats. Eftersom `id_tokens` är alltid en JWT, många bibliotek finns för att verifiera dessa token – vi rekommenderar att du använder något av dessa i stället göra det själv. 

För att manuellt verifiera token kan se vilka steg i [verifierar en åtkomsttoken](access-tokens.md#validating-tokens). När du har validerat signaturen på token, bör följande anspråken verifieras i id_token (de kan också göras av biblioteket tokenvalidering):

* Tidsstämplar: den `iat`, `nbf`, och `exp` tidsstämplar bör alla ligga före eller efter den aktuella tiden efter behov. 
* Målgrupp: den `aud` anspråk måste matcha det app-ID för ditt program.
* Nonce: den `nonce` anspråk i nyttolasten måste matcha den tillfälligt parameter som överförs till den / tillåta slutpunkt för under den ursprungliga begäran.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azure AD-åtkomsttoken](access-tokens.md)
