---
title: 'Skrivbordsappen att anrop web API: er (flytta till produktion) - Microsoft identity-plattformen'
description: 'Lär dig att skapa en skrivbordsapp att anrop webb-API: er (flytta till produktion)'
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
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ca66a41f26c54bf04273682d14889a36b688c70
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075137"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Skrivbordsappen som anropar webb-API: er – flytta till produktion

Den här artikeln innehåller information om att förbättra programmet ytterligare och flytta den till produktion.

## <a name="handling-errors-in-desktop-applications"></a>Hantera fel i program

I de olika flödena har du lärt dig hantera fel för tyst flöden (som visas i kodfragment). Du har också fått se att det finns fall där interaktion krävs (inkrementell medgivande och villkorlig åtkomst).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Hur du får användargodkännande förskott för flera resurser

> [!NOTE]
> Flera resurser fungerar för Microsoft identity-plattformen, men inte för Azure Active Directory (Azure AD) B2C ska få medgivande. Azure AD-B2C stöder endast administratörens godkännande, inte användarens medgivande.

Microsoft identity-plattformen (v2.0) endpoint låter dig inte att hämta en token för flera resurser på samma gång. Därför kan den `scopes` parametern får bara innehålla omfång för en enskild resurs. Du kan se till att användaren redan godkänner flera resurser med hjälp av den `extraScopesToConsent` parametern.

Till exempel om det finns två resurser scope som har två var:

- `https://mytenant.onmicrosoft.com/customerapi` -med 2 omfång `customer.read` och `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` -med 2 omfång `vendor.read` och `vendor.write`

Du bör använda den `.WithAdditionalPromptToConsent` modifieraren som har den `extraScopesToConsent` parametern.

Exempel:

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

Det här anropet får du en åtkomsttoken för den första webben-API.

När du vill anropa andra webb-API kan anropa du:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Personliga Microsoft-konto kräver reconsenting varje gång appen körs

För användare med Microsoft personliga konton är reprompting om medgivande på varje native client (desktop/mobilapp)-anrop för att auktorisera det avsedda funktionssättet. Interna klientens identitet är sin natur osäkert (sätt som strider mot konfidentiell klientprogrammet som exchange en hemlighet med Microsoft Identity-plattformen att bevisa sin identitet). Microsoft identity-plattformen har valt att minimera den här insecurity för tjänster genom att fråga användaren om tillstånd, varje gång programmet behörighet.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
