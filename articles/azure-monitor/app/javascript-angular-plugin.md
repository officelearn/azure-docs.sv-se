---
title: Vinkel-plugin-program för Application Insights JavaScript SDK
description: Hur du installerar och använder ett vinkel-plugin-program för Application Insights JavaScript SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91844032"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Vinkel-plugin-program för Application Insights JavaScript SDK

Vinkel-plugin-programmet för Application Insights JavaScript SDK, aktiverar:

- Spårning av router-ändringar
- Användnings statistik för vinkel komponenter

> [!WARNING]
> Vinkel-plugin-programmet är inte ECMAScript 3 (ES3)-kompatibelt.

## <a name="getting-started"></a>Komma igång

Installera NPM-paket:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Grundläggande användning

Konfigurera en instans av Application Insights i inmatnings komponenten i din app:

```js
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

Om du vill använda `trackMetric` metoden för att spåra användning av en vinkel komponent, Lägg till `AngularPluginService` som en provider i listan providers i `app.module.ts` filen.

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

Om du vill spåra livs längden för en komponent anropar `trackMetric` du `ngOnDestroy` metoden för den komponenten. När komponenten förstörs utlöses en `trackMetric` händelse som skickar den tid som användaren har kvar på sidan och komponent namnet.

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>Nästa steg

- Mer information om Java Script SDK finns i dokumentationen för [Application Insights JavaScript SDK](javascript.md)
- [Vinkel-plugin på GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)