---
title: Microsoft identity-plattformen och OAuth2.0 On-Behalf-Of-flöde | Azure
description: Den här artikeln beskriver hur du använder HTTP-meddelanden för att implementera tjänst till tjänst-autentisering med hjälp av OAuth2.0 On-Behalf-Of-flöde.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4de33bb02a008d6b394055c64119ac2a4fbc4d9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276056"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft identity-plattformen och OAuth 2.0 Behalf flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

OAuth 2.0-Behalf-flödet (OBO) fungerar användningsfall där ett program anropar en tjänst/webb-API, som i sin tur måste anropa en annan tjänst/webb-API. Tanken är att sprida delegerade användaren identitets- och behörigheter genom begärandekedjan. För mellannivå-tjänsten ska göra autentiserade begäranden till den underordnade tjänsten, måste den säkra en åtkomsttoken från Microsoft identity-plattformen för användarens räkning.

> [!NOTE]
>
> - Microsoft identity-plattformen slutpunkt stöder inte alla scenarier och funktioner. Läs mer om för att avgöra om du ska använda Microsoft identity-plattformen endpoint, [plattformsbegränsningar för Microsoft identity](active-directory-v2-limitations.md). Mer specifikt stöds kända klientprogram inte för appar med Microsoft-konto (MSA) och Azure AD målgrupper. Därför fungerar inte ett vanligt mönster för medgivande för OBO för klienter som loggar in både personliga- och arbets-eller skolkonto. Mer information om hur du hanterar det här steget i flödet finns [få medgivande för mellannivå programmet](#gaining-consent-for-the-middle-tier-application).
> - Från och med maj 2018 vissa implicit flöde härledda `id_token` kan inte användas för OBO-flöde. Ensidesappar (SPA) ska ha genomgått en **åtkomst** token i en mellannivå konfidentiell klient för att utföra OBO flödar i stället. Mer information om vilka klienter som kan utföra OBO-anrop, finns i [begränsningar](#client-limitations).

## <a name="protocol-diagram"></a>Protokollet diagram

Anta att användaren har autentiserats på ett program med hjälp av den [flöde beviljat med OAuth 2.0-auktoriseringskod](v2-oauth2-auth-code-flow.md). Programmet har nu en åtkomsttoken *för API-A* (token A) med det användar- och medgivande till att komma åt mellannivå webb-API (API-A). Nu kan måste API A göra en autentiserad begäran till underordnade webb-API (API-B).

De steg som följer utgör OBO-flödet och beskrivs med hjälp av följande diagram.

![OAuth2.0 On-Behalf-Of-flöde](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Klientprogrammet skickar en begäran till API A med token A (med en `aud` anspråk av API-A).
1. API-A autentiserar till Microsoft identity-plattformen utfärdande-slutpunkten och begär en token för att komma åt API B.
1. Microsoft identity-plattformen utfärdande slutpunkt validerar API A autentiseringsuppgifter med token A och utfärdar en åtkomsttoken för API-B (token B).
1. Token B har angetts i auktoriseringshuvudet för begäran till API B.
1. Data från den skyddade resursen returneras av API B.

> [!NOTE]
> I det här scenariot har mellannivå-tjänsten inga användaråtgärder för att hämta användarens medgivande till att få åtkomst till underordnade API. Därför visas kan ge åtkomst till underordnade API gång som en del av samtycke steg under autentiseringen. Läs hur du ställer in detta för din app i [få medgivande för mellannivå programmet](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Tjänst-till-tjänst begäran om åtkomsttoken

Skapa en HTTP POST till klientspecifik v2.0-slutpunkten med följande parametrar för token för att begära en åtkomst-token.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Det finns två fall beroende på om klientprogrammet väljer att skyddas av en delad hemlighet eller ett certifikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fall: Begäran om åtkomsttoken med en delad hemlighet

När du använder en delad hemlighet, innehåller en tjänst-till-tjänst begäran om åtkomsttoken följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `grant_type` | Krävs | Typ av begäran om åtkomsttoken. Värdet måste vara begäran med hjälp av en JWT `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Krävs | Programmet (klient)-ID: T som [Azure portal – appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan har tilldelats din app. |
| `client_secret` | Krävs | Klienthemlighet som du skapade för din app i Azure portal - registreringar appsidan. |
| `assertion` | Krävs | Värdet för den token som används i begäran. |
| `scope` | Krävs | Ett blanksteg avgränsade lista med omfattningar för token-begäran. Mer information finns i [scope](v2-permissions-and-consent.md). |
| `requested_token_use` | Krävs | Anger hur begäran ska bearbetas. I OBO-flödet, måste värdet anges till `on_behalf_of`. |

#### <a name="example"></a>Exempel

Följande HTTP POST begär en åtkomsttoken och uppdateringstoken med `user.read` omfång för den https://graph.microsoft.com webb-API.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fall: Begäran om åtkomsttoken med ett certifikat

En begäran för tjänst-till-tjänst åtkomst-token med ett certifikat innehåller följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `grant_type` | Krävs | Typ av token begäran. Värdet måste vara begäran med hjälp av en JWT `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Krävs |  Programmet (klient)-ID: T som [Azure portal – appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan har tilldelats din app. |
| `client_assertion_type` | Krävs | Värdet måste vara `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Krävs | Ett intyg (en JSON-webbtoken) som du behöver för att skapa och signera med certifikatet du registrerad som autentiseringsuppgifter för ditt program. Läs hur du registrerar ditt certifikat och format för kontrollen i [certifikat autentiseringsuppgifter](active-directory-certificate-credentials.md). |
| `assertion` | Krävs | Värdet för den token som används i begäran. |
| `requested_token_use` | Krävs | Anger hur begäran ska bearbetas. I OBO-flödet, måste värdet anges till `on_behalf_of`. |
| `scope` | Krävs | En blankstegsavgränsad lista med omfattningar för token-begäran. Mer information finns i [scope](v2-permissions-and-consent.md).|

Observera att parametrarna är nästan samma sätt som när det gäller begäran från delad hemlighet utom som den `client_secret` parameter har ersatts av två parametrar: en `client_assertion_type` och `client_assertion`.

#### <a name="example"></a>Exempel

Följande HTTP POST begär en åtkomsttoken med `user.read` omfång för den https://graph.microsoft.com webb-API med ett certifikat.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Tjänsten access token svar

Ett lyckat svar är ett JSON OAuth 2.0-svar med följande parametrar.

| Parameter | Beskrivning |
| --- | --- |
| `token_type` | Anger typ tokenu värdet. Den enda skriver som Microsoft identity-plattformen stöder är `Bearer`. Mer information om ägartoken, finns det [Framework för OAuth 2.0-auktorisering: Ägar-Token användning (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Omfattning åtkomst beviljas i token. |
| `expires_in` | Hur lång tid i sekunder som den åtkomst-token är giltig. |
| `access_token` | Den begärda åtkomst-token. Anropa tjänsten kan använda denna token för att autentisera till den mottagande tjänsten. |
| `refresh_token` | Uppdateringstoken för den begärda åtkomst-token. Anropa tjänsten kan använda denna token för att begära en annan åtkomsttoken när den aktuella åtkomst-token upphör att gälla. Uppdateringstoken finns bara om den `offline_access` omfång begärdes. |

### <a name="success-response-example"></a>Exempel för lyckade svar

I följande exempel visas ett lyckat svar på en begäran om en åtkomst-token för den https://graph.microsoft.com webb-API.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> Ovanstående åtkomsttoken är en v1.0-formaterad token. Det beror på att token har angetts baserat på resursen ifråga. Microsoft Graph begär v1.0 token, så Microsoft identity-plattformen ger v1.0 åtkomsttoken när en klient begär token för Microsoft Graph. De program som ska titta på åtkomsttoken. Klienterna behöver inte inspektera dem.

### <a name="error-response-example"></a>Fel svar, exempel

Ett felsvar returneras av tokenslutpunkten vid försök att hämta en åtkomsttoken för den underordnade API om underordnade API: et har en princip för villkorlig åtkomst (till exempel multifaktorautentisering) angetts för den. Tjänsten mellannivå bör ge det här felet till klientprogrammet så att klientprogrammet kan ange användarinteraktion för att uppfylla principen för villkorlig åtkomst.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Använd åtkomsttoken för att komma åt den skyddade resursen

Tjänsten mellannivå kan nu använda den token som anskaffats ovan för att göra autentiserade begäranden till den underordnade webben-API, genom att ange token i den `Authorization` rubrik.

### <a name="example"></a>Exempel

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Få ditt medgivande för mellannivå-programmet

Beroende på målgruppen för ditt program, kan du olika strategier för att säkerställa att OBO-flödet har lyckats. I samtliga fall är målet att säkerställa rätt tillstånd ges. Hur detta sker dock beror på vilka användare ditt program har stöd för. 

### <a name="consent-for-azure-ad-only-applications"></a>Medgivande för Azure endast AD-program

#### <a name="default-and-combined-consent"></a>/.default och kombinerade medgivande

Den traditionella metoden ”kända klientprogram” räcker för program som behöver bara logga in arbets- eller skolkonton. Mellannivå-programmet lägger till klienten i listan över kända klienter program i manifestet och sedan klienten kan utlösa en kombinerad godkännandeflöde för både själva och mellannivå-programmet. Detta görs på v2.0-slutpunkten med hjälp av den [ `/.default` omfång](v2-permissions-and-consent.md#the-default-scope). När du utlöser en godkännandeskärmen med kända klientprogram och `/.default`, godkännandeskärmen visas behörigheter för både klienten mellannivå-API, och också begära behörigheterna som krävs av mellannivå-API. Användaren anger medgivande för båda programmen och sedan OBO flödet fungerar.

För tillfället har inte stöd för personliga Microsoft-kontosystemet kombinerade medgivande och så den här metoden fungerar inte för appar som vill speciellt logga in personliga konton. Personliga Microsoft-konton som används som gästkonton i en klient hanteras med hjälp av Azure AD-system och kan gå igenom kombinerade medgivande.

#### <a name="pre-authorized-applications"></a>Förauktoriserade program

En funktion i application-portalen är ”förauktoriserade program”. På så sätt kan indikerar en resurs att ett visst program alltid har behörighet att ta emot vissa omfång. Detta är främst användbart för att skapa anslutningar mellan en frontend-klient och en backend-resurs smidigare. En resurs kan deklarera flera förauktoriserade program – alla sådana program kan begära dessa behörigheter i en OBO flow och tar emot dem utan att användaren som ger sitt medgivande.

#### <a name="admin-consent"></a>Administratörsmedgivande

En klientadministratör kan garantera att program har behörighet att anropa sina nödvändiga API: er genom att tillhandahålla administratörens godkännande för mellannivå-programmet. Om du vill göra detta måste administratören hitta mellannivå-program i deras klienter, öppna sidan behörigheter som krävs för och väljer att ge behörighet för appen. Läs mer om administratörens godkännande i den [medgivande och behörigheter dokumentation](v2-permissions-and-consent.md).

### <a name="consent-for-azure-ad--microsoft-account-applications"></a>Medgivande för Azure AD + program för Microsoft-konto

På grund av begränsningar i behörighetsmodellen för personliga konton och bristen på en styrande klient skiljer medgivande kraven för personliga konton sig lite från Azure AD. Det finns ingen klient för att kunna tillhandahålla klienttäckande medgivande för eller är det möjligt att göra kombinerade medgivande. Därför Observera andra strategier för närvarande själva – att dessa fungerar för program som bara behöver stöd för Azure AD-konton.

#### <a name="use-of-a-single-application"></a>Användning av ett program

I vissa situationer kan du bara ha en enda parkoppling av mellannivå och frontend-klienten. I det här scenariot kan det vara lättare att göra detta med ett enda program, vilket eliminerar behovet av ett program på mellannivå helt och hållet. Du kan använda cookies, en id_token eller en åtkomsttoken begärs för programmet för att autentisera mellan klient- och webb-API. Begär godkännande från den här enda program till backend-resursen.

## <a name="client-limitations"></a>Klientbegränsningar

Om en klient använder det implicita flödet för att hämta en id_token och klienten har också jokertecken i en svars-URL, kan id_token inte användas för en OBO-flöde.  Dock kan åtkomsttoken som köpts via implicit beviljande av flödet fortfarande lösas in av en konfidentiell klient även om den initierande klienten har ett jokertecken svars-URL som registrerats.

## <a name="next-steps"></a>Nästa steg

Läs mer om OAuth 2.0-protokollet och ett annat sätt att utföra tjänst till tjänst-autentisering via klientautentiseringsuppgifter.

* [Beviljande av autentiseringsuppgifter för OAuth 2.0-klient i Microsoft identity-plattformen](v2-oauth2-client-creds-grant-flow.md)
* [Flöde för OAuth 2.0-kod i Microsoft identity-plattformen](v2-oauth2-auth-code-flow.md)
* [Med hjälp av den `/.default` omfång](v2-permissions-and-consent.md#the-default-scope)
