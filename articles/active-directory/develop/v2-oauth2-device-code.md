---
title: Använd Microsoft Identity Platform för att logga in användare i webbläsare-enheter som är mindre | Azure
description: Bygg inbäddade och webbläsar lösa autentiserings flöden med hjälp av enhets kod tilldelning.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdd99899494e9f7b3c0caa4e83f18803b969db1e
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192712"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Microsoft Identity Platform och OAuth 2,0-enhetens kod flöde

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft Identity Platform stöder enhets [kod tilldelningen](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), vilket gör att användarna kan logga in på inmatade enheter, till exempel en smart TV, IoT-enhet eller skrivare.  Om du vill aktivera det här flödet har enheten användaren besöka en webb sida i webbläsaren på en annan enhet för att logga in.  När användaren loggar in kan enheten hämta åtkomsttoken och uppdatera tokens efter behov.  

> [!IMPORTANT]
> För tillfället stöder slut punkten för Microsoft Identity Platform endast enhets flödet för Azure AD-klienter, men inte personliga konton.  Det innebär att du måste använda en slut punkt som kon figurer ATS som en klient `organizations` eller slut punkten.  Detta stöd kommer snart att aktive ras. 
>
> Personliga konton som bjuds in till en Azure AD-klient kan använda enhets flödes tilldelningen, men endast i klient organisationens kontext.
>
> Som en extra anteckning `verification_uri_complete` ingår eller stöds inte svars fältet för tillfället.  Vi nämner detta eftersom om du läser den standard som `verification_uri_complete` visas som en valfri del av enhets kod flödet standard.

> [!NOTE]
> Slut punkten för Microsoft Identity Platform stöder inte alla Azure Active Directory scenarier och funktioner. För att avgöra om du ska använda Microsoft Identity Platform-slutpunkten läser du om [begränsningar för Microsoft Identity Platform](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Protokoll diagram

Hela enhets kod flödet ser ut ungefär som nästa diagram. Vi beskriver vart och ett av stegen längre fram i den här artikeln.

![Enhetskodflöde](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Begäran om enhets godkännande

Klienten måste först kontrol lera med autentiseringsservern för en enhet och användar kod som används för att initiera autentiseringen. Klienten samlar in den här begäran från `/devicecode` slut punkten. I den här begäran ska klienten även innehålla de behörigheter som krävs för att hämta från användaren. Från den tidpunkt då den här begäran skickas har användaren bara 15 minuter på sig att logga in (det vanliga värdet `expires_in`för), så gör bara den här begäran när användaren har angett att de är redo att logga in.

> [!TIP]
> Försök att köra denna begäran i Postman!
> [![Försök att köra denna begäran i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| `tenant` | Obligatorisk |Den katalog klient som du vill begära behörighet från. Detta kan vara i ett GUID eller eget namn format.  |
| `client_id` | Obligatorisk | **Program-ID: t (klienten)** som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen som har tilldelats din app. |
| `scope` | Rekommenderas | En blankstegsavgränsad lista med [omfattningar](v2-permissions-and-consent.md) som du vill att användaren ska godkänna.  |

### <a name="device-authorization-response"></a>Svar på enhets auktorisering

Ett lyckat svar är ett JSON-objekt som innehåller den information som krävs för att tillåta användaren att logga in.  

| Parameter | Format | Beskrivning |
| ---              | --- | --- |
|`device_code`     | Sträng | En lång sträng som används för att verifiera sessionen mellan klienten och auktoriseringsservern. Klienten använder den här parametern för att begära åtkomsttoken från auktoriseringsservern. |
|`user_code`       | Sträng | En kort sträng som visas för den användare som används för att identifiera sessionen på en sekundär enhet.|
|`verification_uri`| URI | Den URI som användaren ska gå till med `user_code` i för att logga in. |
|`expires_in`      | int | Antalet sekunder innan `device_code` och `user_code` upphör att gälla. |
|`interval`        | int | Antalet sekunder som klienten ska vänta mellan avsöknings begär Anden. |
| `message`        | Sträng | En läslig sträng med instruktioner för användaren. Detta kan lokaliseras genom att inkludera en **frågeparameter** i formulärets `?mkt=xx-XX`begäran och fylla i lämplig språk kultur kod. |

## <a name="authenticating-the-user"></a>Autentisera användaren

När klienten har `user_code` tagit `verification_uri`emot, visar klienten dessa för användaren och instruerar dem att logga in med sin mobil telefon eller PC-webbläsare.  Dessutom kan klienten använda en QR `verfication_uri_complete`-kod eller liknande mekanism för att visa, som tar steget att `user_code` ange för användaren.

När användaren autentiseras på `verification_uri`, ska klienten avsöka `/token` slut punkten för `device_code`den begärda token med hjälp av.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parameter | Obligatorisk | Beskrivning|
| -------- | -------- | ---------- |
| `grant_type` | Obligatorisk | Måste vara`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obligatorisk | Måste matcha det `client_id` som används i den första begäran. |
| `device_code`| Obligatorisk | `device_code` Returnerade i begäran om enhets godkännande.  |

### <a name="expected-errors"></a>Förväntade fel

Enhets kod flödet är ett avsöknings protokoll så att klienten måste vänta på att få fel innan användaren har autentiserats.  

| Fel | Beskrivning | Klient åtgärd |
| ------ | ----------- | -------------|
| `authorization_pending` | Användaren har inte autentiserat autentisering, men har inte avbrutit flödet. | Upprepa begäran efter minst `interval` sekunder. |
| `authorization_declined` | Slutanvändaren nekade begäran om auktorisering.| Stoppa avsökningen och återgå till ett oautentiserat tillstånd.  |
| `bad_verification_code`| `device_code` Skickade`/token` till slut punkten kändes inte igen. | Kontrol lera att klienten skickar rätt `device_code` i begäran. |
| `expired_token` | Minst `expires_in` sekunder har passerat och autentisering är inte längre möjligt med detta `device_code`. | Stoppa avsökningen och återgå till ett oautentiserat tillstånd. |

### <a name="successful-authentication-response"></a>Godkänt autentiserings svar

Ett lyckat svar på token kommer att se ut så här:

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
| `token_type` | Sträng| Alltid "Bearer. |
| `scope` | Separerade blankstegsavgränsad strängar | Om en åtkomsttoken returnerades listar det de omfattningar som åtkomsttoken är giltig för. |
| `expires_in`| int | Antal sekunder innan den inkluderade åtkomsttoken är giltig för. |
| `access_token`| Ogenomskinlig sträng | Utfärdat för de [omfattningar](v2-permissions-and-consent.md) som begärdes.  |
| `id_token`   | JWT | Utfärdas om den ursprungliga `scope` parametern `openid` omfattade omfånget.  |
| `refresh_token` | Ogenomskinlig sträng | Utfärdas om den ursprungliga `scope` parametern ingår `offline_access`.  |

Du kan använda uppdateringstoken för att hämta nya åtkomsttoken och uppdatera tokens med samma flöde som dokumenteras i [dokumentationen för OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token)-kodaren.  
