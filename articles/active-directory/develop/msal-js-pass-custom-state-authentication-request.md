---
title: Skicka anpassade tillstånd i autentiseringsbegäranden (Microsoft Authentication Library för JavaScript) | Azure
description: Lär dig hur du skickar ett värde för parametern av anpassade tillstånd i autentiseringsbegäran med hjälp av Microsoft Authentication Library för JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420505"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Skicka anpassade tillstånd i begäranden om autentisering med hjälp av MSAL.js
Den *tillstånd* parametern, som definieras av OAuth 2.0 ingår i en autentiseringsbegäran och returneras också token svar att förhindra attacker med förfalskning av begäran. Som standard Microsoft Authentication Library för JavaScript (MSAL.js) skickar en slumpmässigt genererad unika *tillstånd* parametervärdet i autentiseringsbegäranden.

Parametern state kan också användas för att koda information av appens tillstånd innan omdirigering. Du kan skicka användarens status i appen, till exempel den sidan eller visa de befann sig i, som indata för den här parametern. MSAL.js biblioteket kan du skicka dina anpassade tillstånd som parametern state i den `Request` objekt:

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
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

Det överförda tillståndet läggs till den unika GUID som angetts av MSAL.js när förfrågan skulle skickas. När svaret returneras MSAL.js söker efter en matchning för tillstånd och returnerar sedan den anpassade som skickades i tillstånd i den `Response` objekt som `accountState`.

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

Om du vill veta mer kan du läsa om [att skapa en ensidesapplikation (SPA)](scenario-spa-overview.md) med MSAL.js.