---
title: Konfigurera ensidig app - Microsoft identity platform | Azure
description: Lär dig hur du skapar ett ensidigt program (appens kodkonfiguration)
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
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: c1799b25ec1adf44342d2305d3b2a29039c39cd4
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419706"
---
# <a name="single-page-application-code-configuration"></a>Ensidigt program: Kodkonfiguration

Lär dig hur du konfigurerar koden för ditt ensidiga program (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>MSAL-bibliotek som stöder implicit flöde

Microsoft-identitetsplattformen tillhandahåller följande MSAL-bibliotek (Microsoft Authentication Library) som stöder implicit flöde med hjälp av branschberekommendationerade säkerhetsrutiner:

| MSAL-bibliotek | Beskrivning |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Vanligt JavaScript-bibliotek för användning i alla webbappar på klientsidan som är inbyggda via JavaScript- eller SPA-ramverk som Angular, Vue.js och React.js. |
| ![MSAL Vinkel](media/sample-v2-code/logo_angular.png) <br/> [MSAL Vinkel](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Omslag till msal.js-biblioteket för att förenkla användningen i ensidiga appar som är byggda via vinkelramverket. |

## <a name="application-code-configuration"></a>Konfiguration av programkod

I ett MSAL-bibliotek skickas programregistreringsinformationen som konfiguration under bibliotekets initiering.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Mer information om de konfigurerbara alternativen finns i [Initiera program med MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Inloggning och utloggning](scenario-spa-sign-in.md)
