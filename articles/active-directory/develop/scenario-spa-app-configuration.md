---
title: Konfigurera ensidig app - Microsoft identity platform | Azure
description: Lär dig hur du skapar ett ensidigt program (appens kodkonfiguration)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f159105046231ba5fb4e458cdd70d930a411a920
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882343"
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
