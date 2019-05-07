---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075152"
---
### <a name="authenticationresult-properties-in-msalnet"></a>AuthenticationResult egenskaper i MSAL.NET

Metoder för att hämta token returneras en `AuthenticationResult` (eller, för asynkrona metoder, en `Task<AuthenticationResult>`.

I MSAL.NET, `AuthenticationResult` visar:

- `AccessToken` för webb-API för åtkomst till resurser. Den här parametern är en sträng, vanligtvis en base64-kodade JWT men klienten aldrig bör se ut i åtkomsttoken. Formatet är inte garanterad är stabil och den vara krypterad för resursen. Personer skriva kod beroende på åtkomst-token innehåll på klienten är en av de största källorna av fel och klienten logic sidbrytningar. Se även [åtkomsttoken](../articles/active-directory/develop/access-tokens.md)
- `IdToken` för användaren (den här parametern är en kodad JWT). Se [ID-token](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` Visar datum och tid när token upphör att gälla
- `TenantId` innehåller den klient där användaren hittades. Klient-ID är gäst-klient, inte unikt klient för gästanvändare (Azure AD B2B-scenarier).
När token som tas emot för en användare, `AuthenticationResult` innehåller även information om den här användaren. För konfidentiell klient flöden där token begärs utan användare (för program), är den här användarinformationen null.
- Den `Scopes` för token har utfärdats.
- Unikt Id för användaren.

### <a name="iaccount"></a>IAccount

MSAL.NET definierar begreppet konto (via den `IAccount` interface). Den här stor förändring ger rätt semantik: det faktum att samma användare kan ha flera konton i olika Azure AD-kataloger. MSAL.NET innehåller också bättre information när det gäller gäst-scenarier som hemma kontoinformation tillhandahålls.
Följande diagram visar strukturen för de `IAccount` gränssnitt:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Den `AccountId` klass identifierar ett konto i en specifik klient. Den har följande egenskaper:

| Egenskap  | Beskrivning |
|----------|-------------|
| `TenantId` | En strängrepresentation för ett GUID som är ID för klienten där kontot finns. |
| `ObjectId` | En strängrepresentation för ett GUID som är ID för användaren som äger konto i klient. |
| `Identifier` | Unik identifierare för kontot. `Identifier` är en sammanfogning av `ObjectId` och `TenantId` avgränsade med ett kommatecken och är inte base64-kodad. |

Den `IAccount` gränssnittet representerar information om ett enda konto. Samma användare kan finnas i olika klienter, dvs, en användare kan ha flera konton. Medlemmar är:

| Egenskap  | Beskrivning |
|----------|-------------|
| `Username` | En sträng som innehåller några värdet i UserPrincipalName (UPN) format, till exempel john.doe@contoso.com. Den här strängen kan vara null, medan HomeAccountId och HomeAccountId.Identifier inte vara null. Den här egenskapen ersätter den `DisplayableId` egenskapen för `IUser` i tidigare versioner av MSAL.NET. |
| `Environment` | En sträng som innehåller identitetsleverantören för det här kontot, till exempel `login.microsoftonline.com`. Den här egenskapen ersätter den `IdentityProvider` egenskapen för `IUser`, förutom att `IdentityProvider` hade också information om klientorganisation (förutom molnmiljö), medan det här värdet är bara värden. |
| `HomeAccountId` | AccountId för home-konto för användaren. Den här egenskapen identifierar användaren i Azure AD-klienter. |

### <a name="using-the-token-to-call-a-protected-api"></a>Använda token för att anropa ett skyddat API

När den `AuthenticationResult` har returnerats av MSAL (i `result`), du behöver lägga till den i auktoriseringsrubriken HTTP innan du gör anropet för att få åtkomst till skyddade webb-API.

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```