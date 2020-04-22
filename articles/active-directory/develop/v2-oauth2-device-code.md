---
title: Kodflöde för OAuth 2.0-enheter | Azure
titleSuffix: Microsoft identity platform
description: Logga in användare utan webbläsare. Skapa inbäddade och webbläsarlösa autentiseringsflöden med hjälp av enhetsauktoriseringsbidraget.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 2a39dbb3676df5ed916203bdcbbc51d5a0da32a4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677837"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft-identitetsplattform och tilldelningsflödet för OAuth 2.0-enheter

Microsofts identitetsplattform stöder [tilldelningen](https://tools.ietf.org/html/rfc8628)av enheten , vilket gör att användare kan logga in på indatabegränsade enheter som en smart-TV, IoT-enhet eller skrivare.  För att aktivera det här flödet har enheten användaren som besöker en webbsida i sin webbläsare på en annan enhet för att logga in.  När användaren loggar in kan enheten få åtkomsttoken och uppdatera token efter behov.

I den här artikeln beskrivs hur du programmerar direkt mot protokollet i ditt program.  När det är möjligt rekommenderar vi att du använder de Microsoft Authentication Libraries (MSAL) som stöds i stället för att [hämta token och anropa skyddade webb-API:er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på [exempelapparna som använder MSAL](sample-v2-code.md).

## <a name="protocol-diagram"></a>Protokolldiagram

Hela enhetskodflödet liknar nästa diagram. Vi beskriver vart och ett av stegen senare i den här artikeln.

![Flöde av enhetskod](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Begäran om enhetsauktorisering

Klienten måste först kontrollera med autentiseringsservern för en enhet och användarkod som används för att initiera autentisering. Klienten samlar in den `/devicecode` här begäran från slutpunkten. I den här begäran bör klienten också inkludera de behörigheter som den behöver för att hämta från användaren. Från det ögonblick den här begäran skickas har användaren bara 15 `expires_in`minuter att logga in (det vanliga värdet för), så gör bara den här begäran när användaren har angett att de är redo att logga in.

> [!TIP]
> Prova att köra denna begäran i Postman!
> [![Prova att köra den här begäran i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Kan vara /common, /consumers eller /organizations.  Det kan också vara den katalogklient som du vill begära behörighet från i GUID- eller eget namnformat.  |
| `client_id` | Krävs | **Program-ID (klient)** som [Azure-portalen – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) erfarenhet som tilldelats din app. |
| `scope` | Rekommenderas | En utrymmesavgränsad lista över [scope](v2-permissions-and-consent.md) som du vill att användaren ska godkänna.  |

### <a name="device-authorization-response"></a>Svar på enhetsauktorisering

Ett lyckat svar blir ett JSON-objekt som innehåller den information som krävs för att användaren ska kunna logga in.

| Parameter | Format | Beskrivning |
| ---              | --- | --- |
|`device_code`     | Sträng | En lång sträng som används för att verifiera sessionen mellan klienten och auktoriseringsservern. Klienten använder den här parametern för att begära åtkomsttoken från auktoriseringsservern. |
|`user_code`       | Sträng | En kort sträng som visas för användaren som används för att identifiera sessionen på en sekundär enhet.|
|`verification_uri`| URI | Den URI som användaren ska `user_code` gå till med för att logga in. |
|`expires_in`      | int | Antalet sekunder före `device_code` och `user_code` förfaller. |
|`interval`        | int | Antalet sekunder som klienten ska vänta mellan avsökningsbegäranden. |
| `message`        | Sträng | En sträng som kan läsas av människor med instruktioner för användaren. Detta kan lokaliseras genom att inkludera en **frågeparameter** i begäran av formuläret `?mkt=xx-XX`, fylla i lämplig språkkulturkod. |

> [!NOTE]
> Svarsfältet `verification_uri_complete` ingår inte eller stöds för tillfället.  Vi nämner detta eftersom [standard](https://tools.ietf.org/html/rfc8628) om du `verification_uri_complete` läser standarden du ser som anges som en valfri del av enhetens kodflödesstandard.

## <a name="authenticating-the-user"></a>Autentisera användaren

När du `user_code` `verification_uri`har tagit emot och visar klienten dessa för användaren och instruerar dem att logga in med sin mobiltelefon eller datorwebbläsare.

Om användaren autentiserar med ett personligt konto (på /common eller /consumers) uppmanas han att logga in igen för att överföra autentiseringstillståndet till enheten.  De kommer också att uppmanas att ge sitt samtycke, för att säkerställa att de är medvetna om de tillstånd som beviljas.  Detta gäller inte för arbets- eller skolkonton som används för att autentisera.

Medan användaren autentiserar `verification_uri`vid , ska klienten avsöka `/token` slutpunkten `device_code`för den begärda token med hjälp av .

```
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parameter | Krävs | Beskrivning|
| -------- | -------- | ---------- |
| `tenant`  | Krävs | Samma klient- eller klientalias som används i den ursprungliga begäran. |
| `grant_type` | Krävs | Måste vara`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Krävs | Måste matcha `client_id` den som används i den ursprungliga begäran. |
| `device_code`| Krävs | Den `device_code` returnerade i enhetsauktoriseringsbegäran.  |

### <a name="expected-errors"></a>Förväntade fel

Enhetskodflödet är ett avsökningsprotokoll så att klienten måste förvänta sig att få fel innan användaren har autentiserats.

| Fel | Beskrivning | Klientåtgärd |
| ------ | ----------- | -------------|
| `authorization_pending` | Användaren har inte autentiserats klart, men har inte avbrutit flödet. | Upprepa begäran efter `interval` minst några sekunder. |
| `authorization_declined` | Slutanvändaren nekade auktoriseringsbegäran.| Stoppa avsökning och återgå till ett oautentiserade tillstånd.  |
| `bad_verification_code`| Skickade `device_code` till `/token` slutpunkten kändes inte igen. | Kontrollera att klienten skickar `device_code` rätt i begäran. |
| `expired_token` | Minst `expires_in` sekunder har gått och autentisering är `device_code`inte längre möjligt med detta . | Stoppa avsökningen och återgå till ett oautentiserade tillstånd. |

### <a name="successful-authentication-response"></a>Lyckat autentiseringssvar

Ett lyckat tokensvar kommer att se ut:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parameter | Format | Beskrivning |
| --------- | ------ | ----------- |
| `token_type` | Sträng| Alltid "Bärare. |
| `scope` | Utrymme separerade strängar | Om en åtkomsttoken returnerades visar detta de scope som åtkomsttoken är giltig för. |
| `expires_in`| int | Antal sekunder innan den inkluderade åtkomsttoken är giltig för. |
| `access_token`| Ogenomskinlig sträng | Utfärdas för de scope som [begärdes.](v2-permissions-and-consent.md)  |
| `id_token`   | Jwt | Utfärdas om den `scope` ursprungliga `openid` parametern inkluderade scopet.  |
| `refresh_token` | Ogenomskinlig sträng | Utfärdas om den `scope` ursprungliga `offline_access`parametern ingår .  |

Du kan använda uppdateringstoken för att hämta nya åtkomsttoken och uppdatera token med samma flöde som dokumenteras i [OAuth-kodflödesdokumentationen](v2-oauth2-auth-code-flow.md#refresh-the-access-token).
