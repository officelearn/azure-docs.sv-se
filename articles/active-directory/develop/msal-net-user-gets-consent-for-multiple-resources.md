---
title: Få samtycke för flera resurser (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur en användare kan få förhandsgodkännande för flera resurser med hjälp av Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 94c9a2b6a46262ad293da9ca3ba493d6f898c870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085835"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Användaren får samtycke till flera resurser som använder MSAL.NET
Slutpunkten för Microsoft-identitetsplattformen tillåter inte att du får en token för flera resurser samtidigt. När du använder Microsoft Authentication Library for .NET (MSAL.NET) ska parametern scopes i metoden acquire token endast innehålla scope för en enskild resurs. Du kan dock godkänna flera resurser i förväg genom att `.WithExtraScopeToConsent` ange ytterligare scope med hjälp av byggaremetoden.

> [!NOTE]
> Att få medgivande för flera resurser fungerar för Microsoft identity-plattformen, men inte för Azure AD B2C. Azure AD B2C stöder endast administratörssamtycke, inte användarens medgivande.

Om du till exempel har två resurser som har två scope vardera:

- https:\//mytenant.onmicrosoft.com/customerapi (med 2 `customer.read` scope `customer.write`och )
- https:\//mytenant.onmicrosoft.com/vendorapi (med 2 `vendor.read` scope `vendor.write`och )

Du bör `.WithExtraScopeToConsent` använda modifieraren som har parametern *extraScopesToConsent* som visas i följande exempel:

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

Detta ger dig en åtkomsttoken för det första webb-API:et. När du sedan behöver komma åt det andra webb-API:et kan du tyst hämta token från tokencachen:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
