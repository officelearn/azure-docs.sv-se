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
ms.date: 10/11/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 322e0e5f740bd416c7831f32e0d74f9290335fe3
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195754"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credentials"></a>Microsoft Identity Platform och OAuth-autentiseringsuppgifter för resurs ägar lösen ord för OAuth 2,0

Microsoft Identity Platform stöder [OAuth 2,0-ROPC (Resource Owner Password Credential)](https://tools.ietf.org/html/rfc6749#section-4.3)som gör att ett program kan logga in användaren genom att direkt hantera lösen ordet.

> [!WARNING]
> Microsoft rekommenderar att du _inte_ använder ROPC-flödet. I de flesta fall är fler säkra alternativ tillgängliga och rekommenderade. Det här flödet kräver en mycket hög förtroende nivå i programmet och medför risker som inte finns i andra flöden. Du bör endast använda det här flödet när det inte går att använda andra säkrare flöden.

> [!IMPORTANT]
>
> * Slut punkten för Microsoft Identity Platform stöder endast ROPC för Azure AD-klienter, inte personliga konton. Det innebär att du måste använda en klient-/regionsspecifika slut punkt (`https://login.microsoftonline.com/{TenantId_or_Name}`) eller `organizations` slut punkten.
> * Personliga konton som bjuds in till en Azure AD-klient kan inte använda ROPC.
> * Konton som inte har lösen ord kan inte logga in via ROPC. I det här scenariot rekommenderar vi att du använder ett annat flöde för appen i stället.
> * Om användarna behöver använda Multi-Factor Authentication (MFA) för att logga in i programmet kommer de att blockeras i stället.
> * ROPC stöds inte i scenarier med [hybrid identitets Federation](/azure/active-directory/hybrid/whatis-fed) (till exempel Azure AD och ADFS som används för att autentisera lokala konton). Om användarna är fulla omdirigerade till en lokal identitets leverantör kan inte Azure AD testa användar namn och lösen ord mot identitets leverantören. [Direktautentisering](/azure/active-directory/hybrid/how-to-connect-pta) stöds med ROPC, men.

## <a name="protocol-diagram"></a>Protokoll diagram

I följande diagram visas ROPC-flödet.

![Diagram som visar autentiseringsuppgifter för resurs ägarens lösen ord](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Auktoriseringsbegäran

ROPC-flödet är en enskild begäran: den skickar klient identifieringen och användarens autentiseringsuppgifter till IDP och tar sedan emot tokens i retur. Klienten måste begära användarens e-postadress (UPN) och lösen ord innan du gör detta. Omedelbart efter en lyckad begäran bör klienten på ett säkert sätt släppa användarens autentiseringsuppgifter från minnet. Det får aldrig spara dem.

> [!TIP]
> Försök att köra denna begäran i Postman!
> [Försök att köra denna begäran i Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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
| `tenant` | Krävs | Den katalog klient som du vill logga in användaren i. Detta kan vara i ett GUID eller eget namn format. Den här parametern kan inte anges till `common` eller `consumers`, men kan vara inställd på `organizations`. |
| `client_id` | Krävs | Det program (klient)-ID som den [Azure Portal-Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan har tilldelats till din app. | 
| `grant_type` | Krävs | Måste anges till `password`. |
| `username` | Krävs | Användarens e-postadress. |
| `password` | Krävs | Användarens lösen ord. |
| `scope` | Rekommenderas | En blankstegsavgränsad lista med [omfattningar](v2-permissions-and-consent.md)eller behörigheter som appen kräver. I ett interaktivt flöde måste administratören eller användaren samtycka till dessa omfattningar i förväg. |
| `client_secret`| Krävs ibland | Om din app är en offentlig klient kan `client_secret` eller `client_assertion` inte tas med.  Om appen är en konfidentiell klient måste den tas med. | 
| `client_assertion` | Krävs ibland | En annan form av `client_secret`som genereras med hjälp av ett certifikat.  Se [autentiseringsuppgifter för certifikat](active-directory-certificate-credentials.md) för mer information. | 

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
| `token_type` | Sträng | Ange alltid till `Bearer`. |
| `scope` | Separerade blankstegsavgränsad strängar | Om en åtkomsttoken returnerades listar den här parametern de omfattningar som åtkomsttoken är giltig för. |
| `expires_in`| int | Antal sekunder som den inkluderade åtkomsttoken är giltig för. |
| `access_token`| Ogenomskinlig sträng | Utfärdat för de [omfattningar](v2-permissions-and-consent.md) som begärdes. |
| `id_token` | JWT | Utfärdas om den ursprungliga `scope`-parametern innehöll `openid`s området. |
| `refresh_token` | Ogenomskinlig sträng | Utfärdas om den ursprungliga `scope` parametern ingår `offline_access`. |

Du kan använda uppdateringstoken för att hämta nya åtkomsttoken och uppdatera tokens med samma flöde som beskrivs i [dokumentationen för OAuth-kod flödet](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Fel svar

Om användaren inte har angett rätt användar namn eller lösen ord, eller om klienten inte har fått det begärda godkännandet, kommer autentiseringen att Miss sen.

| Fel | Beskrivning | Klient åtgärd |
|------ | ----------- | -------------|
| `invalid_grant` | Autentiseringen misslyckades | Autentiseringsuppgifterna var felaktiga eller så har klienten inte tillstånd för de begärda omfattningarna. Om omfången inte beviljas returneras ett `consent_required` fel. Om detta inträffar ska klienten skicka användaren till en interaktiv prompt med en webbvy eller webbläsare. |
| `invalid_request` | Begäran har inte konstruerats korrekt | Anslags typen stöds inte på `/common` eller `/consumers` autentiserings kontexter.  Använd `/organizations` eller ett klient-ID i stället. |

## <a name="learn-more"></a>Läs mer

* Testa ROPC för dig själv med [exempel konsol programmet](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* För att avgöra om du ska använda v 2.0-slutpunkten läser du om [begränsningar för Microsoft Identity Platform](active-directory-v2-limitations.md).
