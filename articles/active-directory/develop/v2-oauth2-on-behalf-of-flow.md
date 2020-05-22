---
title: Microsoft Identity Platform och OAuth 2.0 på uppdrag av Flow | Azure
titleSuffix: Microsoft identity platform
description: I den här artikeln beskrivs hur du använder HTTP-meddelanden för att implementera tjänst-till-tjänst-autentisering med hjälp av flödes data för OAuth 2.0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bcf971b56aa0dc343fdfaf34b329e49a82bba9a8
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771528"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft Identity Platform och OAuth 2,0 på uppdrag av Flow


OAuth 2,0 on-of-Flow (OBO) är ett sätt att använda det fall där ett program anropar en tjänst/webb-API, som i sin tur måste anropa en annan tjänst/webb-API. Tanken är att sprida den delegerade användar identiteten och behörigheterna via begär ande kedjan. För att mellanskikts tjänsten ska kunna göra autentiserade begär anden till den underordnade tjänsten måste den skydda en åtkomsttoken från Microsoft Identity Platform, å användarens vägnar.

Den här artikeln beskriver hur du programmerar direkt mot protokollet i ditt program.  När det är möjligt rekommenderar vi att du använder MSAL (Microsoft Authentication Libraries) i stället för att [Hämta tokens och anropa säkra webb-API: er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på de [exempel appar som använder MSAL](sample-v2-code.md).

> [!NOTE]
> Från maj till 2018 kan vissa implicita flöden `id_token` inte användas för OBO-flöde. Appar med en sida (SPAs) ska **skicka en åtkomsttoken** till en konfidentiell klient på mellan nivå för att utföra OBO-flöden i stället. Mer information om vilka klienter som kan utföra OBO-anrop finns i [begränsningar](#client-limitations).

## <a name="protocol-diagram"></a>Protokoll diagram

Anta att användaren har autentiserats i ett program med hjälp av [OAuth 2,0-auktoriseringskod som beviljar flöde](v2-oauth2-auth-code-flow.md) eller ett annat inloggnings flöde. I det här läget har programmet en åtkomsttoken *för API a* (token a) med användarens anspråk och medgivande för att komma åt mitten-nivåns webb-API (API a). Nu måste API A göra en autentiserad begäran till det underordnade webb-API: et (API B).

Stegen som följer utgör OBO-flödet och förklaras med hjälp av följande diagram.

![Visar OAuth 2.0-flöde på uppdrag](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Klient programmet skickar en begäran till API A med token A (med `aud` anspråket API a).
1. API A autentiserar till slut punkten för utfärdande av Microsoft Identity Platform-token och begär en token för åtkomst till API B.
1. Slut punkten för utfärdande av Microsoft Identity Platform-token verifierar API A: s autentiseringsuppgifter tillsammans med token A och utfärdar åtkomst-token för API B (token B) till API A.
1. Token B anges av API A i Authorization-huvudet för begäran till API B.
1. Data från den skyddade resursen returneras av API B till API A, och därifrån till klienten.

> [!NOTE]
> I det här scenariot har mellanskikts tjänsten ingen användar åtgärd för att få användarens medgivande att få åtkomst till det underordnade API: et. Alternativet för att bevilja åtkomst till underordnad API visas därför som en del av godkännande steget under autentisering. Information om hur du konfigurerar detta för din app finns i [få medgivande för program på mellan nivå](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Begäran om tjänst-till-tjänst-åtkomsttoken

Om du vill begära en åtkomsttoken gör du ett HTTP-inlägg till den klient-/regionspecifika slut punkten för Microsoft Identity Platform-token med följande parametrar.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Det finns två fall beroende på om klient programmet väljer att skyddas av en delad hemlighet eller ett certifikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fallet: begäran om åtkomsttoken med en delad hemlighet

När du använder en delad hemlighet innehåller en begäran om tjänst-till-tjänst-åtkomsttoken följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `grant_type` | Obligatorisk | Typ av Tokenbegäran. För en begäran som använder en JWT måste värdet vara `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Obligatorisk | Program-ID: t (klienten) som [Azure Portal-Appregistreringar-](https://go.microsoft.com/fwlink/?linkid=2083908) sidan har tilldelats till din app. |
| `client_secret` | Obligatorisk | Den klient hemlighet som du genererade för din app på sidan Azure Portal-Appregistreringar. |
| `assertion` | Obligatorisk | Värdet för den token som används i begäran.  Denna token måste ha en mål grupp för appen som gör denna OBO-begäran (appen avgränsad av `client-id` fältet). |
| `scope` | Obligatorisk | En blankstegsavgränsad lista över omfång för Tokenbegäran. Mer information finns i [omfattningar](v2-permissions-and-consent.md). |
| `requested_token_use` | Obligatorisk | Anger hur begäran ska bearbetas. I OBO-flödet måste värdet anges till `on_behalf_of` . |

#### <a name="example"></a>Exempel

Följande HTTP POST begär en åtkomsttoken och en uppdateringstoken med `user.read` scope för https://graph.microsoft.com webb-API: et.

```HTTP
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

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fall: åtkomsttoken för begäran med ett certifikat

En Tokenbegäran för tjänst-till-tjänst-begäran med ett certifikat innehåller följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `grant_type` | Obligatorisk | Typ av Tokenbegäran. För en begäran som använder en JWT måste värdet vara `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Obligatorisk |  Program-ID: t (klienten) som [Azure Portal-Appregistreringar-](https://go.microsoft.com/fwlink/?linkid=2083908) sidan har tilldelats till din app. |
| `client_assertion_type` | Obligatorisk | Värdet måste vara `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Obligatorisk | En kontroll (en JSON-webbtoken) som du måste skapa och signera med det certifikat som du har registrerat som autentiseringsuppgifter för ditt program. Information om hur du registrerar ditt certifikat och formatet på intyget finns i autentiseringsuppgifter för [certifikat](active-directory-certificate-credentials.md). |
| `assertion` | Obligatorisk | Värdet för den token som används i begäran. |
| `requested_token_use` | Obligatorisk | Anger hur begäran ska bearbetas. I OBO-flödet måste värdet anges till `on_behalf_of` . |
| `scope` | Obligatorisk | En blankstegsavgränsad lista över omfång för Tokenbegäran. Mer information finns i [omfattningar](v2-permissions-and-consent.md).|

Observera att parametrarna är nästan desamma som i fallet med delad hemlighet, förutom att `client_secret` parametern ersätts av två parametrar: a `client_assertion_type` och `client_assertion` .

#### <a name="example"></a>Exempel

Följande HTTP POST begär en åtkomsttoken med `user.read` scope för https://graph.microsoft.com webb-API: et med ett certifikat.

```HTTP
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

## <a name="service-to-service-access-token-response"></a>Svar på tjänst till tjänst åtkomst-token

Ett lyckat svar är ett JSON OAuth 2,0-svar med följande parametrar.

| Parameter | Beskrivning |
| --- | --- |
| `token_type` | Anger värdet för token-typ. Den enda typ som stöds av Microsoft Identity Platform är `Bearer` . Mer information om Bearer-token finns i [OAuth 2,0 Authorization Framework: Bearer token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Omfattningen av åtkomst som beviljats i token. |
| `expires_in` | Hur lång tid i sekunder som åtkomsttoken är giltig. |
| `access_token` | Den begärda åtkomsttoken. Anrops tjänsten kan använda denna token för att autentisera till den mottagande tjänsten. |
| `refresh_token` | Uppdateringstoken för den begärda åtkomsttoken. Anrops tjänsten kan använda denna token för att begära en annan åtkomsttoken när den aktuella åtkomsttoken upphör att gälla. Uppdateringstoken anges endast om `offline_access` omfånget begärdes. |

### <a name="success-response-example"></a>Exempel på slutfört svar

I följande exempel visas ett lyckat svar på en begäran om en åtkomsttoken för https://graph.microsoft.com webb-API: et.

```json
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
> Ovanstående åtkomsttoken är en v 1.0-formaterad token. Detta beror på att token tillhandahålls baserat på den **resurs** som nås. Microsoft Graph har kon figurer ATS för att acceptera v 1.0-token, så Microsoft Identity Platform skapar v 1.0-åtkomsttoken när en klient begär token för Microsoft Graph. Endast program bör titta på åtkomsttoken. Klienterna **får inte** inspektera dem.

### <a name="error-response-example"></a>Exempel på fel svar

Ett felsvar returneras av token-slutpunkten vid försök att hämta en åtkomsttoken för underordnat API, om det underordnade API: t har en princip för villkorlig åtkomst (till exempel [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)) som angetts i den. Den mellanliggande tjänsten ska visa det här felet för klient programmet så att klient programmet kan ge användaren interaktion för att uppfylla principen för villkorlig åtkomst.

```json
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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Använd åtkomsttoken för att få åtkomst till den skyddade resursen

Nu kan den mellanliggande tjänsten använda den token som hämtades ovan för att göra autentiserade begär anden till den underordnade webb-API: n, genom att ange token i `Authorization` rubriken.

### <a name="example"></a>Exempel

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Få medgivande för program på mellan nivå

Beroende på arkitekturen eller användningen av ditt program kan du överväga olika strategier för att se till att OBO-flödet lyckas. I samtliga fall är det slutgiltiga målet att se till att det är rätt beviljat, så att klient programmet kan anropa den mellanliggande appen och att appen mellan nivå har behörighet att anropa backend-resursen.

> [!NOTE]
> Tidigare hade Microsoft-konto systemet (personliga konton) inte stöd för fältet "känt klient program" och kan inte heller Visa kombinerat medgivande.  Detta har lagts till och alla appar i Microsoft Identity Platform kan använda den kända klient program metoden för att få medgivande för OBO-anrop.

### <a name="default-and-combined-consent"></a>/.default och kombinerat medgivande

Programmet på mellan nivå lägger till klienten i listan med kända klient program i manifestet, och klienten kan sedan utlösa ett kombinerat godkännande flöde för både sig och mellan nivå programmet. På Microsoft Identity Platform-slutpunkten görs detta med hjälp av [ `/.default` omfånget](v2-permissions-and-consent.md#the-default-scope). När du utlöser en medgivande skärm med kända klient program och `/.default` visar medgivande skärmen behörigheter för **både** klienten och API: et på mellan nivå, och även begäran om vilka behörigheter som krävs av API: t på mellan nivå. Användaren ger tillåtelse till båda programmen och sedan fungerar OBO-flödet.

### <a name="pre-authorized-applications"></a>Förauktoriserade program

Resurser kan indikera att ett visst program alltid har behörighet att ta emot vissa omfattningar. Detta är främst användbart för att upprätta anslutningar mellan en klient dels klient och en backend-resurs som är mer sömlös. En resurs kan deklarera flera förauktoriserade program – alla sådana program kan begära dessa behörigheter i ett OBO flöde och ta emot dem utan att användaren tillfrågas.

### <a name="admin-consent"></a>Administratörsmedgivande

En innehavaradministratör kan garantera att program har behörighet att anropa de API: er som krävs genom att ge administrativt medgivande för program mellan nivåer. Administratören kan göra detta genom att hitta program på mellan nivå i sin klient, öppna sidan behörigheter som krävs och välja att ge appen behörighet. Läs mer om administratörs medgivande i dokumentationen för [medgivande och behörighet](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Användning av ett enda program

I vissa fall kan du bara ha en enda koppling av mellan nivå och klient del. I det här scenariot kan det vara lättare att göra detta till ett enda program, vilket avvisar behovet av ett program mellan flera nivåer helt och hållet. Om du vill autentisera mellan klient delen och webb-API: et kan du använda cookies, ett id_token eller en åtkomsttoken som begärs för själva programmet. Sedan kan du begära medgivande från det här enskilda programmet till backend-resursen.

## <a name="client-limitations"></a>Klient begränsningar

Om en klient använder det implicita flödet för att hämta en id_token och klienten även har jokertecken i en svars-URL, kan id_token inte användas för ett OBO flöde.  Åtkomst-token som förvärv ATS via det implicita tilldelnings flödet kan dock fortfarande lösas av en konfidentiell klient även om den initierande klienten har svars-URL: en som jokertecken har registrerats.

## <a name="next-steps"></a>Nästa steg

Läs mer om OAuth 2,0-protokollet och ett annat sätt att utföra tjänst-till-tjänst-autentisering med hjälp av klientautentiseringsuppgifter.

* [Beviljande av OAuth 2,0-klientautentiseringsuppgifter i Microsoft Identity Platform](v2-oauth2-client-creds-grant-flow.md)
* [Kod flöde för OAuth 2,0 i Microsoft Identity Platform](v2-oauth2-auth-code-flow.md)
* [Använda `/.default` omfånget](v2-permissions-and-consent.md#the-default-scope)
