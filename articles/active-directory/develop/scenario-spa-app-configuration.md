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
ms.openlocfilehash: 30a9b710ffbf98ebc523217a3b8a7fd9a2640c49
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443049"
---
# <a name="single-page-application-code-configuration"></a>Program med en sida: kod konfiguration

Lär dig hur du konfigurerar koden för en Enkels Ides applikation (SPA).

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>MSAL-bibliotek för SPAs och stödda autentiserings flöden

Microsoft Identity Platform tillhandahåller följande Microsoft-autentiseringspaket för Java Script (MSAL.js) som stöd för implicit flödes-och auktoriseringskod med PKCE med hjälp av bransch rekommenderade säkerhets metoder:

| MSAL-bibliotek | Flöden | Beskrivning |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | Authorization Code Flow (PKCE) | Oformaterat JavaScript-bibliotek för användning i alla webb program på klient sidan som bygger på JavaScript-eller SPA-ramverk, till exempel vinkel, Vue.js och React.js. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | Implicit flöde | Oformaterat JavaScript-bibliotek för användning i alla webb program på klient sidan som bygger på JavaScript-eller SPA-ramverk, till exempel vinkel, Vue.js och React.js. |
| ![MSAL-vinkel](media/sample-v2-code/logo_angular.png) <br/> [MSAL-vinkel](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Implicit flöde | Omslutningen av kärn MSAL.jss biblioteket för att förenkla användningen av appar på en sida som är byggda genom ram-ramverket. |

## <a name="application-code-configuration"></a>Program kod konfiguration

I ett MSAL-bibliotek skickas programmets registrerings information som konfiguration under biblioteks initieringen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Mer information om konfigurerbara alternativ finns i avsnittet [om att initiera program med MSAL.js](msal-js-initializing-client-applications.md).

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

Gå vidare till nästa artikel i det här scenariot, [Logga in och logga ut](scenario-spa-sign-in.md).
