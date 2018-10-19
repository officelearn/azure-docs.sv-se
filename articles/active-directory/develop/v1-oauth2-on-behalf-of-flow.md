---
title: Azure AD tjänst till tjänst-autentisering med hjälp av OAuth2.0 On-Behalf-Of utkast till en specifikation | Microsoft Docs
description: Den här artikeln beskriver hur du använder HTTP-meddelanden för att implementera tjänst till tjänst-autentisering med hjälp av OAuth2.0 On-Behalf-Of-flöde.
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: celested
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.openlocfilehash: f795b58be760bae0743b05d2827c0e9f8bdb10c6
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430093"
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Tjänst till tjänst-anrop med hjälp av delegerad användaridentitet i On-Behalf-Of-flöde
Den OAuth 2.0 Behalf (OBO) flow fungerar användningsfall där ett program anropar en tjänst/webb-API, som i sin tur måste anropa en annan tjänst/webb-API. Tanken är att sprida delegerade användaren identitets- och behörigheter genom begärandekedjan. För mellannivå-tjänsten ska göra autentiserade begäranden till den underordnade tjänsten, behöver så skydda en åtkomsttoken från Azure Active Directory (Azure AD), användarens räkning.

> [!IMPORTANT]
> Från och med maj 2018, ett `id_token` kan inte användas för On-Behalf-Of-flöde - SPA måste klara ett **åtkomst** token i en mellannivå konfidentiell klient för att utföra OBO flöden. Se [begränsningar](#client-limitations) för mer information som klienter kan utföra On-Behalf-Of-anrop.

## <a name="on-behalf-of-flow-diagram"></a>On-Behalf-Of flödesdiagram
Anta att användaren har autentiserats på ett program med hjälp av den [flöde beviljat med OAuth 2.0-auktoriseringskod](v1-protocols-oauth-code.md). Programmet har nu en åtkomsttoken (token A) med det användar- och medgivande till att komma åt mellannivå webb-API (API-A). Nu kan måste API A göra en autentiserad begäran till underordnade webb-API (API-B).

De steg som följer utgör On-Behalf-Of-flöde och beskrivs med hjälp av följande diagram.

![OAuth2.0-On-Behalf-Of-flöde](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Klientprogrammet skickar en begäran till API A med token A.
2. API-A autentiserar till Azure AD-slutpunkten för utfärdande och begär en token för att komma åt API B.
3. Azure AD-slutpunkten för utfärdande verifierar API A autentiseringsuppgifter med token A och utfärdar en åtkomsttoken för API-B (token B).
4. Token B har angetts i auktoriseringshuvudet för begäran till API B.
5. Data från den skyddade resursen returneras av API B.

>[!NOTE]
>Publik anspråk i en åtkomst-token som används för att begära en token för en underordnad tjänst måste vara id för tjänsten skickar OBO-förfrågan och token måste vara signerade med Azure Active Directory global signeringsnyckeln (som är standard för program som är registrerade via **appregistreringar** i portalen)

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrera den och en tjänst i Azure AD
Registrera både klientprogrammet och tjänsten mellannivå i Azure AD.
### <a name="register-the-middle-tier-service"></a>Registrera mellannivå-tjänsten
1. Logga in på [Azure Portal](https://portal.azure.com).
2. I det översta fältet, klicka på ditt konto och under den **Directory** väljer Active Directory-klient som du vill registrera ditt program.
3. Klicka på **fler tjänster** i det vänstra navigeringsfältet och välj **Azure Active Directory**.
4. Klicka på **appregistreringar** och välj **ny programregistrering**.
5. Ange ett eget namn för programmet och välj vilken typ av program. Baserat på typen uppsättningen program inloggnings-URL eller omdirigerings-URL: en till en bas-URL. Klicka på **skapa** att skapa programmet.
6. När du fortfarande i Azure portal, Välj ditt program och klicka på **inställningar**. Från menyn Inställningar väljer **nycklar** och lägga till en nyckel – Välj antingen 1 eller 2 år viktiga varaktigheten. När du sparar den här sidan, om nyckelvärdet visas, kopiera och spara värdet på en säker plats – du behöver den här nyckeln senare för att konfigurera inställningar för program i din implementering - kan värdet för nyckeln inte visas igen, och inte heller hämtningsbar på annat sätt , så spara det så snart den är synlig från Azure Portal.

### <a name="register-the-client-application"></a>Registrera klientprogrammet
1. Logga in på [Azure Portal](https://portal.azure.com).
2. I det översta fältet, klicka på ditt konto och under den **Directory** väljer Active Directory-klient som du vill registrera ditt program.
3. Klicka på **fler tjänster** i det vänstra navigeringsfältet och välj **Azure Active Directory**.
4. Klicka på **appregistreringar** och välj **ny programregistrering**.
5. Ange ett eget namn för programmet och välj vilken typ av program. Baserat på typen uppsättningen program inloggnings-URL eller omdirigerings-URL: en till en bas-URL. Klicka på **skapa** att skapa programmet.
6. Konfigurera behörigheter för ditt program – i menyn Inställningar, Välj den **nödvändiga behörigheter** klickar du på **Lägg till**, sedan **Välj en API**, och Skriv namnet på tjänsten mellannivå i textrutan. Klicka på **Select-behörigheter** och välj ”åtkomst *tjänstnamn*'.

### <a name="configure-known-client-applications"></a>Konfigurera kända klientprogram
I det här scenariot har mellannivå-tjänsten inga användaråtgärder för att hämta användarens medgivande till att få åtkomst till underordnade API. Därför måste kan ge åtkomst till underordnade API visas gång som en del av samtycke steg under autentiseringen.
Följ stegen nedan för att binda uttryckligen klienten appregistrering i Azure AD med registreringen av tjänsten mellannivå som sammanfogar medgivande som krävs för både klient- och mellannivå i en enda dialogruta för att uppnå detta.
1. Navigera till registrering av mellannivå tjänst och klicka på **Manifest** att öppna redigeringsprogrammet för applikationsmanifestet.
2. I manifestet, letar du upp den `knownClientApplications` matris egenskapen och Lägg till klient-ID för klientprogrammet som ett element.
3. Spara manifestet genom att klicka på Spara knappen.

## <a name="service-to-service-access-token-request"></a>Tjänsten för att tjänstbegäran om åtkomsttoken
Om du vill begära en åtkomsttoken, skapa en HTTP POST till klient-specifika Azure AD-slutpunkten med följande parametrar.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Det finns två fall beroende på om klientprogrammet väljer att skyddas av en delad hemlighet eller ett certifikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Först för användningsfall: begäran om åtkomsttoken med en delad hemlighet
När du använder en delad hemlighet, innehåller en tjänst-till-tjänst begäran om åtkomsttoken följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| _typ av beviljande |obligatorisk | Typ av token begäran. Eftersom en begäran om OBO använder en JWT-token för åtkomst, värdet måste vara **urn: ietf:params:oauth:grant-typ: jwt-ägarautentisering**. |
| försäkran |obligatorisk | Värdet för den åtkomst-token som används i begäran. |
| client_id |obligatorisk | App-ID som tilldelats till tjänsten anropande under registreringen med Azure AD. För att hitta App-ID i hanteringsportalen för Azure, klickar du på **Active Directory**, klickar du på katalogen och klicka sedan på namnet på programmet. |
| client_secret |obligatorisk | Nyckeln som har registrerats för den anropande tjänsten i Azure AD. Det här värdet ska ha har antecknat vid tidpunkten för registrering. |
| resurs |obligatorisk | App-ID URI för den mottagande tjänsten (säker resurs). För att hitta URI: N för App-ID i hanteringsportalen för Azure, klickar du på **Active Directory**, klickar du på katalogen, klickar du på namnet på programmet, klickar du på **alla inställningar** och klicka sedan på **egenskaper**. |
| requested_token_use |obligatorisk | Anger hur begäran ska bearbetas. Värdet måste vara i On-Behalf-Of-flöde **on_behalf_of**. |
| omfång |obligatorisk | Ett blanksteg avgränsade lista med omfattningar för token-begäran. För OpenID Connect, omfånget **openid** måste anges.|

#### <a name="example"></a>Exempel
Följande HTTP POST begär en åtkomsttoken för den https://graph.windows.net webb-API. Den `client_id` identifierar den tjänst som begär åtkomst-token.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fallet: begäran om åtkomsttoken med ett certifikat
En begäran för tjänst-till-tjänst åtkomst-token med ett certifikat innehåller följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| _typ av beviljande |obligatorisk | Typ av token begäran. Eftersom en begäran om OBO använder en JWT-token för åtkomst, värdet måste vara **urn: ietf:params:oauth:grant-typ: jwt-ägarautentisering**. |
| försäkran |obligatorisk | Värdet för den token som används i begäran. |
| client_id |obligatorisk | App-ID som tilldelats till tjänsten anropande under registreringen med Azure AD. För att hitta App-ID i hanteringsportalen för Azure, klickar du på **Active Directory**, klickar du på katalogen och klicka sedan på namnet på programmet. |
| client_assertion_type |obligatorisk |Värdet måste vara `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |obligatorisk | Ett intyg (en JSON Web Token) som du behöver för att skapa och signera med certifikatet du registrerad som autentiseringsuppgifter för ditt program. Läs mer om [certifikat autentiseringsuppgifter](active-directory-certificate-credentials.md) att lära dig hur du registrerar ditt certifikat och format för kontrollen.|
| resurs |obligatorisk | App-ID URI för den mottagande tjänsten (säker resurs). För att hitta URI: N för App-ID i hanteringsportalen för Azure, klickar du på **Active Directory**, klickar du på katalogen, klickar du på namnet på programmet, klickar du på **alla inställningar** och klicka sedan på **egenskaper**. |
| requested_token_use |obligatorisk | Anger hur begäran ska bearbetas. Värdet måste vara i On-Behalf-Of-flöde **on_behalf_of**. |
| omfång |obligatorisk | Ett blanksteg avgränsade lista med omfattningar för token-begäran. För OpenID Connect, omfånget **openid** måste anges.|

Observera att parametrarna är nästan samma sätt som i fallet med begäran från delad hemlighet förutom att parametern client_secret ersätts av två parametrar: en client_assertion_type och client_assertion.

#### <a name="example"></a>Exempel
Följande HTTP POST begär en åtkomsttoken för den https://graph.windows.net webb-API med ett certifikat. Den `client_id` identifierar den tjänst som begär åtkomst-token.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Tjänsten access token svar
Ett lyckat svar är ett JSON OAuth 2.0-svar med följande parametrar.

| Parameter | Beskrivning |
| --- | --- |
| token_type |Anger typ tokenu värdet. Den enda typen som har stöd för Azure AD är **ägar**. Mer information om ägar-token finns i den [Framework för OAuth 2.0-auktorisering: ägar-Token användning (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| omfång |Omfattning åtkomst beviljas i token. |
| expires_in |Hur lång tid den åtkomst-token är giltig (i sekunder). |
| expires_on |Den tid då den åtkomst-token upphör att gälla. Datumet visas som hur många sekunder en från 1970-01-01T0:0:0Z UTC tills de upphör att gälla. Det här värdet används för att fastställa livslängd för cachelagrade token. |
| resurs |App-ID URI för den mottagande tjänsten (säker resurs). |
| access_token |Den begärda åtkomst-token. Anropa tjänsten kan använda denna token för att autentisera till den mottagande tjänsten. |
| id_token |Det begärda id-token. Anropa tjänsten kan använda detta för att verifiera användarens identitet och starta en session med användaren. |
| refresh_token |Uppdateringstoken för den begärda åtkomst-token. Anropa tjänsten kan använda denna token för att begära en annan åtkomsttoken när den aktuella åtkomst-token upphör att gälla. |

### <a name="success-response-example"></a>Exempel för lyckade svar
I följande exempel visas ett lyckat svar på en begäran om en åtkomst-token för den https://graph.windows.net webb-API.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Fel svar, exempel
Ett felsvar returneras av Azure AD-tokenslutpunkten vid försök att hämta en åtkomsttoken för den underordnade API om underordnade API: et har en princip för villkorlig åtkomst som multifaktorautentisering som angetts för den. Tjänsten mellannivå bör ge det här felet till klientprogrammet så att klientprogrammet kan ange användarinteraktion för att uppfylla principen för villkorlig åtkomst.

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
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```
## <a name="service-to-service-calls-using-a-saml-assertion-obtained-with-an-oauth20-on-behalf-of-flow"></a>Tjänst till tjänst-anrop med hjälp av en SAML-försäkran som hämtas med en OAuth2.0 på-flöde

Vissa OAuth-baserade webbtjänsterna services behöver åtkomst till andra API: er som har stöd för SAML-intyg i icke-interaktivt flöden för webbtjänsten.  Azure Active Directory kan ge en SAML-försäkran som svar på en on-behalf-of-flöde med en SAML-baserad webbtjänst som en målresurs. 

>[!NOTE] 
>Det här är en förlängning av inte är standard i on-behalf-of flöde för OAuth 2.0 som gör att ett OAuth2-baserade program till åtkomst API slutpunkter för webbtjänster som använder SAML-token.  

>[!TIP]
>Om du anropar en webbtjänst för SAML skyddas från ett frontend-webbprogram, du helt enkelt anropa API: et och starta ett flöde för normal interaktiv autentisering som används av användarna befintlig session.  Du behöver bara Överväg att använda en OBO-flöde när en tjänst till tjänst-anrop krävs en SAML-token för användarkontext.

### <a name="obtain-a-saml-token-using-an-obo-request-with-a-shared-secret"></a>Hämta en SAML-token med en OBO-begäran med en delad hemlighet
En tjänst-till-tjänst-begäran för att hämta en SAML-försäkran innehåller följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| _typ av beviljande |obligatorisk | Typ av token begäran. Värdet måste vara begäran med hjälp av en JWT **urn: ietf:params:oauth:grant-typ: jwt-ägarautentisering**. |
| försäkran |obligatorisk | Värdet för den åtkomst-token som används i begäran.|
| client_id |obligatorisk | App-ID som tilldelats till tjänsten anropande under registreringen med Azure AD. För att hitta App-ID i hanteringsportalen för Azure, klickar du på **Active Directory**, klickar du på katalogen och klicka sedan på namnet på programmet. |
| client_secret |obligatorisk | Nyckeln som har registrerats för den anropande tjänsten i Azure AD. Det här värdet ska ha har antecknat vid tidpunkten för registrering. |
| resurs |obligatorisk | App-ID URI för den mottagande tjänsten (säker resurs). Det här är den resurs som kommer att målgruppen för SAML-token.  För att hitta URI: N för App-ID i hanteringsportalen för Azure, klickar du på **Active Directory**, klickar du på katalogen, klickar du på namnet på programmet, klickar du på **alla inställningar** och klicka sedan på **egenskaper**. |
| requested_token_use |obligatorisk | Anger hur begäran ska bearbetas. Värdet måste vara i On-Behalf-Of-flöde **on_behalf_of**. |
| requested_token_type | obligatorisk | Anger vilken typ av token begärs.  Värdet kan vara ”urn: ietf:params:oauth:token-typ: saml2” eller ”urn: ietf:params:oauth:token-typ: saml1” beroende på kraven för resursen ifråga. |


Svaret innehåller en UTF8 och Base64url kodad SAML token. 

SubjectConfirmationData för SAML-försäkran som kommer från ett OBO-anrop: Om målprogrammet kräver ett mottagarens värde i SubjectConfirmationData så det måste anges som en icke-jokertecken svars-URL i programkonfigurationen för resursen.

Noden SubjectConfirmationData får inte innehålla en InResponseTo attributet eftersom det inte är en del av en SAML-svar.  Programmet som tar emot SAML-token behov för att kunna acceptera SAML-försäkran utan ett InResponseTo-attribut.

Medgivande: För att få en SAML-token som innehåller användardata på en flöde för OAuth medgivande måste ha beviljats.  Se https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent för information om behörighet och hämta administratörens godkännande.

### <a name="response-with-saml-assertion"></a>Svar med SAML-försäkran

| Parameter | Beskrivning |
| --- | --- |
| token_type |Anger typ tokenu värdet. Den enda typen som har stöd för Azure AD är **ägar**. Mer information om ägar-token finns i den [Framework för OAuth 2.0-auktorisering: ägar-Token användning (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| omfång |Omfattning åtkomst beviljas i token. |
| expires_in |Hur lång tid den åtkomst-token är giltig (i sekunder). |
| expires_on |Den tid då den åtkomst-token upphör att gälla. Datumet visas som hur många sekunder en från 1970-01-01T0:0:0Z UTC tills de upphör att gälla. Det här värdet används för att fastställa livslängd för cachelagrade token. |
| resurs |App-ID URI för den mottagande tjänsten (säker resurs). |
| access_token |SAML-försäkran returneras i parametern access_token. |
| refresh_token |Uppdateringstoken. Anropa tjänsten kan använda denna token för att begära en annan åtkomsttoken när den aktuella SAML-försäkran upphör att gälla. |

token_type: ägar expires_in:3296 ext_expires_in:0 expires_on:1529627844 resurs:https://api.contoso.com access_token: <Saml assertion> issued_token_type:urn:ietf:params:oauth:token-typ: saml2 refresh_token: <Refresh token>

## <a name="client-limitations"></a>Klientbegränsningar
Offentliga klienter med jokertecken svars-URL kan inte använda en `id_token` för OBO flöden. En konfidentiell klient kan dock fortfarande lösa in åtkomsttoken som köpts via implicit beviljande av flödet även om offentlig klient har jokertecken redirect URI-registreras.

## <a name="next-steps"></a>Nästa steg
Läs mer om OAuth 2.0-protokollet och ett annat sätt att utföra tjänst till tjänst-autentisering via klientautentiseringsuppgifter.
* [Tjänsten för att tjänstautentisering med hjälp av OAuth 2.0-klientautentiseringsuppgifter i Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 i Azure AD](v1-protocols-oauth-code.md)