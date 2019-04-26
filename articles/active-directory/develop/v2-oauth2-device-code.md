---
title: Använd Microsoft identity-plattformen för att logga in på webbläsare utan enheter | Azure
description: Skapa inbäddad och ge webbläsaren utan autentiseringsflöden med hjälp av kod för enheten.
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
ms.date: 04/20/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 703416788d123798774802613d71b30e8fbdaa9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299417"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Microsoft identity-plattformen och kodflöde för OAuth 2.0-enhet

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft identity-plattformen stöder den [enheten beviljande via](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), vilket gör att användare att logga in på indata begränsad enheter, till exempel en smart-TV, IoT-enheter och skrivare.  Om du vill aktivera det här flödet har enheten användaren gå till en webbsida i webbläsaren på en annan enhet för att logga in.  När användaren loggar in, kan enheten att få åtkomst-token och uppdatera token efter behov.  

> [!IMPORTANT]
> För närvarande stöder Microsoft identity-plattformen endpoint endast enheten flödet för Azure AD-klienter, men inte personliga konton.  Det innebär att du måste använda en slutpunkt som en klient eller `organizations` slutpunkt.  
>
> Personliga konton som är välkomna till en Azure AD-klient kommer att kunna använda det för enheten flöde, men endast i kontexten för klienten.

> [!NOTE]
> Microsoft identity-plattformen slutpunkt stöder inte alla Azure Active Directory-scenarier och funktioner. Läs mer om för att avgöra om du ska använda Microsoft identity-plattformen endpoint, [plattformsbegränsningar för Microsoft identity](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Protokollet diagram

Kodflöde för hela enheten ser ut ungefär så i nästa diagram. Vi beskriver varje steg nedan.

![Enhetskodflöde](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Begäran om godkännande för enhet

Klienten måste först kontrollera med authentication-servern om en enhets- och kod som används för att initiera autentisering. Klienten samlar in den här begäran från den `/devicecode` slutpunkt. I den här begäran bör klienten ta de behörigheter som krävs för att läsa från användaren. Från det ögonblick då denna begäran skickas, användaren har bara 15 minuter att logga in (vanligt värdet för `expires_in`), så att endast göra denna begäran när användaren har angett de är redo att logga in.

> [!TIP]
> Försök att köra den här begäran i Postman!
> [![Kör i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| `tenant` | Obligatoriskt |Directory-klient som du vill begära behörighet från. Detta kan vara i GUID eller eget namnformat.  |
| `client_id` | Obligatoriskt | Den **(klient)-ID: T** som den [Azure-portalen – appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelse som tilldelats din app. |
| `scope` | Rekommenderas | En blankstegsavgränsad lista över [scope](v2-permissions-and-consent.md) som du vill att användaren att godkänna.  |

### <a name="device-authorization-response"></a>Svaret för auktorisering av enheten

Ett lyckat svar är ett JSON-objekt som innehåller informationen som krävs för att tillåta användare att logga in.  

| Parameter | Format | Beskrivning |
| ---              | --- | --- |
|`device_code`     | String | En lång sträng som används för att verifiera sessionen mellan klienten och auktoriseringsservern. Klienten använder den här parametern för att begära åtkomsttoken från auktoriseringsservern. |
|`user_code`       | String | En kort sträng som visas för användaren som används för att identifiera session på en sekundär enhet.|
|`verification_uri`| URI | URI: N som användaren bör gå till med den `user_code` för att kunna logga in. |
|`verification_uri_complete`| URI | En URI som kombinerar de `user_code` och `verification_uri`, som används för bilder överföring för användaren (till exempel via Bluetooth till en enhet eller via en QR-kod).  |
|`expires_in`      | int | Antalet sekunder innan den `device_code` och `user_code` upphör att gälla. |
|`interval`        | int | Hur många sekunder som klienten ska vänta mellan avsökningen begäranden. |
| `message`        | String | En läsbara sträng med instruktioner för användaren. Detta kan lokaliseras genom att inkludera en **frågeparameter** i begäran i formatet `?mkt=xx-XX`, fylla i lämplig kultur språkkoden. |

## <a name="authenticating-the-user"></a>Autentisering av användaren

När du tar emot den `user_code` och `verification_uri`, klienten visar dem till användare, instruera dem att logga in med sina mobila telefonnummer eller en PC-webbläsaren.  Klienten kan dessutom använda en QR-kod eller liknande mekanism för att visa den `verfication_uri_complete`, som tar steget i att ange den `user_code` för användaren.

När användaren autentiseras på den `verification_uri`, klienten ska avsöka den `/token` slutpunkt för den begärda token med hjälp av den `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parameter | Obligatoriskt | Beskrivning|
| -------- | -------- | ---------- |
| `grant_type` | Obligatoriskt | Måste vara `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obligatoriskt | Måste matcha den `client_id` används i den första begäran. |
| `device_code`| Obligatoriskt | Den `device_code` returneras i auktoriseringsbegäran för enheten.  |

### <a name="expected-errors"></a>Förväntat fel

Kodflöde för enheten är ett protokoll för avsökning så att din klient måste förväntar dig att få fel innan användaren är klar med autentisering.  

| Fel | Beskrivning | Klientåtgärd |
| ------ | ----------- | -------------|
| `authorization_pending` | Användaren har inte slutförts autentisering, men har inte avbrutits flödet. | Upprepa begäran när du har minst `interval` sekunder. |
| `authorization_declined` | Användaren nekade begäran om godkännande.| Stoppa avsökning och återgå till ett icke-autentiserade tillstånd.  |
| `bad_verification_code`| Den `device_code` skickas till den `/token` endpoint kändes inte igen. | Kontrollera att klienten skickar rätt `device_code` i begäran. |
| `expired_token` | Minst `expires_in` sekunder har genomgått och autentisering kan inte längre med den här `device_code`. | Stoppa avsökning och återgå till ett icke-autentiserade tillstånd. |

### <a name="successful-authentication-response"></a>Lyckad autentiseringssvar

Ett lyckat svar för token kommer att se ut:

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
| `token_type` | String| Alltid ”Ägarautentisering. |
| `scope` | Avgränsade med blanksteg strängar | Om en åtkomsttoken returnerades listas detta scope åtkomsttoken är giltig för. |
| `expires_in`| int | Antalet sekunder innan den inkluderade åtkomst-token är giltig för. |
| `access_token`| Täckande sträng | Utfärdats för den [scope](v2-permissions-and-consent.md) som begärdes.  |
| `id_token`   | JWT | Utfärdade om ursprungligt `scope` parameter som ingår i `openid` omfång.  |
| `refresh_token` | Täckande sträng | Utfärdade om ursprungligt `scope` parametern ingår `offline_access`.  |

Du kan använda uppdateringstoken för att hämta nya åtkomsttoken och uppdatera token med samma flöde dokumenterade i den [OAuth Code flow-dokumentation](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
