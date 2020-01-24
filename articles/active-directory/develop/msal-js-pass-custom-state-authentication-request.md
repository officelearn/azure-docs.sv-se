---
title: Skicka anpassat tillstånd i autentiseringsbegäranden (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skickar ett anpassat tillstånds parameter värde i autentiseringsbegäran med Microsoft Authentication Library för Java Script (MSAL. js).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: be2dd887358aa00c87a4b5668a99c425d83b59dc
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696035"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Skicka anpassat tillstånd i autentiseringsbegäranden med MSAL. js

Parametern *State* , som definieras av OAuth 2,0, ingår i en autentiseringsbegäran och returneras också i svaret från token för att förhindra förfalskning av begäran mellan webbplatser. Som standard skickar Microsoft Authentication Library för Java Script (MSAL. js) ett slumpmässigt genererat unikt *tillstånds* parameter värde i autentiseringsbegäranden.

Tillstånds parametern kan också användas för att koda information om appens tillstånd före omdirigeringen. Du kan skicka användarens tillstånd i appen, t. ex. sidan eller vyn som de var på, som indata för den här parametern. Med MSAL. js-biblioteket kan du skicka anpassad status som en tillstånds parameter i `Request`-objektet:

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
> Om du vill hoppa över en cachelagrad token och gå till servern, måste du skicka det booleska `forceRefresh` till AuthenticationParameters-objektet som används för att göra en begäran om inloggning/token.
> `forceRefresh` ska inte användas som standard på grund av prestanda påverkan på ditt program.
> Om du förlitar dig på cacheminnet får användarna en bättre upplevelse.
> Att hoppa över cachen bör endast användas i scenarier där du vet att cachelagrade data inte innehåller uppdaterad information.
> Till exempel ett administrations verktyg som lägger till roller till en användare som behöver hämta en ny token med uppdaterade roller.

Ett exempel:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Det överförda läget läggs till i det unika GUID som anges av MSAL. js när begäran skickas. När svaret returneras söker MSAL. js efter en tillstånds matchning och returnerar sedan det anpassade tillstånd som skickats i `Response`-objektet som `accountState`.

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

Läs mer om att [skapa ett program med en sida (Spa)](scenario-spa-overview.md) med hjälp av MSAL. js.