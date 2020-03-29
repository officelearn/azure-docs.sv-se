---
title: Logga in med tilldelning av lösenord för resursägare | Azure
titleSuffix: Microsoft identity platform
description: Stöd för autentiseringsflöden som är mindre än webbläsare med hjälp av gPC-bidraget (Resource Owner Password Credential).
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
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b935ad2491ca486a3bc6878f0332e5390600b1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76700693"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft-identitetsplattform och OAuth 2.0 Lösenord för resursägare

Microsoft identity-plattformen stöder [bidraget OAuth 2.0 Resource Owner Password (ROPC),](https://tools.ietf.org/html/rfc6749#section-4.3)som gör det möjligt för ett program att logga in användaren genom att direkt hantera deras lösenord.  I den här artikeln beskrivs hur du programmerar direkt mot protokollet i ditt program.  När det är möjligt rekommenderar vi att du använder de Microsoft Authentication Libraries (MSAL) som stöds i stället för att [hämta token och anropa skyddade webb-API:er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på [exempelapparna som använder MSAL](sample-v2-code.md).

> [!WARNING]
> Microsoft rekommenderar att du _inte_ använder ROPC-flödet. I de flesta fall finns säkrare alternativ tillgängliga och rekommenderas. Detta flöde kräver en mycket hög grad av förtroende för ansökan, och medför risker som inte finns i andra flöden. Du bör bara använda det här flödet när andra säkrare flöden inte kan användas.

> [!IMPORTANT]
>
> * Slutpunkten för Microsoft-identitetsplattform stöder endast ROPC för Azure AD-klienter, inte personliga konton. Det innebär att du måste använda en`https://login.microsoftonline.com/{TenantId_or_Name}`klientspecifik `organizations` slutpunkt ( ) eller slutpunkten.
> * Personliga konton som är inbjudna till en Azure AD-klient kan inte använda ROPC.
> * Konton som inte har lösenord kan inte logga in via ROPC. I det här scenariot rekommenderar vi att du använder ett annat flöde för din app i stället.
> * Om användare behöver använda MFA (Multi Factor Authentication) för att logga in på programmet blockeras de i stället.
> * ROPC stöds inte i [hybrididentitetsfederationsscenarier](/azure/active-directory/hybrid/whatis-fed) (till exempel Azure AD och ADFS som används för att autentisera lokala konton). Om användare omdirigeras till en lokal identitetsleverantörer kan Azure AD inte testa användarnamnet och lösenordet mot den identitetsprovidern. [Direktautentisering](/azure/active-directory/hybrid/how-to-connect-pta) stöds dock med ROPC.

## <a name="protocol-diagram"></a>Protokolldiagram

Följande diagram visar ROPC-flödet.

![Diagram som visar resursägarens lösenordsautentiseringsflöde](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Begäran om auktorisering

ROPC-flödet är en enda begäran: det skickar klientidentifieringen och användarens autentiseringsuppgifter till IDP och tar sedan emot token i gengäld. Klienten måste begära användarens e-postadress (UPN) och lösenord innan du gör det. Omedelbart efter en lyckad begäran bör klienten på ett säkert sätt frigöra användarens autentiseringsuppgifter från minnet. Det får aldrig rädda dem.

> [!TIP]
> Prova att köra denna begäran i Postman!
> [![Prova att köra den här begäran i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Den katalogklient som du vill logga in användaren i. Detta kan vara i GUID eller eget namnformat. Den här parametern kan `common` `consumers`inte ställas in `organizations`på eller , men kan ställas in på . |
| `client_id` | Krävs | Program-ID (klient) som [sidan Azure Portal - Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) har tilldelats din app. | 
| `grant_type` | Krävs | Måste ställas `password`in på . |
| `username` | Krävs | Användarens e-postadress. |
| `password` | Krävs | Användarens lösenord. |
| `scope` | Rekommenderas | En utrymmesavgränsad lista över [scope](v2-permissions-and-consent.md)eller behörigheter som appen kräver. I ett interaktivt flöde måste administratören eller användaren samtycka till dessa scope i förväg. |
| `client_secret`| Ibland krävs | Om din app är en `client_secret` offentlig `client_assertion` klient, då eller inte kan inkluderas.  Om appen är en konfidentiell klient måste den inkluderas. | 
| `client_assertion` | Ibland krävs | En annan `client_secret`form av , genereras med hjälp av ett certifikat.  Mer information finns i [certifikatuppgifter.](active-directory-certificate-credentials.md) | 

### <a name="successful-authentication-response"></a>Lyckat autentiseringssvar

I följande exempel visas ett lyckat tokensvar:

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
| `token_type` | String | Alltid inställd `Bearer`på . |
| `scope` | Utrymme separerade strängar | Om en åtkomsttoken returnerades visar den här parametern de scope som åtkomsttoken är giltig för. |
| `expires_in`| int | Antal sekunder som den inkluderade åtkomsttoken är giltig för. |
| `access_token`| Ogenomskinlig sträng | Utfärdas för de scope som [begärdes.](v2-permissions-and-consent.md) |
| `id_token` | Jwt | Utfärdas om den `scope` ursprungliga `openid` parametern inkluderade scopet. |
| `refresh_token` | Ogenomskinlig sträng | Utfärdas om den `scope` ursprungliga `offline_access`parametern ingår . |

Du kan använda uppdateringstoken för att hämta nya åtkomsttoken och uppdatera token med samma flöde som beskrivs i [OAuth-kodflödesdokumentationen](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Felsvar

Om användaren inte har angett rätt användarnamn eller lösenord, eller om klienten inte har fått det begärda medgivandet, misslyckas autentiseringen.

| Fel | Beskrivning | Klientåtgärd |
|------ | ----------- | -------------|
| `invalid_grant` | Autentiseringen misslyckades | Autentiseringsuppgifterna var felaktiga eller så har klienten inte medgivande för de begärda scope. Om scopen inte beviljas returneras ett `consent_required` fel. Om detta inträffar ska klienten skicka användaren till en interaktiv uppmaning med hjälp av en webbvy eller webbläsare. |
| `invalid_request` | Begäran konstruerades felaktigt | Bidragstypen stöds inte `/common` på `/consumers` kontexterna eller autentiseringskontexterna.  Använd `/organizations` eller ett klient-ID i stället. |

## <a name="learn-more"></a>Läs mer

* Prova ROPC själv med hjälp av [exempelkonsolprogrammet](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* För att avgöra om du ska använda v2.0-slutpunkten läser du om begränsningar av [Microsofts identitetsplattform](active-directory-v2-limitations.md).
