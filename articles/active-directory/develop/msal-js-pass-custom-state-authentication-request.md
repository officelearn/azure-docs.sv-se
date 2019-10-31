---
title: Skicka anpassat tillstånd i autentiseringsbegäranden (Microsoft Authentication Library för Java Script)
titleSuffix: Microsoft identity platform
description: Lär dig hur du skickar ett anpassat tillstånds parameter värde i autentiseringsbegäran med Microsoft Authentication Library för Java Script (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 527c1937da8634f7448c82ca8c0331fb5cfb85e3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150616"
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
};
```

Exempel:

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