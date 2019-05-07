---
title: Hämta medgivande för flera resurser (Microsoft Authentication Library för .NET) | Azure
description: Lär dig hur en användare kan få före medgivande för flera resurser med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45bad9a03e8eff6f22ebb99fd2ef4bcd5fecf9b5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158827"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Användaren får medgivande för flera resurser med hjälp av MSAL.NET
Microsoft identity-plattformen slutpunkten kan du inte att hämta en token för flera resurser på samma gång. När du använder Microsoft Authentication Library för .NET (MSAL.NET), får scope-parametern i metoden hämta token endast innehålla omfång för en enskild resurs. Dock kan förväg samtycker du till förskott flera resurser genom att ange ytterligare scope med hjälp av den `.WithExtraScopeToConsent` builder-metoden.

> [!NOTE]
> Få godkännande flera resurser fungerar för Microsoft identity-plattformen, men inte för Azure AD B2C. Azure AD-B2C stöder endast administratörens godkännande, inte användarens medgivande.

Till exempel om du har två resurser som har definierar 2 var och en:

- https://mytenant.onmicrosoft.com/customerapi (med 2 omfång `customer.read` och `customer.write`)
- https://mytenant.onmicrosoft.com/vendorapi (med 2 omfång `vendor.read` och `vendor.write`)

Du bör använda den `.WithExtraScopeToConsent` modifieraren som har den *extraScopesToConsent* parameter som visas i följande exempel:

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

Detta får du en åtkomsttoken för den första webben-API. När du behöver komma åt andra webb-API kan du sedan tyst hämta token från token-cache:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
