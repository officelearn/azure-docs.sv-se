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
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423770"
---
### <a name="authenticationresult-properties-in-msalnet"></a>AuthenticationResult-egenskaper i MSAL.NET

Metoderna för att hämta tokens returnerar ett `AuthenticationResult` (eller, för asynkrona metoder, en `Task<AuthenticationResult>`.

I MSAL.NET exponeras `AuthenticationResult`:

- `AccessToken` för webb-API: et för att få åtkomst till resurser. Den här parametern är en sträng, vanligt vis en Base64-kodad JWT men klienten bör aldrig titta inuti åtkomsttoken. Formatet är inte garanterat vara stabilt och kan krypteras för resursen. Användare som skriver kod beroende på åtkomsttoken på klienten är en av de största källorna till fel och klient logiks avbrott. Se även [åtkomst-token](../articles/active-directory/develop/access-tokens.md)
- `IdToken` för användaren (den här parametern är en kodad JWT). Se [ID-token](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` anger det datum/tid när token upphör att gälla
- `TenantId` innehåller innehavaren där användaren hittades. För gäst användare (Azure AD B2B-scenarier) är klient-ID: t gäst klienten, inte den unika klient organisationen.
När token levereras för en användare, innehåller `AuthenticationResult` också information om den här användaren. För konfidentiella klient flöden där tokens begärs utan användare (för programmet) är denna användar information null.
- `Scopes` som token utfärdades för.
- Unikt ID för användaren.

### <a name="iaccount"></a>IAccount

MSAL.NET definierar konto begreppet (via `IAccount`-gränssnittet). Den här avbrytande ändringen ger rätt semantik: det faktum att samma användare kan ha flera konton i olika Azure AD-kataloger. MSAL.NET tillhandahåller också bättre information när det gäller gäst scenarier, eftersom information om hem kontot tillhandahålls.
Följande diagram visar strukturen för `IAccount` gränssnittet:

![mallar](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Klassen `AccountId` identifierar ett konto i en speciell klient. Den har följande egenskaper:

| Egenskap | Beskrivning |
|----------|-------------|
| `TenantId` | En sträng representation för ett GUID, som är ID: t för den klient där kontot finns. |
| `ObjectId` | En sträng representation för ett GUID, vilket är ID: t för den användare som äger kontot i klient organisationen. |
| `Identifier` | Unikt ID för kontot. `Identifier` är en sammanfogning av `ObjectId` och `TenantId` avgränsat med kommatecken och är inte Base64-kodat. |

`IAccount`-gränssnittet representerar information om ett enda konto. Samma användare kan finnas i olika klienter, vilket innebär att en användare kan ha flera konton. Dess medlemmar är:

| Egenskap | Beskrivning |
|----------|-------------|
| `Username` | En sträng som innehåller det visnings bara värdet i UserPrincipalName-format (UPN), till exempel john.doe@contoso.com. Den här strängen kan vara null, medan HomeAccountId och HomeAccountId. Identifier inte får vara null. Den här egenskapen ersätter egenskapen `DisplayableId` för `IUser` i tidigare versioner av MSAL.NET. |
| `Environment` | En sträng som innehåller identitets leverantören för det här kontot, till exempel `login.microsoftonline.com`. Den här egenskapen ersätter egenskapen `IdentityProvider` för `IUser`, förutom att `IdentityProvider` också hade information om klienten (förutom moln miljön), och här är värdet bara värden. |
| `HomeAccountId` | AccountId för användarens hem konto. Den här egenskapen identifierar en unik användare i Azure AD-klienter. |

### <a name="using-the-token-to-call-a-protected-api"></a>Använda token för att anropa ett skyddat API

När `AuthenticationResult` har returnerats av MSAL (i `result`) måste du lägga till den i HTTP Authorization-huvudet innan du kan ringa till åtkomst till det skyddade webb-API: et.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```