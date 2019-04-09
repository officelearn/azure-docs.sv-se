---
title: Azure Active Directory access token referens | Microsoft Docs
description: Läs mer om orsakats av slutpunkterna v1.0 och v2.0 av Azure AD-åtkomsttoken.
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
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b94004aa4b4834be80c13a044fcf7eb0023b6f7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259872"
---
# <a name="azure-active-directory-access-tokens"></a>Azure Active Directory-åtkomsttoken

Åtkomst-token gör att klienterna kan på ett säkert sätt anropa API: er som skyddas av Azure. Azure Active Directory (Azure AD)-åtkomsttoken är [JWTs](https://tools.ietf.org/html/rfc7519), Base64-kodade JSON-objekt som signerats av Azure. Klienter bör hantera åtkomst som täckande strängar som innehållet i token är avsedda för resursen endast. För verifiering och felsökning kan utvecklare kan avkoda JWTs med hjälp av en plats som [jwt.ms](https://jwt.ms). Klienten kan hämta en åtkomsttoken från antingen slutpunkten (v1.0 eller v2.0) med olika protokoll.

När du begär en åtkomsttoken returnerar Azure AD även vissa metadata om åtkomsttoken för användning av din app. Informationen omfattar förfallotiden för åtkomst-token och de omfattningar som den är giltig. Dessa data kan din app att utföra intelligent cachelagring av åtkomsttoken utan att parsa den åtkomst-token.

Om ditt program är en resurs (webb-API) som klienter kan begära åtkomst till, ange åtkomst-token användbar information för användning i autentisering och auktorisering, till exempel användaren, klient, utfärdare, behörigheter och mer. 

Se följande avsnitt för att lära dig hur en resurs kan verifiera och använder anspråken i en åtkomsttoken.

> [!NOTE]
> När du testar ditt klientprogram med ett personligt konto (t.ex hotmail.com eller outlook.com) kan hända att den åtkomst-token som tas emot av din klient är en täckande sträng. Det beror på att resursen ifråga har begärt äldre MSA (Microsoft-konto) biljetter som är krypterade och kan inte tolkas av klienten.

## <a name="sample-tokens"></a>Exempel-token

V1.0 och v2.0 token likna och innehåller många av samma anspråk. Ett exempel på var och en finns här.

### <a name="v10"></a>V1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd
```

Visa den här v1.0 token i [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Visa den här v2.0-token i [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Anspråk i åtkomsttoken

JWTs är uppdelat i tre delar:

* **Rubriken** -ger information om hur du [Validera token](#validating-tokens) inklusive information om vilka typer av token och hur det är signerat.
* **Nyttolasten** -innehåller alla viktig information om den användare eller en app som försöker anropar din tjänst.
* **Signaturen** -är råmaterial som används för att validera token.

Varje del separeras av en punkt (`.`) och separat Base64-kodad.

Anspråk finns endast om ett värde för att fylla i. Appen ska därför inte skapa ett beroende på ett anspråk är närvarande. Exempel är `pwd_exp` (inte varje klient kräver lösenord för att gå ut) eller `family_name` ([autentiseringsuppgifter för klienten](v1-oauth2-client-creds-grant-flow.md) flöden är åt program som inte har några namn). Anspråk som används för verifieringen av åtkomsttoken kommer alltid att finns.

> [!NOTE]
> Vissa anspråk används för att Azure AD säker token vid återanvändning. Dessa är markerade som inte är för offentlig konsumtion i beskrivningen på ”täckande”. De här anspråken kanske eller kanske inte visas i en token och kan lägga till nya utan föregående meddelande.

### <a name="header-claims"></a>Huvud-anspråk

|Begäran | Format | Beskrivning |
|--------|--------|-------------|
| `typ` | String - always "JWT" | Anger att token är en JWT.|
| `nonce` | String | En unik identifierare som används för att skydda mot token repetitionsattacker. Resursen kan registrera det här värdet för att skydda mot repetitioner. |
| `alg` | String | Anger den algoritm som användes för att signera token, till exempel ”RS256” |
| `kid` | String | Anger certifikatets tumavtryck för den offentliga nyckeln som används för att registrera den här token. Släpps i både v1.0 och v2.0-åtkomsttoken. |
| `x5t` | String | Fungerar på samma sätt (i användning och värde) som `kid`. `x5t` genereras ett äldre anspråk endast i v1.0 åtkomsttoken för kompatibilitet. |

### <a name="payload-claims"></a>Nyttolasten anspråk

| Begäran | Format | Beskrivning |
|-----|--------|-------------|
| `aud` | Sträng, ett App-ID-URI | Identifierar den avsedda mottagaren av token. I åtkomst-token är målgruppen appens program-ID som tilldelats din app i Azure-portalen. Din app ska verifiera det här värdet och avvisa token om värdet inte matchar. |
| `iss` | Sträng, en STS-URI | Identifierar den säkerhetstokentjänst (STS) som skapar och återställer token och Azure AD-klient där användaren autentiserades. Om Utfärdad token är en v2.0-token (se den `ver` anspråk), URI: N går ut om `/v2.0`. Det GUID som anger att användaren är en konsument användare från ett Microsoft-konto är `9188040d-6c67-4c5b-b112-36a304b66dad`. Din app ska använda GUID-del av kravet för att minska antalet klienter som kan logga in på appen, om tillämpligt. |
|`idp`| String, usually an STS URI | Registrerar den identitetsprovider som har autentiserat subjektet för token. Det här värdet är identiskt med utfärdaren anspråkets värde såvida inte användarkontot inte i samma klient som utfärdaren - gäster, till exempel. Om anspråket inte är tillgänglig, innebär det att värdet för `iss` kan användas i stället.  För personliga konton som används i en organisations kontext (till exempel ett personligt konto bjudits in till en Azure AD-klient), den `idp` anspråk kan vara ”live.com” eller en STS-URI som innehåller Microsoft-kontots klientorganisation `9188040d-6c67-4c5b-b112-36a304b66dad`. |  
| `iat` | int, en UNIX-tidsstämpel | ”Utfärdat till” anger när autentisering för den här token inträffade. |
| `nbf` | int, en UNIX-tidsstämpel | ”Nbf” (inte före)-anspråket identifierar den tid som JWT inte måste godkännas för bearbetning. |
| `exp` | int, en UNIX-tidsstämpel | ”Exp” (upphör att gälla)-anspråket identifierar den upphör att gälla på eller efter vilket JWT måste inte godkännas för bearbetning. Det är viktigt att Observera att en resurs kan avvisa token före denna tidpunkt, till exempel när en ändring i autentisering krävs eller en återkallningen av token har identifierats. |
| `acr` | Sträng, ett ”0” eller ”1” | ”Autentisering kontext class”-anspråk. Värdet ”0” anger slutanvändarens autentisering inte uppfyllde kraven för ISO/IEC 29115. |
| `aio` | Täckande sträng | Ett internt anspråk som används av Azure AD för att registrera data för token återanvändning. Resurser bör inte använda det här anspråket. |
| `amr` | JSON-matris med strängar | Visa endast i v1.0 token. Identifierar hur ämnet för token autentiserades. Se [amr anspråk avsnittet](#the-amr-claim) för mer information. |
| `appid` | Sträng, ett GUID | Visa endast i v1.0 token. Program-ID för klienten med hjälp av token. Programmet kan fungera som sig självt eller för en användares räkning. Program-ID representerar vanligen ett programobjekt, men det kan också visa en tjänstens huvudnamnsobjekt i Azure AD. |
| `appidacr` | ”0”, ”1” eller ”2” | Visa endast i v1.0 token. Anger hur klienten autentiserades. Värdet är ”0” för en offentlig klient. Om klient-ID och klienthemlighet används, är värdet ”1”. Om ett klientcertifikat har använts för autentisering, är värdet ”2”. |
| `azp` | Sträng, ett GUID | Visa endast i v2.0-token. Program-ID för klienten med hjälp av token. Programmet kan fungera som sig självt eller för en användares räkning. Program-ID representerar vanligen ett programobjekt, men det kan också visa en tjänstens huvudnamnsobjekt i Azure AD. |
| `azpacr` | ”0”, ”1” eller ”2” | Visa endast i v2.0-token. Anger hur klienten autentiserades. Värdet är ”0” för en offentlig klient. Om klient-ID och klienthemlighet används, är värdet ”1”. Om ett klientcertifikat har använts för autentisering, är värdet ”2”. |
| `groups` | JSON-matris av GUID | Innehåller objekt-ID som representerar användarens gruppmedlemskap. Dessa värden är unikt (se objekt-ID) och kan användas på ett säkert sätt för att hantera åtkomst till exempel framtvinga behörighet att komma åt en resurs. De grupper som ingår i grupper-anspråket är konfigurerade på basis av per program, via den `groupMembershipClaims` egenskapen för den [programmanifestet](reference-app-manifest.md). En null-värde ska undanta alla grupper, värdet ”Tilldelningsmodulen” innehåller endast Active Directory-säkerhetsgrupp-medlemskap och ett värde av ”alla” kommer inkludera säkerhetsgrupper och distributionslistor för Office 365. <br><br>Se den `hasgroups` anspråk nedan för information om hur du använder den `groups` anspråk med implicit beviljande. <br>För andra flöden om antalet grupper som användaren är i går via en gräns (150 för SAML, 200 för JWT), läggs sedan ett överförbrukning anspråk till anspråk datakällor som pekar på Graph-slutpunkt som innehåller listan över grupper för användaren. |
| `hasgroups` | Boolesk | Om det finns alltid `true`, anger användaren finns i minst en grupp. Används i stället för den `groups` anspråk för JWTs i implicit beviljande av flöden om fullständig grupper anspråk kan utökas URI-fragment överskrider gränserna för URL-längd (för närvarande 6 eller flera grupper). Anger att klienten ska använda i diagrammet för att fastställa användarens grupper (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | JSON-objekt | För tokenbegäranden som inte är begränsad längd (se `hasgroups` ovan) men fortfarande är för stor för token en länk till fullständiga listan över för användaren ska inkluderas. För JWTs som en distribuerad anspråk för SAML som ett nytt anspråk i stället för den `groups` anspråk. <br><br>**Exempelvärde JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `preferred_username` | String | Primära användarnamnet som representerar användaren. Det kan vara en e-postadress, telefonnummer eller ett allmänt användarnamn utan angivet format. Värdet är föränderliga och kan ändras över tid. Eftersom det är föränderliga, måste det här värdet inte användas vid auktoriseringsbeslut.  Den kan användas för användarnamn tips om. Den `profile` omfattning krävs för att ta emot det här anspråket. |
| `name` | String | Innehåller ett läsbart värde som identifierar föremål för token. Värdet är inte säkert att vara unikt, det är föränderliga och den har utformats för att användas endast för visning. Den `profile` omfattning krävs för att ta emot det här anspråket. |
| `oid` | Sträng, ett GUID | Oföränderligt ID för ett objekt i Microsoft identity-plattformen, i det här fallet ett användarkonto. Det kan också användas för att utföra auktoriseringskontroller på ett säkert sätt och som en nyckel i databastabeller. Detta ID identifierar användaren för program – två olika program som loggar in samma användare får samma värde i den `oid` anspråk. Därför `oid` kan användas när att göra frågor till Microsoft online services, till exempel Microsoft Graph. Microsoft Graph returnerar detta ID som den `id` -egenskapen för ett givet användarkonto. Eftersom den `oid` tillåter flera appar att korrelera användare, den `profile` omfattning krävs för att ta emot det här anspråket. Observera att om en enskild användare finns i flera klienter, användaren innehåller ett annat objekt-ID i varje klient – de anses vara olika konton, även om användaren loggar in på varje konto med samma autentiseringsuppgifter. |
| `rh` | Täckande sträng | Ett internt anspråk som används av Azure för att verifiera token. Resurser bör inte använda det här anspråket. |
| `scp` | Sträng, ett blanksteg avgränsade lista med omfattningar | Uppsättningen med omfattningar som exponeras av ditt program där klientprogrammet har begärt (och tas emot) godkänna. Din app ska kontrollera att dessa scope är giltigt som exponeras av din app och fatta auktoriseringsbeslut baserat på värdet för dessa scope. Ingår endast för [användartoken](#user-and-application-tokens). |
| `roles` | Matris med strängar, en lista över behörigheter | Uppsättningen behörigheter som exponeras av programmet som det begärande programmet har fått behörighet att anropa. För [programmet token](#user-and-application-tokens), används under den [klientens autentiseringsuppgifter](v1-oauth2-client-creds-grant-flow.md) flödet i stället för användarens omfång.  För [användartoken](#user-and-application-tokens) detta fylls i med de roller som användaren har tilldelats på målprogrammet. |
| `sub` | Sträng, ett GUID | Huvudkontot som token kontrollerar information, t.ex användare av en app. Det här värdet kan inte ändras och det går inte att tilldela om eller återanvänds. Den kan användas för att utföra auktoriseringskontroller på ett säkert sätt, till exempel när token används för att komma åt en resurs och kan användas som en nyckel i databastabeller. Eftersom ämne är alltid finns i token som Azure AD-problem, rekommenderar vi att använda det här värdet i ett auktoriseringssystem för allmänna. Ämnet är dock en pairwise identifierare – det är unikt för ett visst program-ID. Om en användare loggar in på två olika appar med hjälp av två olika klient-ID, därför får apparna två olika värden för ämne anspråket. Detta kan eller inte är önskvärt beroende på dina krav arkitektur och sekretess. |
| `tid` | Sträng, ett GUID | Representerar den Azure AD-klient som användaren är från. För arbets- och skolkonton-konton är GUID oföränderligt klient-ID för den organisation som användaren tillhör. Värdet är för personliga konton `9188040d-6c67-4c5b-b112-36a304b66dad`. Den `profile` omfattning krävs för att ta emot det här anspråket. |
| `unique_name` | String | Visa endast i v1.0 token. Innehåller ett läsbart värde som identifierar subjektet för token. Det här värdet är inte säkert att vara unika inom en klient och bör endast användas för visning. |
| `uti` | Täckande sträng | Ett internt anspråk som används av Azure för att verifiera token. Resurser bör inte använda det här anspråket. |
| `ver` | String, antingen `1.0` eller `2.0` | Anger versionen av den åtkomst-token. |

#### <a name="v10-basic-claims"></a>V1.0 grundläggande anspråk

Följande anspråken kommer att inkluderas i v1.0 token om det är tillämpligt, men ingår inte i version 2.0-token som standard. Om du använder version 2.0 och du behöver någon av dessa anspråk kan begära dem med hjälp av [valfria anspråk](active-directory-optional-claims.md).

| Begäran | Format | Beskrivning |
|-----|--------|-------------|
| `ipaddr`| String | IP-adressen den användare som autentiseras från. |
| `onprem_sid`| Strängen i [SID-format](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | I fall där användaren har en lokal autentisering ger det här anspråket sina SID. Du kan använda `onprem_sid` för auktorisering i äldre program.|
| `pwd_exp`| int, en UNIX-tidsstämpel | Anger när användarens lösenord upphör att gälla. |
| `pwd_url`| String | En URL där användare kan skickas för att återställa sina lösenord. |
| `in_corp`|boolesk | Signaler om klienten inloggning från företagsnätverket. Om de inte är ingår inte anspråket. |
| `nickname`| String | Ytterligare ett namn för användaren, separat från förnamn eller efternamn.|
| `family_name` | String | Finns den senaste namn, efternamn eller namn för användaren som definieras på användarobjektet. |
| `given_name` | String | Innehåller först eller namnet för användaren som angetts på användarobjektet. |
| `upn` | String | Användarnamnet för användaren. Kan vara ett telefonnummer, e-postadress eller oformaterad sträng. Bör endast användas för visning och tillhandahåller användarnamn tips i omautentisering scenarier. |

#### <a name="the-amr-claim"></a>Den `amr` anspråk

Microsoft identiteter kan autentiseras i en mängd olika sätt, vilket kan vara relevanta för ditt program. Den `amr` anspråk är en matris som kan innehålla flera objekt som `["mfa", "rsa", "pwd"]`, för en autentisering som används för både ett lösenord och autentiseraren.

| Värde | Beskrivning |
|-----|-------------|
| `pwd` | Lösenordsautentisering Microsoft ett lösenord eller en app klienthemlighet. |
| `rsa` | Autentisering baserades på bevis på en RSA-nyckel, till exempel med den [Microsoft Authenticator-appen](https://aka.ms/AA2kvvu). Detta inkluderar om autentisering har utförts av en självsignerat JWT med en tjänst som ägs X509 certifikat. |
| `otp` | Engångskod med hjälp av ett e-postmeddelande eller ett SMS. |
| `fed` | En federerad autentisering försäkran (till exempel JWT eller SAML) har använts. |
| `wia` | Windows-integrerad autentisering |
| `mfa` | Multifaktorautentisering har använts. Om det finns begäras andra autentiseringsmetoder också. |
| `ngcmfa` | Motsvarar `mfa`, som används för etablering av vissa avancerade autentiseringstyper. |
| `wiaormfa`| Användaren för Windows eller en MFA-autentiseringsuppgifter för att autentisera. |
| `none` | Ingen autentisering har utförts. |

## <a name="validating-tokens"></a>Verifiera token

För att verifiera en id_token eller en access_token kan bör din app verifiera både signaturen för den token och anspråk. För att kunna verifiera åtkomsttoken, bör din app också verifiera utfärdaren, målgruppen och signering token. Dessa måste verifieras mot värdena i OpenID discovery-dokumentet. Till exempel klient oberoende-versionen av dokumentet finns på [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). 

Azure AD-mellanprogram har inbyggda funktioner för att verifiera åtkomsttoken och du kan bläddra igenom vår [exempel](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) i valfritt språk. Mer information om hur du uttryckligen validerar en JWT-token finns i den [manuell JWT-Verifieringsexempel](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation). 

Vi tillhandahåller bibliotek och kodexempel som visar hur du enkelt hantera tokenvalidering. Den informationen nedan tillhandahålls för de som vill förstå underliggande processen. Det finns också flera bibliotek med öppen källkod för från tredje part för JWT-verifiering – det finns minst ett alternativ för nästan alla plattformar och språk där. Läs mer om Azure AD-autentiseringsbibliotek och kodexempel [v1.0 autentiseringsbibliotek](active-directory-authentication-libraries.md) och [v2.0 autentiseringsbibliotek](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Verifiera signaturen

En JWT innehåller tre segment som avgränsas med den `.` tecken. Det första segmentet kallas den **rubrik**, andra som den **brödtext**, och tredje som den **signatur**. Signatur-segment kan användas för att verifiera äkthet token så att det kan vara betrott av din app.

Token som utfärdas av Azure AD har signerats med hjälp av bransch standard asymmetriska krypteringsalgoritmer, till exempel RSA-256. Rubriken för JWT innehåller information om metoden nyckel och kryptering används för att signera token:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

Den `alg` anspråk anger algoritmen som används för att signera token, medan den `kid` anspråk anger den specifika offentliga nyckel som användes för att signera token.

Vid en given tidpunkt i tid, kan Azure AD logga en id_token som använder någon av en viss uppsättning offentligt / privat nyckelpar. Azure AD roterar möjliga uppsättning nycklar regelbundet, så att din app ska skrivas till hanterar dessa viktiga ändringar automatiskt. En rimlig frekvens för att söka efter uppdateringar till de offentliga nycklar som används av Azure AD är 24 timmar.

Du kan hämta signering viktiga data som behövs för att verifiera signaturen med hjälp av den [OpenID Connect metadatadokument](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) finns på:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Prova följande [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) i en webbläsare!

Det här Metadatadokumentet:

* Är ett JSON-objekt som innehåller flera olika användbara typer av information, t.ex platsen för de olika slutpunkter som krävs för att utföra autentisering med OpenID Connect. 
* Innehåller en `jwks_uri`, vilket ger platsen för en uppsättning offentliga nycklar som används för att signera token. JSON-dokumentet finns på den `jwks_uri` innehåller alla informationen om offentliga nyckeln används av den viss tidpunkten. Din app kan använda den `kid` anspråk i JWT-huvudet för att välja vilka offentliga nyckeln i det här dokumentet har använts för att logga en viss token. Den kan sedan utföra signaturverifiering med rätt offentlig nyckel och den angivna algoritmen.

> [!NOTE]
> V1.0 slutpunkten returnerar både den `x5t` och `kid` anspråk, medan v2.0-slutpunkten svarar med endast de `kid` anspråk. Framöver kommer vi rekommenderar att du använder den `kid` anspråk som ska verifiera din token.

Utför signaturverifiering ligger utanför omfånget för det här dokumentet – det finns många bibliotek med öppen källkod för att hjälpa dig göra det om det behövs.  Microsoft Identity-plattformen har dock en förlängning av standarder - anpassade Signeringsnycklar för tokensignering.  

Om din app har anpassade Signeringsnycklar användningen av den [mappning av anspråk](active-directory-claims-mapping.md) funktion, som du måste lägga till en `appid` frågeparameter som innehåller app-ID för att få en `jwks_uri` som pekar på din app signeringsnyckel information som ska användas för verifiering. Till exempel: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` innehåller en `jwks_uri` av `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Anspråksbaserad auktorisering

Ditt programs affärslogik bestämmer det här steget, vissa vanliga auktoriseringsmetoder presenteras nedan.

* Kontrollera den `scp` eller `roles` anspråk som ska kontrollera att alla finns scope matchar de som visas av ditt API och tillåter klienten att utföra den begärda åtgärden.
* Den anropande klienten har behörighet att anropa API med hjälp av den `appid` anspråk.
* Verifiera statusen för autentisering för den anropande klienten med hjälp av `appidacr` -det får inte vara 0 om offentliga klienter inte tillåts att anropa ditt API.
* Kontrollera mot en lista över tidigare `nonce` utger sig för att verifiera token inte spelas.
* Kontrollera att den `tid` matchar en klient som kan anropa ditt API.
* Använd den `acr` anspråk som ska verifiera användaren har utfört MFA. Detta ska tillämpas med hjälp av [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Om du har begärt den `roles` eller `groups` anspråk i åtkomsttoken, kontrollera att användaren är i gruppen får utföra den här åtgärden.
  * För token hämtas med hjälp av det implicita flödet, behöver du troligen fråga den [Microsoft Graph](https://developer.microsoft.com/graph/) för dessa data som det är ofta ryms inte i token.

## <a name="user-and-application-tokens"></a>Användar- och token

Ditt program kan ta emot tokens åt en användare (vanligt flöde) eller direkt från ett program (via den [flödet för klientautentiseringsuppgifter](v1-oauth2-client-creds-grant-flow.md)). Dessa appspecifika token tyda på att det här anropet kommer från ett program och inte har en användare som backar upp den. Dessa token ska hanteras i stort sett samma, med några skillnader:

* Appspecifika token har inte en `scp` anspråk och i stället måste en `roles` anspråk. Det här är där programbehörigheten (i stället för delegerade behörigheter) registreras. Mer information om behörigheter för delegerad och program finns i behörighet och ditt medgivande i [v1.0](v1-permissions-and-consent.md) och [v2.0](v2-permissions-and-consent.md).
* Många human-specifika anspråk kommer att saknas, till exempel `name`.

## <a name="token-revocation"></a>Återkallningen av token

Uppdatera token kan vara ogiltig eller återkallas när som helst av olika skäl. Dessa är indelade i två huvudsakliga kategorier: tidsgränser och återkallelse.

### <a name="token-timeouts"></a>Token tidsgränser

* MaxInactiveTime: Om uppdateringstoken inte har använts inom den tiden enligt MaxInactiveTime kan längre uppdatera Token inte giltig. 
* MaxSessionAge: Om du har angett MaxAgeSessionMultiFactor eller MaxAgeSessionSingleFactor till något annat än standard (tills-återkallas), sedan måste omautentisering utföras efter den i MaxAgeSession * tiden. 
* Exempel:
  * Klienten har en MaxInactiveTime på fem dagar och användaren gick på semester för en vecka och så AAD har inte sett en ny token begäran från användaren i 7 dagar. Nästa gång användaren begär en ny token hittar de sina uppdatera Token har återkallats och de måste ange sina autentiseringsuppgifter igen.
  * Ett känsliga program har en MaxAgeSessionSingleFactor på 1 dag. Om en användare loggar in på måndag och tisdagen (efter 25 timmar har gått), uppmanas de att autentiseras på nytt.

### <a name="revocation"></a>Återkallade certifikat

|   | Lösenordsbaserad cookie | Lösenordsbaserad token | Icke-den lösenordsbaserade cookie | Icke-den lösenordsbaserade token | Konfidentiell klient-token |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Lösenordet upphör att gälla | Förblir aktiv | Förblir aktiv | Förblir aktiv | Förblir aktiv | Förblir aktiv |
| Lösenordet har ändrats av användaren | Återkallad | Återkallad | Förblir aktiv | Förblir aktiv | Förblir aktiv |
| Användare utför SSPR | Återkallad | Återkallad | Förblir aktiv | Förblir aktiv | Förblir aktiv |
| Återställer lösenordet för serveradministratören | Återkallad | Återkallad | Förblir aktiv | Förblir aktiv | Förblir aktiv |
| Användaren återkallar sina uppdateringstoken [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Återkallad | Återkallad | Återkallad | Återkallad | Återkallad |
| Administratör återkallar alla uppdateringstoken för klienten [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Återkallad | Återkallad |Återkallad | Återkallad | Återkallad |
| [Enkel utloggning](v1-protocols-openid-connect-code.md#single-sign-out) på webben | Återkallad | Förblir aktiv | Återkallad | Förblir aktiv | Förblir aktiv |

> [!NOTE]
> En inloggning som ”icke-password baserad” är en där användaren inte ange ett lösenord för att hämta den. Till exempel använda ansiktet med Windows Hello, en FIDO-nyckel eller en PIN-kod. 
>
> Det finns ett känt problem med den Windows primära uppdatera Token. Om PRT hämtas via ett lösenord, och sedan en användare loggar in via Hello, ursprunget av PRT ändras inte och kommer att återkallas om användaren ändrar sitt lösenord.
>
> Uppdateringstoken är inte ogiltiga eller återkallas när används för att hämta en ny åtkomsttoken och uppdateringstoken.  

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [ `id_tokens` i Azure AD](id-tokens.md).
* Läs mer om behörighet och ditt medgivande i [v1.0](v1-permissions-and-consent.md) och [v2.0](v2-permissions-and-consent.md).
