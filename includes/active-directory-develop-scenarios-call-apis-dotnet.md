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
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76308947"
---
### <a name="authenticationresult-properties-in-msalnet"></a>AuthenticationResult-egenskaper i MSAL.NET

Metoderna för att hämta tokens returnerar `AuthenticationResult`. `Task<AuthenticationResult>` returnerar för asynkrona metoder.

I MSAL.NET exponeras `AuthenticationResult`:

- `AccessToken` för webb-API: et för att få åtkomst till resurser. Den här parametern är en sträng, vanligt vis en Base-64-kodad JWT. Klienten bör aldrig se ut i åtkomsttoken. Formatet är inte garanterat vara stabilt och kan krypteras för resursen. Att skriva kod som är beroende av innehåll i åtkomsttoken på klienten är en av de största källorna till fel och klient logiks brytningar. Mer information finns i åtkomsttoken [.](../articles/active-directory/develop/access-tokens.md)
- `IdToken` för användaren. Den här parametern är en kodad JWT. Mer information finns i [ID-token](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn` anger det datum och tidpunkt då token upphör att gälla.
- `TenantId` innehåller innehavaren där användaren hittades. För gäst användare i Azure Active Directory (Azure AD) B2B-scenarier är klient-ID: t gäst klienten, inte den unika klient organisationen.
När token levereras för en användare, innehåller `AuthenticationResult` också information om den här användaren. För konfidentiella klient flöden där tokens begärs utan användare för programmet, är denna användar information null.
- `Scopes` som token utfärdades för.
- Unikt ID för användaren.

### <a name="iaccount"></a>IAccount

MSAL.NET definierar begreppet för ett konto genom `IAccount` gränssnittet. Den här avbrytande ändringen ger rätt semantik. Samma användare kan ha flera konton i olika Azure AD-kataloger. Dessutom ger MSAL.NET bättre information när det gäller gäst scenarier eftersom information om hem kontot tillhandahålls.
Följande diagram visar strukturen för `IAccount` gränssnittet.

![IAccount-gränssnitts struktur](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Klassen `AccountId` identifierar ett konto i en speciell klient med de egenskaper som visas i följande tabell.

| Egenskap | Beskrivning |
|----------|-------------|
| `TenantId` | En sträng representation för ett GUID, som är ID: t för den klient där kontot finns. |
| `ObjectId` | En sträng representation för ett GUID, vilket är ID: t för den användare som äger kontot i klient organisationen. |
| `Identifier` | Unikt ID för kontot. `Identifier` är sammanfogningen av `ObjectId` och `TenantId` avgränsat med kommatecken. De är inte grundläggande 64-kodade. |

`IAccount`-gränssnittet representerar information om ett enda konto. Samma användare kan finnas i olika klienter, vilket innebär att en användare kan ha flera konton. Dess medlemmar visas i följande tabell.

| Egenskap | Beskrivning |
|----------|-------------|
| `Username` | En sträng som innehåller det visnings bara värdet i UserPrincipalName-format (UPN), till exempel john.doe@contoso.com. Den här strängen kan vara null, till skillnad från HomeAccountId och HomeAccountId. Identifier, som inte är null. Den här egenskapen ersätter egenskapen `DisplayableId` för `IUser` i tidigare versioner av MSAL.NET. |
| `Environment` | En sträng som innehåller identitets leverantören för det här kontot, till exempel `login.microsoftonline.com`. Den här egenskapen ersätter `IdentityProvider` egenskapen för `IUser`, förutom att `IdentityProvider` även hade information om klienten, förutom moln miljön. Här är värdet bara värden. |
| `HomeAccountId` | Konto-ID för användarens hem konto. Den här egenskapen identifierar en unik användare i Azure AD-klienter. |

### <a name="use-the-token-to-call-a-protected-api"></a>Använda token för att anropa ett skyddat API

När `AuthenticationResult` har returnerats av MSAL i `result`lägger du till det i HTTP Authorization-huvudet innan du får åtkomst till det skyddade webb-API: et.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```