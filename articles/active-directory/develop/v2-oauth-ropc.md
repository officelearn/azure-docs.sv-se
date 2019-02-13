---
title: Använda Azure AD v2.0 för att logga in användare med hjälp av ROPC | Microsoft Docs
description: Stöd för webbläsare utan autentisering flöden med resursen ägare lösenord credential grant.
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
ms.topic: article
ms.date: 11/28/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 716f48a5db437ef1dc865aca66fbac1c1d51aec3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207440"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-resource-owner-password-credential"></a>Azure Active Directory v2.0- och OAuth 2.0-resurs för resursägarlösenord

Azure Active Directory (Azure AD) stöder den [resource resursägarlösenord (ROPC) ge](https://tools.ietf.org/html/rfc6749#section-4.3), vilket gör att ett program för att logga in användaren genom att direkt hantera sitt lösenord. Flödet ROPC kräver en hög grad av förtroende och användaren exponering och utvecklare ska bara använda det här flödet när de andra och säkrare, flödena inte kan användas.

> [!Important]
> * Azure AD v2.0-slutpunkten har endast stöd för ROPC för Azure AD-klienter, inte personliga konton. Det innebär att du måste använda en klientspecifik slutpunkt (`https://login.microsoftonline.com/{TenantId_or_Name}`) eller `organizations` slutpunkt.
> * Personliga konton som är välkomna till en Azure AD-klient kan inte använda ROPC.
> * Konton som inte har lösenord kan inte logga in via ROPC. Det här scenariot rekommenderar vi att du använder ett annat flöde för din app i stället.
> * Om användare vill använda multifaktorautentisering (MFA) för att logga in till programmet kan kommer de att blockeras istället.

## <a name="protocol-diagram"></a>Protokollet diagram

Följande diagram visar ROPC flödet.

![ROPC flöde](media/v2-oauth2-ropc/v2-oauth-ropc.png)

## <a name="authorization-request"></a>Begäran om auktorisering

ROPC flödet är en enskild begäran&mdash;den skickar klienten identifiering och användarens autentiseringsuppgifter till IDP: N och sedan ta emot tokens i utbyte. Klienten måste begära användarens e-postadress (UPN) och lösenord innan du gör det. Omedelbart efter en lyckad begäran bör klienten på ett säkert sätt att släppa användarens autentiseringsuppgifter från minnet. Du måste spara dem aldrig.

```
// Line breaks and spaces are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token?

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&client_secret=wkubdywbc2894u
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Directory-klient som du vill logga in användaren i. Detta kan vara i GUID eller eget namnformat. Den här parametern kan inte anges till `common` eller `consumers`, men kan anges till `organizations`. |
| `grant_type` | Krävs | Måste anges till `password`. |
| `username` | Krävs | Användarens e-postadress. |
| `password` | Krävs | Användarens lösenord. |
| `scope` | Rekommenderas | En blankstegsavgränsad lista över [scope](v2-permissions-and-consent.md), eller behörigheter som appen kräver. Dessa scope måste vara samtyckt till förbereds i förväg av en administratör eller av användare i en interaktiv flöde. |

### <a name="successful-authentication-response"></a>Lyckad autentiseringssvar

Nedan visas ett exempel på ett lyckat svar för token:

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
| `token_type` | String | Alltid inställt `Bearer`. |
| `scope` | Avgränsade med blanksteg strängar | Om en åtkomsttoken returnerades listas scope åtkomsttoken är giltig för den här parametern. |
| `expires_in`| int | Antal sekunder som den inkluderade åtkomst-token är giltig för. |
| `access_token`| Täckande sträng | Utfärdats för den [scope](v2-permissions-and-consent.md) som begärdes. |
| `id_token` | JWT | Utfärdade om ursprungligt `scope` parameter som ingår i `openid` omfång. |
| `refresh_token` | Täckande sträng | Utfärdade om ursprungligt `scope` parametern ingår `offline_access`. |

Du kan använda uppdateringstoken för att hämta nya åtkomsttoken och uppdatera token med samma flöde som beskrivs i den [OAuth Code flow-dokumentation](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Felsvar

Om användaren inte har angett rätt användarnamn eller lösenord eller om klienten har inte mottagit det begärda medgivandet, misslyckas autentiseringen.

| Fel | Beskrivning | Klientåtgärd |
|------ | ----------- | -------------|
| `invalid_grant` | Autentiseringen misslyckades | Autentiseringsuppgifterna är felaktiga eller klienten har inte medgivande för de begärda omfång. Om du inte beviljats scope, en `consent_required` suberror kommer att returneras. Om detta inträffar kan ska klienten skicka användaren till en interaktiv prompt med hjälp av en webbvy eller webbläsare. |
| `invalid_request` | Begäran konstruerades felaktigt | Beviljandetypen stöds inte på den `/common` eller `/consumers` kontexter för autentisering.  Använd `/organizations` i stället. |
| `invalid_client` | Appen konfigureras felaktigt | Detta kan inträffa om den `allowPublicClient` egenskapen inte är inställt på true i den [programmanifestet](reference-app-manifest.md). Den `allowPublicClient` egenskapen är nödvändigt eftersom det ROPC beviljandet inte har en omdirigerings-URI. Azure AD kan inte fastställa om appen är en offentlig klientprogram eller ett konfidentiellt klientprogram, såvida inte egenskapen. Observera att ROPC stöds endast för offentliga klientappar. |

## <a name="learn-more"></a>Läs mer

* Prova att använda ROPC själv med hjälp av den [exempelprogrammet konsolen](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, [v2.0 begränsningar](active-directory-v2-limitations.md).
