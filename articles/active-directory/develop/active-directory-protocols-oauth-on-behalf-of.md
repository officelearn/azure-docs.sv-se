---
title: "Azure AD tjänster auth med OAuth2.0 On-Behalf-Of utkast till en specifikation | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder HTTP-meddelanden för att implementera tjänster autentisering med hjälp av OAuth2.0 på-flöde."
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 0bb74816f216f0965c3ec780c4895cf7e488c3cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Tjänsten tjänstanrop med delegerad användaridentitet i On-Behalf-Of-flöde
OAuth 2.0-On-Behalf-Of flöde fungerar användningsfall där ett program anropar ett service/webb-API, som i sin tur behöver anropa en annan tjänst/webb-API. Tanken är att sprida delegerad användarens identitet och behörigheter via alla begäranden har gjorts. För tjänsten mellannivå så att autentiserade begäranden till den underordnade tjänsten behöver skydda en åtkomst-token från Azure Active Directory (Azure AD) för användarens räkning.

## <a name="on-behalf-of-flow-diagram"></a>On-Behalf-Of flödesdiagram
Anta att användaren har autentiserats på ett program som använder den [OAuth 2.0 flöde beviljat med auktoriseringskod](active-directory-protocols-oauth-code.md). Programmet har nu en åtkomst-token (token A) med användarens anspråk och medgivande till mellannivå webb-API (API-A). Nu måste API A att begära en autentiserad underordnat webb-API (API-B).

De steg som följer utgöra On-Behalf-Of-flöde och förklaras med hjälp av följande diagram.

