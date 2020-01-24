---
title: Få medgivande för flera resurser (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur en användare kan få för hands godkännande för flera resurser med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3bd23a1f14d5e3cbf9fc41ade47571c6689f3468
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695032"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Användaren får ett medgivande för flera resurser med MSAL.NET
Med Microsoft Identity Platform-slutpunkten kan du inte hämta en token för flera resurser på en gång. När du använder Microsoft Authentication Library för .NET (MSAL.NET) ska omfattnings parametern i metoden för att hämta token bara innehålla omfång för en enskild resurs. Du kan dock förkonfigurera för flera resurser genom att ange ytterligare omfång med hjälp av metoden `.WithExtraScopeToConsent` Builder.

> [!NOTE]
> Att få ett medgivande för flera resurser fungerar för Microsoft Identity Platform, men inte för Azure AD B2C. Azure AD B2C stöder endast administrativt godkännande, inte användar medgivande.

Om du till exempel har två resurser som har två omfång:

- https:\//mytenant.onmicrosoft.com/customerapi (med 2 omfång `customer.read` och `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (med 2 omfång `vendor.read` och `vendor.write`)

Du bör använda `.WithExtraScopeToConsent` modifierare som har parametern *extraScopesToConsent* som visas i följande exempel:

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

Då får du en åtkomsttoken för det första webb-API: et. När du behöver komma åt det andra webb-API: et kan du hämta token från token cache:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
