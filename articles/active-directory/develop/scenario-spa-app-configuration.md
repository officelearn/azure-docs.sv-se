---
title: Program kod konfiguration med en sida (appens kod) – Microsoft Identity Platform
description: Lär dig hur du skapar ett program med en enda sida (appens kod konfiguration)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891535"
---
# <a name="single-page-application---code-configuration"></a>Program kod konfiguration med enkel sida

Lär dig hur du konfigurerar koden för en Enkels Ides applikation (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>MSAL-bibliotek som stöder implicit flöde

Microsoft Identity Platform tillhandahåller MSAL. js-bibliotek för att stödja det implicita flödet med hjälp av branschens rekommenderade säkra metoder.  

Bibliotek som stöder implicit flöde är:

| MSAL-bibliotek | Beskrivning |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Oformaterat JavaScript-bibliotek för användning i alla klient sidans webbappar som skapats med hjälp av Java Script-eller SPA-ramverk, till exempel vinkel, Vue. js, reagera. js osv. |
| ![MSAL-vinkel](media/sample-v2-code/logo_angular.png) <br/> [MSAL-vinkel](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Omslutning av kärnan MSAL. js-biblioteket för att förenkla användningen av appar med enkla sidor som skapats med vinkel ramverket. Det här biblioteket är i för hands version och har [kända problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) med vissa vinkel versioner och webbläsare. |

## <a name="application-code-configuration"></a>Program kod konfiguration

I MSAL-biblioteket skickas programmets registrerings information som konfiguration under biblioteks initieringen.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
Mer information om konfigurerbara alternativ finns i avsnittet om att [initiera program med MSAL. js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Logga in och logga ut](scenario-spa-sign-in.md)