![OAuth2.0 på-flöde](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Klientprogrammet begär till API-A med token A.
2. API-A autentiserar till Azure AD utfärdande slutpunkten och begär en token för åtkomst till API B.
3. Azure AD utfärdande slutpunkten validerar API A autentiseringsuppgifter med ett token och skickar åtkomsttoken för API-B (token B).
4. Token B har angetts i auktoriseringshuvudet för en begäran om att API B.
5. Data från den skyddade resursen returneras av API B.

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrera programmet och service i Azure AD
Registrera både klientprogrammet och mellannivå-tjänsten i Azure AD.
### <a name="register-the-middle-tier-service"></a>Registrera service mellannivå
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på den översta raden på ditt konto och under den **Directory** Välj Active Directory-klient som du vill registrera ditt program.
3. Klicka på **fler tjänster** i den vänstra nav och välj **Azure Active Directory**.
4. Klicka på **App registreringar** och välj **nya appregistrering**.
5. Ange ett eget namn för programmet och välj programtyp. Baserat på uppsättningen programmet typen inloggning URL eller omdirigera URL: en till en bas-URL. Klicka på **skapa** att skapa programmet.
6. Välj programmet när fortfarande i Azure-portalen och klicka på **inställningar**. Välj på menyn inställningar **nycklar** och lägga till en nyckel - markerar du en nyckel varaktighet för antingen 1 eller 2 år. När du sparar den här sidan kan värdet för nyckeln ska visas, kopiera och spara värdet i en säker plats – du behöver den här nyckeln senare för att konfigurera inställningar för program i din implementering - värdet för nyckeln ska inte visas igen, och inte heller strängfält av något annat sätt, så du posten så snart den är synliga från Azure Portal.

### <a name="register-the-client-application"></a>Registrera klientprogrammet
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på den översta raden på ditt konto och under den **Directory** Välj Active Directory-klient som du vill registrera ditt program.
3. Klicka på **fler tjänster** i den vänstra nav och välj **Azure Active Directory**.
4. Klicka på **App registreringar** och välj **nya appregistrering**.
5. Ange ett eget namn för programmet och välj programtyp. Baserat på uppsättningen programmet typen inloggning URL eller omdirigera URL: en till en bas-URL. Klicka på **skapa** att skapa programmet.
6. Konfigurera behörigheter för ditt program - på menyn Inställningar, väljer den **nödvändiga behörigheter** klickar du på **Lägg till**, sedan **väljer en API**, och Skriv namnet på mellannivå-tjänst i textrutan. Klicka på **Välj behörigheter** och välj ' åtkomst *tjänstnamnet*'.

### <a name="configure-known-client-applications"></a>Konfigurera kända klientprogram
I det här scenariot har mellannivå-tjänsten inga användaråtgärder för att hämta användarens medgivande åtkomst till underordnade API. Därför måste alternativet för att bevilja åtkomst till underordnade API: N vara angiven gång som en del av samtycke steg under autentiseringen.
Följ stegen nedan ska bindas explicit klienten appens registrering i Azure AD med registreringen av tjänsten mellannivå som sammanfogas medgivande som krävs för både klient- och mellannivå i en enda dialogruta för att uppnå.
1. Navigera till registrering av mellannivå tjänst och klicka på **Manifest** att öppna Redigeraren för manifestet.
2. I manifestet, leta upp den `knownClientApplications` matrisen egenskapen och Lägg till klient-ID för klientprogrammet som ett element.
3. Spara manifestet genom att klicka på Spara knappen.

## <a name="service-to-service-access-token-request"></a>Tjänsten token tjänstbegäran för åtkomst
Om du vill begära en åtkomst-token kan du göra en HTTP POST för klient-specifika Azure AD-slutpunkten med följande parametrar.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Det finns två fall beroende på om klientprogrammet väljer att skyddas av en delad hemlighet, eller ett certifikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Först fall: token åtkomst-begäran med en delad hemlighet
När du använder en delad hemlighet, innehåller en tjänst-till-tjänst åtkomst tokenbegäran följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| grant_type |Krävs | Typ av tokenbegäran. Värdet måste vara en begäran som använder en JWT **urn: ietf:params:oauth:grant-typ: jwt-ägar**. |
| kontrollen |Krävs | Värdet för den token som används i begäran. |
| client_id |Krävs | App-ID som tilldelats till tjänsten anropa under registreringen med Azure AD. Du hittar det App-ID i Azure-hanteringsportalen, klicka på **Active Directory**klickar du på katalogen och klicka sedan på namnet på programmet. |
| client_secret |Krävs | Nyckeln som har registrerats för anropa tjänsten i Azure AD. Det här värdet ska har konstaterats vid tiden för registrering. |
| Resursen |Krävs | App-ID URI för tjänsten mottagande (skyddad resurs). Om du vill hitta URI: N för App-ID i Azure-hanteringsportalen, klickar du på **Active Directory**, klicka på katalogen, programnamn, **alla inställningar** och klicka sedan på **egenskaper**. |
| requested_token_use |Krävs | Anger hur begäran ska bearbetas. Värdet måste vara i On-Behalf-Of-flöde **on_behalf_of**. |
| Omfång |Krävs | Ett utrymme avgränsade lista över scope för tokenbegäran. För OpenID Connect omfånget **openid** måste anges.|

#### <a name="example"></a>Exempel
Följande HTTP POST-begäranden en åtkomsttoken för https://graph.windows.net webb-API. Den `client_id` identifierar den tjänst som begär åtkomst-token.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fall: token åtkomst-begäran med ett certifikat
En token-tjänster åtkomst-begäran med ett certifikat innehåller följande parametrar:

| Parameter |  | Beskrivning |
| --- | --- | --- |
| grant_type |Krävs | Typ av tokenbegäran. Värdet måste vara en begäran som använder en JWT **urn: ietf:params:oauth:grant-typ: jwt-ägar**. |
| kontrollen |Krävs | Värdet för den token som används i begäran. |
| client_id |Krävs | App-ID som tilldelats till tjänsten anropa under registreringen med Azure AD. Du hittar det App-ID i Azure-hanteringsportalen, klicka på **Active Directory**klickar du på katalogen och klicka sedan på namnet på programmet. |
| client_assertion_type |Krävs |Värdet måste vara`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Krävs | Ett intyg (en JSON Web Token) som du behöver för att skapa och registrera med certifikatet du registrerad som autentiseringsuppgifter för ditt program.  Läs mer om [certifikat autentiseringsuppgifter](active-directory-certificate-credentials.md) att lära dig att registrera ditt certifikat och format för kontrollen.|
| Resursen |Krävs | App-ID URI för tjänsten mottagande (skyddad resurs). Om du vill hitta URI: N för App-ID i Azure-hanteringsportalen, klickar du på **Active Directory**, klicka på katalogen, programnamn, **alla inställningar** och klicka sedan på **egenskaper**. |
| requested_token_use |Krävs | Anger hur begäran ska bearbetas. Värdet måste vara i On-Behalf-Of-flöde **on_behalf_of**. |
| Omfång |Krävs | Ett utrymme avgränsade lista över scope för tokenbegäran. För OpenID Connect omfånget **openid** måste anges.|

Observera att parametrarna är nästan desamma som i fallet med begäran från delad hemlighet förutom att client_secret-parameter har ersatts av två parametrar: en client_assertion_type och client_assertion.

#### <a name="example"></a>Exempel
Följande HTTP POST-begäranden en åtkomsttoken för https://graph.windows.net webb-API med ett certifikat. Den `client_id` identifierar den tjänst som begär åtkomst-token.

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

## <a name="service-to-service-access-token-response"></a>Tjänsten för att tjänsten åtkomst-token svar
Ett lyckat svar är ett JSON OAuth 2.0-svar med följande parametrar.

| Parameter | Beskrivning |
| --- | --- |
| token_type |Anger värdet för token-typer. Den enda typen som har stöd för Azure AD är **ägar**. Läs mer om ägar-token i [OAuth 2.0 auktorisering Framework: ägar-Token användning (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| Omfång |Omfattningen av åtkomst som beviljas i token. |
| expires_in |Hur lång tid den åtkomst-token är giltig (i sekunder). |
| expires_on |Tiden då den åtkomst-token upphör att gälla. Representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC tills förfallotid. Det här värdet används för att fastställa livslängden för cachelagrade token. |
| Resursen |App-ID URI för tjänsten mottagande (skyddad resurs). |
| access_token |Den begärda åtkomst-token. Anropa tjänsten kan använda denna token för att autentisera till den mottagande tjänsten. |
| id_token |Det begärda id-token. Anropa tjänsten kan använda den för att verifiera användarens identitet och starta en session med användaren. |
| refresh_token |Uppdateringstoken för den begärda åtkomst-token. Anropa tjänsten kan använda denna token för att begära en annan åtkomsttoken när den aktuella åtkomst-token upphör att gälla. |

### <a name="success-response-example"></a>Lyckade svar-exempel
I följande exempel visas ett lyckat svar på en begäran om en åtkomsttoken för https://graph.windows.net webb-API.

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

### <a name="error-response-example"></a>Fel svar-exempel
Ett felsvar som returneras av Azure AD-token för slutpunkt när du försöker hämta en åtkomst-token för den underordnade API om underordnade API: et har en princip för villkorlig åtkomst, till exempel multifaktorautentisering som angetts för den. Tjänsten mellannivå bör ansluta det här felet till klientprogrammet så att klientprogrammet kan användaren för att uppfylla principen för villkorlig åtkomst.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Använd åtkomst-token för att komma åt den skyddade resursen
Tjänsten mellannivå kan nu använda token förvärvade ovan för att göra autentiserade begäranden för den underordnade webben-API, genom att ange token i den `Authorization` rubrik.

### <a name="example"></a>Exempel
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="next-steps"></a>Nästa steg
Läs mer om OAuth 2.0-protokollet och ett annat sätt att utföra tjänster auth med klientens autentiseringsuppgifter.
* [Tjänsten för att tjänsten autentisering med OAuth 2.0 klientens autentiseringsuppgifter grant i Azure AD](active-directory-protocols-oauth-service-to-service.md)
* [OAuth 2.0 i Azure AD](active-directory-protocols-oauth-code.md)
