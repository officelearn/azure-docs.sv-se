---
title: Flytta webb-API:er för att anropa webb-API:er för stationära datorer – Microsoft identity platform | Azure
description: Lär dig hur du flyttar en skrivbordsapp som anropar webb-API:er till produktion
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea564eb69f102d8e548bf8ae9a626598fa264cd4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882887"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Skrivbordsapp som anropar webb-API:er: Flytta till produktion

I den här artikeln får du lära dig hur du flyttar din skrivbordsapp som anropar webb-API:er till produktion.

## <a name="handle-errors-in-desktop-applications"></a>Hantera fel i skrivbordsprogram

I de olika flödena har du lärt dig hur du hanterar felen för de tysta flödena, som visas i kodavsnitten. Du har också sett att det finns fall där interaktion behövs, som i inkrementellt medgivande och villkorlig åtkomst.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Har användaren samtycke i förväg för flera resurser

> [!NOTE]
> Att få medgivande för flera resurser fungerar för Microsoft identity-plattformen men inte för Azure Active Directory (Azure AD) B2C. Azure AD B2C stöder endast administratörssamtycke, inte användarens medgivande.

Du kan inte hämta en token för flera resurser samtidigt med slutpunkten för Microsoft identity platform (v2.0). Parametern `scopes` kan innehålla scope för endast en enskild resurs. Du kan se till att användaren i förväg `extraScopesToConsent` godkänner flera resurser med hjälp av parametern.

Du kan till exempel ha två resurser som har två scope vardera:

- `https://mytenant.onmicrosoft.com/customerapi`med omfattningar `customer.read` och`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`med omfattningar `vendor.read` och`vendor.write`

I det här `.WithAdditionalPromptToConsent` exemplet använder du `extraScopesToConsent` den modifierare som har parametern.

Till exempel:

### <a name="in-msalnet"></a>I MSAL.NET

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

### <a name="in-msal-for-ios-and-macos"></a>I MSAL för iOS och macOS

Mål C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Det här anropet ger dig en åtkomsttoken för det första webb-API:et.

Anropa API:et när du behöver `AcquireTokenSilent` anropa det andra webb-API:et.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Microsofts personliga konto kräver att appen samlas igen varje gång appen körs

För Microsofts användare av personliga konton är det avsedda beteendet att vidarebefordra samtycke på varje inbyggt klientsamtal (skrivbord eller mobilapp) för att auktorisera. Native klient identitet är i sig osäker, vilket strider mot konfidentiell klient programidentitet. Konfidentiella klientprogram utbyter en hemlighet med Microsoft Identity-plattformen för att bevisa sin identitet. Microsofts identitetsplattform valde att minska detta otrygghet för konsumenttjänster genom att uppmana användaren att ge sitt samtycke varje gång programmet godkänns.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
