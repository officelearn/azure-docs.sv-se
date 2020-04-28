---
title: Konfigurera en app med en enda sida – Microsoft Identity Platform | Azure
description: Lär dig hur du skapar ett program med en enda sida (appens kod konfiguration)
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882343"
---
# <a name="single-page-application-code-configuration"></a>Program med en sida: kod konfiguration

Lär dig hur du konfigurerar koden för en Enkels Ides applikation (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>MSAL-bibliotek som stöder implicit flöde

Microsoft Identity Platform tillhandahåller följande bibliotek för Microsoft Authentication Library (MSAL) för att stödja implicit flöde med hjälp av bransch rekommenderade säkerhets metoder:

| MSAL-bibliotek | Beskrivning |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Oformaterat JavaScript-bibliotek för användning i alla webb program på klient sidan som bygger på JavaScript-eller SPA-ramverk, till exempel vinkel, Vue. js och reagerar. js. |
| ![MSAL-vinkel](media/sample-v2-code/logo_angular.png) <br/> [MSAL-vinkel](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Omslutning av kärnan MSAL. js-biblioteket för att förenkla användningen av appar på en sida som är byggda genom ram-ramverket. |

## <a name="application-code-configuration"></a>Program kod konfiguration

I ett MSAL-bibliotek skickas programmets registrerings information som konfiguration under biblioteks initieringen.

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

Mer information om konfigurerbara alternativ finns i avsnittet [om att initiera program med MSAL. js](msal-js-initializing-client-applications.md).

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
