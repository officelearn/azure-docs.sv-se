---
title: 'Skriv bords app som anropar webb-API: er (flytta till produktion) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en stationär app som anropar webb-API: er (flytta till produktion)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5331f01c5dc6acf01f567dbe4c332853bf7aa47e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175546"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Skriv bords app som anropar webb-API: er – flytta till produktion

Den här artikeln innehåller information om hur du förbättrar programmet ytterligare och flyttar det till produktion.

## <a name="handling-errors-in-desktop-applications"></a>Hantera fel i Skriv bords program

I de olika flödena har du lärt dig hur du hanterar felen för de tysta flödena (som visas i kodfragment). Du har också sett att det finns fall där interaktion krävs (stegvist medgivande och villkorlig åtkomst).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Så här får du användar medgivande för flera resurser

> [!NOTE]
> Att få ett medgivande för flera resurser fungerar för Microsoft Identity Platform, men inte för Azure Active Directory (Azure AD) B2C. Azure AD B2C stöder endast administrativt godkännande, inte användar medgivande.

Slut punkten för Microsoft Identity Platform (v 2.0) låter dig inte hämta en token för flera resurser på en gång. Därför kan `scopes`-parametern bara innehålla omfång för en enskild resurs. Du kan se till att användaren har samtyckt till flera resurser genom att använda `extraScopesToConsent`-parametern.

Om du till exempel har två resurser som har två omfång:

- `https://mytenant.onmicrosoft.com/customerapi`-med 2 omfattningar `customer.read` och `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`-med 2 omfattningar `vendor.read` och `vendor.write`

Du bör använda `.WithAdditionalPromptToConsent` modifierare som har parametern `extraScopesToConsent`.

Exempel:

### <a name="in-msalnet"></a>I MSAL.NET

```CSharp
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

Mål-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Införliva

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Det här anropet får du en åtkomsttoken för det första webb-API: et.

När du behöver anropa det andra webb-API: et kan du anropa `AcquireTokenSilent` API:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Microsoft personal-kontot kräver att du godkänner varje tillfälle som appen körs

För användare av Microsoft personal, uppmanas du att svara på varje intern klient (stationär dator/mobilapp) anrop till auktorisera är det avsedda beteendet. Den interna klient identiteten är osäker (till skillnad från konfidentiellt klient program som utbyter en hemlighet med Microsoft Identity Platform för att bevisa sin identitet). Microsoft Identity Platform valde att minimera den här insäkerheten för konsument tjänster genom att meddela användaren om godkännande, varje gången programmet auktoriseras.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
