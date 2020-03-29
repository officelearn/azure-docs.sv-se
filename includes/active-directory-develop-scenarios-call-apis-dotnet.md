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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76308947"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Egenskaper för autentiseringsresultat i MSAL.NET

Metoderna för att `AuthenticationResult`hämta token returnerar . För async-metoder returnerar. `Task<AuthenticationResult>`

I MSAL.NET exponerar `AuthenticationResult` du:

- `AccessToken`för att webb-API:et ska komma åt resurser. Den här parametern är en sträng, vanligtvis en Base-64-kodad JWT. Klienten bör aldrig titta inuti åtkomsttoken. Formatet är inte garanterat att förbli stabilt och kan krypteras för resursen. Att skriva kod som är beroende av åtkomsttokeninnehåll på klienten är en av de största felkällorna och klientlogikens avbrott. Mer information finns i [Access-token](../articles/active-directory/develop/access-tokens.md).
- `IdToken`för användaren. Den här parametern är en kodad JWT. Mer information finns i [ID-token](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn`anger datum och tid då token upphör att gälla.
- `TenantId`innehåller klienten där användaren hittades. För gästanvändare i Azure Active Directory (Azure AD) B2B-scenarier är klient-ID gästklienten, inte den unika klienten.
När token levereras för `AuthenticationResult` en användare innehåller den också information om den här användaren. För konfidentiella klientflöden där token begärs utan användare för programmet är den här användarinformationen null.
- För `Scopes` vilken token utfärdades.
- Det unika ID:t för användaren.

### <a name="iaccount"></a>IAccount (IAccount)

MSAL.NET definierar begreppet konto via `IAccount` gränssnittet. Denna bryta förändring ger rätt semantik. Samma användare kan ha flera konton i olika Azure AD-kataloger. Dessutom ger MSAL.NET bättre information när det gäller gästscenarier eftersom hemkontoinformation tillhandahålls.
Följande diagram visar `IAccount` gränssnittets struktur.

![Gränssnittsstruktur för IAccount](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Klassen `AccountId` identifierar ett konto i en viss klient med de egenskaper som visas i följande tabell.

| Egenskap | Beskrivning |
|----------|-------------|
| `TenantId` | En strängrepresentation för ett GUID, som är ID för den klient där kontot finns. |
| `ObjectId` | En strängrepresentation för ett GUID, som är ID för den användare som äger kontot i klienten. |
| `Identifier` | Unik identifierare för kontot. `Identifier`är sammanfogningen `ObjectId` av `TenantId` och åtskilda av ett kommatecken. De är inte Base 64 kodade. |

Gränssnittet `IAccount` representerar information om ett enda konto. Samma användare kan finnas i olika klienter, vilket innebär att en användare kan ha flera konton. Dess medlemmar visas i följande tabell.

| Egenskap | Beskrivning |
|----------|-------------|
| `Username` | En sträng som innehåller visningsvärdet i UPN-format (UserPrincipalName), till exempel john.doe@contoso.com. Den här strängen kan vara null, till skillnad från HomeAccountId och HomeAccountId.Identifier, som inte är null. Den här egenskapen ersätter egenskapen `DisplayableId` `IUser` för i tidigare versioner av MSAL.NET. |
| `Environment` | En sträng som innehåller identitetsprovidern för `login.microsoftonline.com`det här kontot, till exempel . Den här egenskapen `IUser`ersätter `IdentityProvider` egenskapen `IdentityProvider` för , förutom att även ha information om klienten, förutom molnmiljön. Här är värdet bara värden. |
| `HomeAccountId` | Konto-ID för hemkontot för användaren. Den här egenskapen identifierar användaren unikt för Azure AD-klienter. |

### <a name="use-the-token-to-call-a-protected-api"></a>Använd token för att anropa ett skyddat API

När `AuthenticationResult` msal har returnerats i lägger du till det i `result`HTTP-auktoriseringshuvudet innan du ringer anropet för att komma åt det skyddade webb-API:et.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```