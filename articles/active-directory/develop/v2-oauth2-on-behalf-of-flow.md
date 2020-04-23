---
title: Microsofts identitetsplattform och OAuth2.0 för flödet | Azure
description: I den här artikeln beskrivs hur du använder HTTP-meddelanden för att implementera tjänstautentisering med hjälp av flödet OAuth2.0 on-Behalf-Of.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3d3e071d5f2f181f5b17e79f2f1097394d0ebaf3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868431"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsofts identitetsplattform och OAuth 2.0 för flödet för räkning


OAuth 2.0 On-Behalf-Of flow (OBO) tjänar användningsfallet när ett program anropar ett tjänst-/webb-API, som i sin tur måste anropa ett annat tjänst-/webb-API. Tanken är att sprida den delegerade användaridentiteten och behörigheterna via förfråkomstkedjan. För att tjänsten på mellannivå ska kunna göra autentiserade begäranden till tjänsten nedströms måste den skydda en åtkomsttoken från Microsofts identitetsplattform för användarens räkning.

I den här artikeln beskrivs hur du programmerar direkt mot protokollet i ditt program.  När det är möjligt rekommenderar vi att du använder de Microsoft Authentication Libraries (MSAL) som stöds i stället för att [hämta token och anropa skyddade webb-API:er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på [exempelapparna som använder MSAL](sample-v2-code.md).

> [!NOTE]
> Från och med maj 2018 kan `id_token` vissa implicita flöde som härleds inte användas för OBO-flöde. Ensidiga appar (SPA) ska skicka en **åtkomsttoken** till en konfidentiell klient på mellannivå för att utföra OBO-flöden i stället. Mer information om vilka klienter som kan utföra OBO-samtal finns i [begränsningar](#client-limitations).

## <a name="protocol-diagram"></a>Protokolldiagram

Anta att användaren har autentiserats i ett program med hjälp av [OAuth 2.0-auktoriseringskodens bidragsflöde](v2-oauth2-auth-code-flow.md) eller ett annat inloggningsflöde. Nu har programmet en åtkomsttoken *för API A* (token A) med användarens anspråk och samtycke till åtkomst till api :et på mellannivå (API A). Nu måste API A göra en autentiserat begäran till api:et (API B) nedströms.

De steg som följer utgör OBO flödet och förklaras med hjälp av följande diagram.

![Visar OAuth2.0 för flödet för räkning](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Klientprogrammet gör en begäran till API A `aud` med token A (med ett anspråk på API A).
1. API A autentiserar till slutpunkten för utgivning av Microsoft-identitetsplattformstoken och begär en token för åtkomst till API B.
1. Slutpunkten för microsoft identity-plattformstokensutfärdande validerar API A:s autentiseringsuppgifter tillsammans med token A och utfärdar åtkomsttoken för API B (token B) till API A.
1. Token B anges av API A i auktoriseringshuvudet för begäran till API B.
1. Data från den skyddade resursen returneras av API B till API A och därifrån till klienten.

> [!NOTE]
> I det här fallet har tjänsten på mellannivå ingen användarinteraktion för att få användarens medgivande att komma åt nedströms-API:et. Därför visas alternativet att bevilja åtkomst till api:et i efterföljande led i förväg som en del av medgivandesteget under autentiseringen. Mer information om hur du konfigurerar detta för din app finns i [Hämta samtycke för programmet på mellannivå](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Begäran om tjänst-till-tjänst-åtkomsttoken

Om du vill begära en åtkomsttoken gör du ett HTTP-POST till den klientspecifika microsoft-identitetsplattformstokens slutpunkten med följande parametrar.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Det finns två ärenden beroende på om klientprogrammet väljer att skyddas av en delad hemlighet eller ett certifikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fallet: Åtkomsttokenbegäran med en delad hemlighet

När du använder en delad hemlighet innehåller en tjänst-till-tjänst-åtkomsttokenbegäran följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `grant_type` | Krävs | Typ av tokenbegäran. För en begäran med en JWT `urn:ietf:params:oauth:grant-type:jwt-bearer`måste värdet vara . |
| `client_id` | Krävs | Programmets (klient)-ID som sidan [Azure Portal - Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) har tilldelats din app. |
| `client_secret` | Krävs | Klienthemligheten som du har genererat för din app på sidan Azure Portal - Appregistreringar. |
| `assertion` | Krävs | Värdet för den token som används i begäran.  Den här token måste ha en målgrupp för appen som gör `client-id` den här OBO-begäran (appen som anges av fältet). |
| `scope` | Krävs | En utrymmesavskiljad lista över scope för tokenbegäran. Mer information finns i [scope](v2-permissions-and-consent.md). |
| `requested_token_use` | Krävs | Anger hur begäran ska bearbetas. I OBO-flödet måste värdet anges `on_behalf_of`till . |

#### <a name="example"></a>Exempel

Följande HTTP POST begär en åtkomsttoken och uppdaterar token med `user.read` omfång för webb-API:et. https://graph.microsoft.com

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

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fallet: Åtkomsttokenbegäran med ett certifikat

En tjänst-till-tjänst-åtkomsttokenbegäran med ett certifikat innehåller följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `grant_type` | Krävs | Typen av tokenbegäran. För en begäran med en JWT `urn:ietf:params:oauth:grant-type:jwt-bearer`måste värdet vara . |
| `client_id` | Krävs |  Programmets (klient)-ID som sidan [Azure Portal - Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) har tilldelats din app. |
| `client_assertion_type` | Krävs | Värdet måste `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`vara . |
| `client_assertion` | Krävs | Ett påstående (en JSON-webbtoken) som du behöver skapa och signera med certifikatet som du registrerade som autentiseringsuppgifter för ditt program. Mer information om hur du registrerar certifikatet och formatet på kontrollen finns i [certifikatuppgifter](active-directory-certificate-credentials.md). |
| `assertion` | Krävs | Värdet för den token som används i begäran. |
| `requested_token_use` | Krävs | Anger hur begäran ska bearbetas. I OBO-flödet måste värdet anges `on_behalf_of`till . |
| `scope` | Krävs | En utrymmesavgränsad lista över scope för tokenbegäran. Mer information finns i [scope](v2-permissions-and-consent.md).|

Observera att parametrarna är nästan desamma som i fallet med `client_secret` begäran av delad hemlighet `client_assertion_type` `client_assertion`förutom att parametern ersätts med två parametrar: a och .

#### <a name="example"></a>Exempel

Följande HTTP POST begär en `user.read` åtkomsttoken med omfång för webb-API:et https://graph.microsoft.com med ett certifikat.

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

## <a name="service-to-service-access-token-response"></a>Tjänst till tjänståtkomst token svar

Ett lyckat svar är ett JSON OAuth 2.0-svar med följande parametrar.

| Parameter | Beskrivning |
| --- | --- |
| `token_type` | Anger tokentypsvärdet. Den enda typ som Microsoft `Bearer`identity platform stöder är . Mer information om innehavartoken finns i [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Omfattningen av åtkomst som beviljats i token. |
| `expires_in` | Den tid, i sekunder, som åtkomsttoken är giltig. |
| `access_token` | Den begärda åtkomsttoken. Anropartjänsten kan använda den här token för att autentisera till den mottagande tjänsten. |
| `refresh_token` | Uppdateringstoken för den begärda åtkomsttoken. Anropartjänsten kan använda den här token för att begära en annan åtkomsttoken när den aktuella åtkomsttoken har upphört att gälla. Uppdateringstoken anges endast `offline_access` om scopet begärdes. |

### <a name="success-response-example"></a>Exempel på lyckat svar

I följande exempel visas ett lyckat svar på https://graph.microsoft.com en begäran om en åtkomsttoken för webb-API:et.

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
> Ovanstående åtkomsttoken är en v1.0-formaterad token. Detta beror på att token tillhandahålls baserat på den **resurs** som används. Microsoft Graph är inställd på att acceptera v1.0-token, så Microsoft identity platform producerar v1.0-åtkomsttoken när en klient begär token för Microsoft Graph. Endast program bör titta på åtkomsttoken. Klienter **får inte** inspektera dem.

### <a name="error-response-example"></a>Exempel på felsvar

Ett felsvar returneras av tokenslutpunkten när du försöker hämta en åtkomsttoken för nedströms-API:et, om nedströms-API:et har en princip för villkorlig åtkomst (till exempel multifaktorautentisering) inställd på den. Tjänsten mellannivå bör visa det här felet för klientprogrammet så att klientprogrammet kan tillhandahålla användarinteraktionen för att uppfylla principen villkorlig åtkomst.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Använd åtkomsttoken för att komma åt den skyddade resursen

Nu kan tjänsten på mellannivå använda token som förvärvats ovan för att göra autentiserade begäranden till det underordnade webb-API:et `Authorization` genom att ange token i huvudet.

### <a name="example"></a>Exempel

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Få samtycke för programmet på mellannivå

Beroende på arkitektur eller användning av ditt program kan du överväga olika strategier för att säkerställa att OBO-flödet lyckas. I samtliga fall är det slutliga målet att se till att korrekt samtycke ges så att klientappen kan anropa appen på mellannivå och att appen på mellannivå har behörighet att anropa backend-resursen.

> [!NOTE]
> Tidigare stödde inte Microsoft-kontosystemet (personliga konton) fältet "Känt klientprogram" och kunde inte heller visa kombinerat medgivande.  Detta har lagts till och alla appar i Microsofts identitetsplattform kan använda den kända klientprogrammetoden för gettign-medgivande för OBO-anrop.

### <a name="default-and-combined-consent"></a>/.standard och kombinerat samtycke

Det mellannivåprogrammet lägger till klienten i listan över kända klientprogram i manifestet och sedan kan klienten utlösa ett kombinerat medgivandeflöde för både sig själv och programmet på mellannivå. På slutpunkten för Microsoft-identitetsplattformen görs detta med hjälp av [ `/.default` scopet](v2-permissions-and-consent.md#the-default-scope). När du utlöser en medgivandeskärm med kända klientprogram och `/.default`visar medgivandeskärmen behörigheter för **både** klienten till API:et på mellannivå och begär även vilka behörigheter som krävs av API:et på mellannivå. Användaren ger sitt samtycke till båda programmen och sedan fungerar OBO-flödet.

### <a name="pre-authorized-applications"></a>Förauktoriserade ansökningar

Resurser kan ange att ett visst program alltid har behörighet att ta emot vissa scope. Detta är främst användbart för att göra anslutningar mellan en frontend-klient och en backend-resurs mer sömlösa. En resurs kan deklarera flera förauktoriserade program - alla sådana program kan begära dessa behörigheter i ett OBO-flöde och ta emot dem utan att användaren ger sitt medgivande.

### <a name="admin-consent"></a>Administratörsmedgivande

En klientadministratör kan garantera att program har behörighet att anropa sina nödvändiga API:er genom att ge administratörsgodkännande för programmet på mellannivå. För att göra detta kan administratören hitta programmet på mellannivå i sin klientorganisation, öppna sidan för behörigheter som krävs och välja att ge appen behörighet. Mer information om administratörsmedgivande finns i [dokumentationen för medgivande och behörigheter](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Användning av ett enda program

I vissa fall kanske du bara har en enda parkoppling av mellannivå och klient. I det här fallet kan det vara lättare att göra detta till ett enda program, vilket motverkar behovet av ett mellannivåprogram helt och hållet. Om du vill autentisera mellan klientdelen och webb-API:et kan du använda cookies, en id_token eller en åtkomsttoken som begärs för själva programmet. Begär sedan medgivande från det här enskilda programmet till backend-resursen.

## <a name="client-limitations"></a>Begränsningar för klienten

Om en klient använder det implicita flödet för att få en id_token och klienten också har jokertecken i en svars-URL, kan id_token inte användas för ett OBO-flöde.  Åtkomsttoken som hämtas via det implicita beviljandeflödet kan dock fortfarande lösas in av en konfidentiell klient även om den initierande klienten har en url för jokerteckensvar registrerats.

## <a name="next-steps"></a>Nästa steg

Läs mer om OAuth 2.0-protokollet och ett annat sätt att utföra service till serviceautentisering med hjälp av klientautentiseringsuppgifter.

* [OAuth 2.0-klientautentiseringsuppgifter beviljas i Microsofts identitetsplattform](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0-kodflöde i Microsofts identitetsplattform](v2-oauth2-auth-code-flow.md)
* [Använda `/.default` omfattningen](v2-permissions-and-consent.md#the-default-scope)
