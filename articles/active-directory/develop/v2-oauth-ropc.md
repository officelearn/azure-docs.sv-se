---
title: Använd Microsoft Identity Platform för att logga in användare som använder ROPC-beviljande (Resource Owner Password Credential) | Azure
description: Stöd webbläsare – mindre autentiseringar flödar med hjälp av autentiseringsuppgifter för resurs ägarens lösen ord.
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
ms.openlocfilehash: 7d5324aba5202abb76f07d1eaf43fe214e690393
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193216"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Microsoft Identity Platform och OAuth 2,0-autentiseringsuppgifter för resurs ägar lösen ord

Microsoft Identity Platform stöder [ROPC-tilldelningen för resurs ägar lösen ord](https://tools.ietf.org/html/rfc6749#section-4.3), vilket gör att ett program kan logga in användaren genom att direkt hantera sitt lösen ord. ROPC-flödet kräver en hög grad av förtroende och användar exponering och du bör endast använda det här flödet när andra, säkrare och flöden inte kan användas.

> [!IMPORTANT]
>
> * Slut punkten för Microsoft Identity Platform stöder endast ROPC för Azure AD-klienter, inte personliga konton. Det innebär att du måste använda en klient-/regionsspecifika slut punkt`https://login.microsoftonline.com/{TenantId_or_Name}`() `organizations` eller slut punkten.
> * Personliga konton som bjuds in till en Azure AD-klient kan inte använda ROPC.
> * Konton som inte har lösen ord kan inte logga in via ROPC. I det här scenariot rekommenderar vi att du använder ett annat flöde för appen i stället.
> * Om användarna behöver använda Multi-Factor Authentication (MFA) för att logga in i programmet kommer de att blockeras i stället.

## <a name="protocol-diagram"></a>Protokoll diagram

I följande diagram visas ROPC-flödet.

![Diagram som visar autentiseringsuppgifter för resurs ägarens lösen ord](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Begäran om auktorisering

ROPC-flödet är en enskild begäran: den skickar klient identifieringen och användarens autentiseringsuppgifter till IDP och tar sedan emot tokens i retur. Klienten måste begära användarens e-postadress (UPN) och lösen ord innan du gör detta. Omedelbart efter en lyckad begäran bör klienten på ett säkert sätt släppa användarens autentiseringsuppgifter från minnet. Det får aldrig spara dem.

> [!TIP]
> Försök att köra denna begäran i Postman!
> [![Försök att köra denna begäran i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required. 

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| `tenant` | Obligatorisk | Den katalog klient som du vill logga in användaren i. Detta kan vara i ett GUID eller eget namn format. Den här parametern kan inte anges `common` till `consumers`eller, men den kan vara `organizations`inställd på. |
| `client_id` | Obligatorisk | Det program (klient)-ID som den [Azure Portal-Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan har tilldelats till din app. | 
| `grant_type` | Obligatorisk | Måste anges till `password`. |
| `username` | Obligatorisk | Användarens e-postadress. |
| `password` | Obligatorisk | Användarens lösen ord. |
| `scope` | Rekommenderas | En blankstegsavgränsad lista med [omfattningar](v2-permissions-and-consent.md)eller behörigheter som appen kräver. I ett interaktivt flöde måste administratören eller användaren samtycka till dessa omfattningar i förväg. |
| `client_secret`| Krävs ibland | Om din app är en offentlig klient `client_secret` kan eller `client_assertion` inte tas med.  Om appen är en konfidentiell klient måste den tas med. | 
| `client_assertion` | Krävs ibland | En annan form av `client_secret`, som genereras med hjälp av ett certifikat.  Se [autentiseringsuppgifter för certifikat](active-directory-certificate-credentials.md) för mer information. | 

### <a name="successful-authentication-response"></a>Godkänt autentiserings svar

I följande exempel visas ett lyckat svar på token:

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
| `token_type` | Sträng | Ställ alltid in `Bearer`på. |
| `scope` | Separerade blankstegsavgränsad strängar | Om en åtkomsttoken returnerades listar den här parametern de omfattningar som åtkomsttoken är giltig för. |
| `expires_in`| int | Antal sekunder som den inkluderade åtkomsttoken är giltig för. |
| `access_token`| Ogenomskinlig sträng | Utfärdat för de [omfattningar](v2-permissions-and-consent.md) som begärdes. |
| `id_token` | JWT | Utfärdas om den ursprungliga `scope` parametern `openid` omfattade omfånget. |
| `refresh_token` | Ogenomskinlig sträng | Utfärdas om den ursprungliga `scope` parametern ingår `offline_access`. |

Du kan använda uppdateringstoken för att hämta nya åtkomsttoken och uppdatera tokens med samma flöde som beskrivs i [dokumentationen för OAuth-kod flödet](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Fel svar

Om användaren inte har angett rätt användar namn eller lösen ord, eller om klienten inte har fått det begärda godkännandet, kommer autentiseringen att Miss sen.

| Fel | Beskrivning | Klient åtgärd |
|------ | ----------- | -------------|
| `invalid_grant` | Autentiseringen misslyckades | Autentiseringsuppgifterna var felaktiga eller så har klienten inte tillstånd för de begärda omfattningarna. Om omfången inte beviljas returneras ett `consent_required` fel. Om detta inträffar ska klienten skicka användaren till en interaktiv prompt med en webbvy eller webbläsare. |
| `invalid_request` | Begäran har inte konstruerats korrekt | Anslags typen stöds inte i `/common` kontexterna eller `/consumers` .  Använd `/organizations` eller ett klient-ID i stället. |

## <a name="learn-more"></a>Lär dig mer

* Testa ROPC för dig själv med [exempel konsol programmet](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* För att avgöra om du ska använda v 2.0-slutpunkten läser du om [begränsningar för Microsoft Identity Platform](active-directory-v2-limitations.md).
