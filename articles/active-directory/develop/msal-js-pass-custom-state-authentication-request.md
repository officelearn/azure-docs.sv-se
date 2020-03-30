---
title: Skicka anpassat tillstånd i autentiseringsbegäranden (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skickar ett anpassat tillståndsparametervärde i autentiseringsbegäran med hjälp av Microsoft Authentication Library for JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084946"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Skicka anpassat tillstånd i autentiseringsbegäranden med MSAL.js

*Tillståndsparametern,* som definieras av OAuth 2.0, ingår i en autentiseringsbegäran och returneras också i tokensvaret för att förhindra förfalskningsattacker mellan webbplatser. Som standard skickar Microsoft Authentication Library for JavaScript (MSAL.js) ett slumpmässigt genererat unikt tillståndsparametervärde i autentiseringsbegärandena. *state*

Tillståndsparametern kan också användas för att koda information om appens tillstånd före omdirigering. Du kan skicka användarens tillstånd i appen, till exempel sidan eller vyn som de var på, som indata till den här parametern. Med MSAL.js-biblioteket kan du skicka ditt anpassade `Request` tillstånd som tillståndsparameter i objektet:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> Om du vill hoppa över en cachelagd token och gå `forceRefresh` till servern, vänligen skicka in boolean till AuthenticationParameters-objektet som används för att göra en inloggnings-/tokenbegäran.
> `forceRefresh`bör inte användas som standard på grund av prestandapåverkan på ditt program.
> Att förlita sig på cachen ger användarna en bättre upplevelse.
> Hoppa över cacheminnet bör endast användas i scenarier där du vet att de för närvarande cachelagrade data inte har aktuell information.
> Till exempel ett admin-verktyg som lägger till roller till en användare som behöver skaffa en ny token med uppdaterade roller.

Ett exempel:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Det skickade tillståndet läggs till i den unika GUID som msal.js har angett när begäran skickas. När svaret returneras söker MSAL.js efter en tillståndsmatchning och returnerar sedan det anpassade skickade tillståndet i `Response` objektet som `accountState`.

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Mer information finns i om du [vill att du ska skapa ett spa med](scenario-spa-overview.md) HJÄLP av MSAL.js.