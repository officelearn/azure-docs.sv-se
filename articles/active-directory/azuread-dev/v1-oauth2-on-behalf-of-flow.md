---
title: Autentisering från service till tjänst med OAuth2.0 för flödet | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder HTTP-meddelanden för att implementera autentisering från tjänst till tjänst med flödet OAuth2.0 on-Behalf-Of.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: a301029f30a77f4e62ad3529aac488a81c12566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154533"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Service-till-tjänst-anrop som använder delegerad användaridentitet i flödet On-Behalf-Of

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

OAuth 2.0 On-Behalf-Of (OBO) flödet gör det möjligt för ett program som anropar en tjänst eller webb-API att skicka användarautentisering till en annan tjänst eller webb-API. OBO-flödet sprider den delegerade användaridentiteten och behörigheterna via förfråkomsskedjan. För att tjänsten på mellannivå ska kunna göra autentiserade begäranden till den underordnade tjänsten måste den skydda en åtkomsttoken från Azure Active Directory (Azure AD) för användarens räkning.

> [!IMPORTANT]
> Från och med maj 2018 kan en `id_token` inte användas för flödet On-Behalf-Of.  Ensidiga appar (SPA) måste skicka en åtkomsttoken till en konfidentiell klient på mellannivå för att utföra OBO-flöden. Mer information om de klienter som kan utföra on-behalf-of-samtal finns i [begränsningar](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>Flödesdiagram för att komma till 2000-2-0

OBO-flödet startar efter att användaren har autentiserats i ett program som använder [tilldelningskodsflödet OAuth 2.0](v1-protocols-oauth-code.md). Då skickar programmet en åtkomsttoken (token A) till API (api A) på mellannivå som innehåller användarens anspråk och samtycker till åtkomst till API A. Därefter gör API A en autentiserat begäran till API (API B) nedströms.

Dessa steg utgör flödet On-Behalf-Of: ![Visar stegen i flödet OAuth2.0 för den skull](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Klientprogrammet gör en begäran till API A med token A.
1. API A autentiserar till slutpunkten för Azure AD-tokenutfärdande och begär en token för åtkomst till API B.
1. Slutpunkten för Azure AD-tokenutfärdande validerar API A:s autentiseringsuppgifter med token A och utfärdar åtkomsttoken för API B (token B).
1. Begäran till API B innehåller token B i auktoriseringshuvudet.
1. API B returnerar data från den skyddade resursen.

>[!NOTE]
>Målgruppsanspråket i en åtkomsttoken som används för att begära en token för en underordnad tjänst måste vara ID för tjänsten som gör OBO-begäran. Token måste också signeras med azure Active Directory global signeringsnyckel (som är standard för program som registrerats via **Appregistreringar** i portalen).

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrera programmet och tjänsten i Azure AD

Registrera både mellannivåtjänsten och klientprogrammet i Azure AD.

### <a name="register-the-middle-tier-service"></a>Registrera tjänsten på mellannivå

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I det övre fältet väljer du ditt konto och tittar under **kataloglistan** för att välja en Active Directory-klient för ditt program.
1. Välj **Fler tjänster** i den vänstra rutan och välj Azure Active **Directory**.
1. Välj **Appregistreringar** och sedan **Ny registrering**.
1. Ange ett eget namn för programmet och välj programtypen.
1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
1. Ange omdirigerings-URI:n till bas-URL:en.
1. Välj **Registrera** för att skapa programmet.
1. Generera en klienthemlighet innan du avslutar Azure-portalen.
1. I Azure-portalen väljer du ditt program och väljer **Certifikat & hemligheter**.
1. Välj **Ny klienthemlighet** och lägg till en hemlighet med en varaktighet på antingen ett år eller två år.
1. När du sparar den här sidan visar Azure-portalen det hemliga värdet. Kopiera och spara det hemliga värdet på en säker plats.

> [!IMPORTANT]
> Du behöver hemligheten för att konfigurera programinställningarna i implementeringen. Det här hemliga värdet visas inte igen och det kan inte hämtas på något annat sätt. Spela in den så snart den visas i Azure-portalen.

### <a name="register-the-client-application"></a>Registrera klientprogrammet

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I det övre fältet väljer du ditt konto och tittar under **kataloglistan** för att välja en Active Directory-klient för ditt program.
1. Välj **Fler tjänster** i den vänstra rutan och välj Azure Active **Directory**.
1. Välj **Appregistreringar** och sedan **Ny registrering**.
1. Ange ett eget namn för programmet och välj programtypen.
1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
1. Ange omdirigerings-URI:n till bas-URL:en.
1. Välj **Registrera** för att skapa programmet.
1. Konfigurera behörigheter för ditt program. I **API-behörigheter**väljer du **Lägg till en behörighet** och sedan Mina **API:er**.
1. Skriv namnet på mellannivåtjänsten i textfältet.
1. Välj **Välj behörigheter** och välj sedan ** \<Access-tjänstnamn>**.

### <a name="configure-known-client-applications"></a>Konfigurera kända klientprogram

I det här fallet måste tjänsten på mellannivå hämta användarens medgivande för att komma åt det underordnade API:et utan användarinteraktion. Alternativet att bevilja åtkomst till api:et i efterföljande led måste visas på framsidan som en del av medgivandesteget under autentiseringen.

Följ stegen nedan för att uttryckligen binda klientappens registrering i Azure AD med registreringen på mellannivå. Den här åtgärden sammanfogar det medgivande som krävs av både klienten och mellannivån till en enda dialogruta.

1. Gå till tjänstregistreringen på mellannivå och välj **Manifest** för att öppna manifestredigeraren.
1. Leta `knownClientApplications` reda på matrisegenskapen och lägg till klient-ID:et för klientprogrammet som ett element.
1. Spara manifestet genom att välja **Spara**.

## <a name="service-to-service-access-token-request"></a>Begäran om tjänst-till-tjänst-åtkomsttoken

Om du vill begära en åtkomsttoken gör du ett HTTP-POST till den klientspecifika Azure AD-slutpunkten med följande parametrar:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Klientprogrammet skyddas antingen av en delad hemlighet eller av ett certifikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fallet: Åtkomsttokenbegäran med en delad hemlighet

När du använder en delad hemlighet innehåller en tjänst-till-tjänst-åtkomsttokenbegäran följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| grant_type |krävs | Typen av tokenbegäran. En OBO-begäran använder en JSON Web Token (JWT) så värdet måste vara **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| Påstående |krävs | Värdet för åtkomsttoken som används i begäran. |
| client_id |krävs | App-ID:t som tilldelats den anropande tjänsten under registreringen med Azure AD. Om du vill hitta app-ID:n i Azure-portalen väljer du **Active Directory,** väljer katalogen och väljer sedan programnamnet. |
| client_secret |krävs | Nyckeln som registrerats för anropande tjänsten i Azure AD. Detta värde borde ha noterats vid registreringstillfället. |
| resource |krävs | App-ID-URI för den mottagande tjänsten (säker resurs). Om du vill hitta app-ID-URI i Azure-portalen väljer du **Active Directory** och väljer katalogen. Välj programnamnet, välj **Alla inställningar**och välj sedan **Egenskaper**. |
| requested_token_use |krävs | Anger hur begäran ska bearbetas. I flödet För den för första heten måste värdet **vara on_behalf_of**. |
| omfång |krävs | En utrymmesavskiljad lista över scope för tokenbegäran. För OpenID Connect måste scopet **openid** anges.|

#### <a name="example"></a>Exempel

Följande HTTP POST begär en https://graph.microsoft.com åtkomsttoken för webb-API:et. Identifierar `client_id` tjänsten som begär åtkomsttoken.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.ewogICJhdWQiOiAiaHR0cHM6Ly9ncmFwaC5taWNyb3NvZnQuY29tIiwKICAiaXNzIjogImh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLAogICJpYXQiOiAxNDkzNDIzMTY4LAogICJuYmYiOiAxNDkzNDIzMTY4LAogICJleHAiOiAxNDkzNDY2OTUxLAogICJhY3IiOiAiMSIsCiAgImFpbyI6ICJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsCiAgImFtciI6IFsKICAgICJwd2QiCiAgXSwKICAiYXBwaWQiOiAiNjI1MzkxYWYtYzY3NS00M2U1LThlNDQtZWRkM2UzMGNlYjE1IiwKICAiYXBwaWRhY3IiOiAiMSIsCiAgImVfZXhwIjogMzAyNjgzLAogICJmYW1pbHlfbmFtZSI6ICJUZXN0IiwKICAiZ2l2ZW5fbmFtZSI6ICJOYXZ5YSIsCiAgImlwYWRkciI6ICIxNjcuMjIwLjEuMTc3IiwKICAibmFtZSI6ICJOYXZ5YSBUZXN0IiwKICAib2lkIjogIjFjZDRiY2FjLWI4MDgtNDIzYS05ZTJmLTgyN2ZiYjFiYjczOSIsCiAgInBsYXRmIjogIjMiLAogICJwdWlkIjogIjEwMDMzRkZGQTEyRUQ3RkUiLAogICJzY3AiOiAiVXNlci5SZWFkIiwKICAic3ViIjogIjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLAogICJ0aWQiOiAiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwKICAidW5pcXVlX25hbWUiOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1cG4iOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1dGkiOiAieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsCiAgInZlciI6ICIxLjAiCn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fallet: Åtkomsttokenbegäran med ett certifikat

En tjänst-till-tjänst-åtkomsttokenbegäran med ett certifikat innehåller följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| grant_type |krävs | Typen av tokenbegäran. En OBO-begäran använder en JWT-åtkomsttoken så värdet måste vara **urn:ietf:params:oauth:grant-type:jwt-bärare**. |
| Påstående |krävs | Värdet för den token som används i begäran. |
| client_id |krävs | App-ID:t som tilldelats den anropande tjänsten under registreringen med Azure AD. Om du vill hitta app-ID:n i Azure-portalen väljer du **Active Directory,** väljer katalogen och väljer sedan programnamnet. |
| client_assertion_type |krävs |Värdet måste vara`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |krävs | En JSON-webbtoken som du skapar och signerar med certifikatet som du registrerade som autentiseringsuppgifter för ditt program. Se [certifikatuppgifter](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) om du vill lära dig mer om kontrollformat och hur du registrerar certifikatet.|
| resource |krävs | App-ID-URI för den mottagande tjänsten (säker resurs). Om du vill hitta app-ID-URI i Azure-portalen väljer du **Active Directory** och väljer katalogen. Välj programnamnet, välj **Alla inställningar**och välj sedan **Egenskaper**. |
| requested_token_use |krävs | Anger hur begäran ska bearbetas. I flödet För den för första heten måste värdet **vara on_behalf_of**. |
| omfång |krävs | En utrymmesavskiljad lista över scope för tokenbegäran. För OpenID Connect måste scopet **openid** anges.|

Dessa parametrar är nästan samma som med begäran `client_secret parameter` av delad hemlighet `client_assertion_type` förutom `client_assertion`att ersätts av två parametrar: och .

#### <a name="example"></a>Exempel

Följande HTTP POST begär en https://graph.microsoft.com åtkomsttoken för webb-API:et med ett certifikat. Identifierar `client_id` tjänsten som begär åtkomsttoken.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Åtkomsttoken för tjänst

Ett lyckat svar är ett JSON OAuth 2.0-svar med följande parametrar:

| Parameter | Beskrivning |
| --- | --- |
| token_type |Anger tokentypsvärdet. Den enda typ som Azure AD stöder är **Bearer**. Mer information om innehavartoken finns i [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| omfång |Omfattningen av åtkomst som beviljats i token. |
| expires_in |Hur länge åtkomsttoken är giltig (i sekunder). |
| expires_on |Den tidpunkt då åtkomsttoken upphör att gälla. Datumet representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC fram till förfallotiden. Det här värdet används för att bestämma livslängden för cachelagrade token. |
| resource |App-ID-URI för den mottagande tjänsten (säker resurs). |
| access_token |Den begärda åtkomsttoken. Anropartjänsten kan använda den här token för att autentisera till den mottagande tjänsten. |
| id_token |Den begärda ID-token. Anropartjänsten kan använda den här token för att verifiera användarens identitet och påbörja en session med användaren. |
| refresh_token |Uppdateringstoken för den begärda åtkomsttoken. Anropartjänsten kan använda den här token för att begära en annan åtkomsttoken när den aktuella åtkomsttoken har upphört att gälla. |

### <a name="success-response-example"></a>Exempel på lyckat svar

I följande exempel visas ett lyckat svar på https://graph.microsoft.com en begäran om en åtkomsttoken för webb-API:et.

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.microsoft.com",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Exempel på felsvar

Slutpunkten för Azure AD-token returnerar ett felsvar när den försöker hämta en åtkomsttoken för ett nedströms-API som har angetts med en princip för villkorlig åtkomst (till exempel multifaktorautentisering). Tjänsten mellannivå bör visa det här felet för klientprogrammet så att klientprogrammet kan tillhandahålla användarinteraktionen för att uppfylla principen villkorlig åtkomst.

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

Tjänsten mellannivå kan använda den förvärvade åtkomsttoken för att göra autentiserade begäranden till det underordnade webb-API:et `Authorization` genom att ange token i huvudet.

### <a name="example"></a>Exempel

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>SAML påståenden som erhållits med en OAuth2.0 OBO flöde

Vissa OAuth-baserade webbtjänster måste komma åt andra webbtjänst-API:er som accepterar SAML-påståenden i icke-interaktiva flöden. Azure Active Directory kan tillhandahålla ett SAML-påstående som svar på ett on-behalf-Of-flöde som använder en SAML-baserad webbtjänst som målresurs.

>[!NOTE]
>Detta är ett icke-standardtillägg till OAuth 2.0 On-Behalf-Of-Flow som gör att ett OAuth2-baserat program kan komma åt webbtjänst API-slutpunkter som använder SAML-token.

> [!TIP]
> När du anropar en SAML-skyddad webbtjänst från ett frontend-webbprogram kan du helt enkelt anropa API:et och initiera ett normalt interaktivt autentiseringsflöde med användarens befintliga session. Du behöver bara använda ett OBO-flöde när ett service-to-service-samtal kräver en SAML-token för att tillhandahålla användarkontext.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Skaffa en SAML-token med hjälp av en OBO-begäran med en delad hemlighet

En service-till-tjänst-begäran om ett SAML-påstående innehåller följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| grant_type |krävs | Typen av tokenbegäran. För en begäran som använder en JWT måste värdet vara **urn:ietf:params:oauth:grant-type:jwt-bärare**. |
| Påstående |krävs | Värdet för åtkomsttoken som används i begäran.|
| client_id |krävs | App-ID:t som tilldelats den anropande tjänsten under registreringen med Azure AD. Om du vill hitta app-ID:n i Azure-portalen väljer du **Active Directory,** väljer katalogen och väljer sedan programnamnet. |
| client_secret |krävs | Nyckeln som registrerats för anropande tjänsten i Azure AD. Detta värde borde ha noterats vid registreringstillfället. |
| resource |krävs | App-ID-URI för den mottagande tjänsten (säker resurs). Det här är den resurs som kommer att vara målgruppen för SAML-token. Om du vill hitta app-ID-URI i Azure-portalen väljer du **Active Directory** och väljer katalogen. Välj programnamnet, välj **Alla inställningar**och välj sedan **Egenskaper**. |
| requested_token_use |krävs | Anger hur begäran ska bearbetas. I flödet För den för första heten måste värdet **vara on_behalf_of**. |
| requested_token_type | krävs | Anger vilken typ av token som begärs. Värdet kan vara **urn:ietf:params:oauth:token-type:saml2** eller **urn:ietf:params:oauth:token-type:saml1** beroende på kraven för den åtkomstda resursen. |

Svaret innehåller en SAML-token som kodats i UTF8 och Base64url.

- **SubjectConfirmationData för ett SAML-påstående som kommer från ett OBO-anrop**: Om målprogrammet kräver ett mottagarvärde i **SubjectConfirmationData**måste värdet vara en svars-URL som inte är jokertecken i resursprogramkonfigurationen.
- **Den SubjectConfirmationData noden**: Noden kan inte innehålla ett **InResponseTo-attribut** eftersom det inte är en del av ett SAML-svar. Programmet som tar emot SAML-token måste kunna acceptera SAML-kontrollen utan ett **InResponseTo-attribut.**

- **Samtycke**: Samtycke måste ha beviljats för att ta emot en SAML-token som innehåller användardata i ett OAuth-flöde. Information om behörigheter och administratörsgodkännande finns [i Behörigheter och medgivande i Slutpunkten för Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent).

### <a name="response-with-saml-assertion"></a>Svar med SAML-påstående

| Parameter | Beskrivning |
| --- | --- |
| token_type |Anger tokentypsvärdet. Den enda typ som Azure AD stöder är **Bearer**. Mer information om innehavartoken finns i [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| omfång |Omfattningen av åtkomst som beviljats i token. |
| expires_in |Hur länge åtkomsttoken är giltig (i sekunder). |
| expires_on |Den tidpunkt då åtkomsttoken upphör att gälla. Datumet representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC fram till förfallotiden. Det här värdet används för att bestämma livslängden för cachelagrade token. |
| resource |App-ID-URI för den mottagande tjänsten (säker resurs). |
| access_token |Parametern som returnerar SAML- påståendet. |
| refresh_token |Uppdateringstoken. Anropartjänsten kan använda den här token för att begära en annan åtkomsttoken efter att det aktuella SAML-påståendet har upphört att gälla. |

- token_type: Bärare
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- Resurs:`https://api.contoso.com`
- access_token: \<SAML påstående\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Uppdatera token\>

## <a name="client-limitations"></a>Begränsningar för klienten

Offentliga klienter med jokerteckensvarsadresser `id_token` kan inte använda en för OBO-flöden. En konfidentiell klient kan dock fortfarande lösa in **åtkomsttoken** som hämtats via det implicita bidragsflödet även om den offentliga klienten har en uri-registrerad jokerteckenomdirigering.

## <a name="next-steps"></a>Nästa steg

Läs mer om OAuth 2.0-protokollet och ett annat sätt att utföra autentisering mellan tjänst som använder klientautentiseringsuppgifter:

* [Tjänst till tjänstautentisering med OAuth 2.0-klientautentiseringsuppgifter bevilja i Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 i Azure AD](v1-protocols-oauth-code.md)
