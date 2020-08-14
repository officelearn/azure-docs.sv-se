---
title: Reagera på plugin-programmet för Application Insights JavaScript SDK
description: Installera och använda reagera-plugin-program för Application Insights JavaScript SDK.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 677810c21f9ea6151e2ffe7a4e2b9cdc8473a09f
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227427"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Reagera på plugin-programmet för Application Insights JavaScript SDK

Reagera på plugin-programmet för Application Insights JavaScript SDK, aktiverar:

- Spårning av väg ändringar
- Användnings statistik för reagerar på komponenter

## <a name="getting-started"></a>Komma igång

Installera NPM-paket:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Grundläggande användning

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>Konfiguration

| Namn    | Standardvärde | Beskrivning                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| historik | null    | Reagerar på routerkonfiguration. Mer information finns i [paket dokumentationen för reakta router](https://reactrouter.com/web/api/history). Information om hur du kommer åt historik-objektet utanför komponenterna finns i [vanliga frågor och svar om att reagera på routern](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Reagera på användnings spårning för komponenter

Om du vill Instrumenta olika användnings spårning för reagerande komponenter använder du `withAITracking` komponenten med högre ordning.

Det kommer att mäta tiden från `ComponentDidMount` händelsen genom `ComponentWillUnmount` evenemanget. För att göra detta mer korrekt kommer det dock att ta bort tiden då användaren var inaktiv `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Om du vill se det här måttet i Azure Portal måste du gå till Application Insights resurs, välja fliken "mått" och konfigurera de tomma diagrammen för att visa det anpassade mått namnet "" reagera komponent aktive tid (sekunder) ", Välj agg regering (sum, AVG osv.) för ditt mått och tillämpa Split är" komponent namn ".

![Skärm bild av diagram som visar det anpassade måttet "" reagera komponent aktive tid (sekunder) "dela efter" komponent namn "](./media/javascript-react-plugin/chart.png)

Du kan också köra anpassade frågor för att dela upp Application Insights data för att generera rapporter och visualiseringar enligt dina krav. I Azure Portal navigerar du till Application Insights resurs väljer du Analytics på den översta menyn på fliken Översikt och kör frågan.

![Skärm bild av anpassade metriska frågeresultat.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Det kan ta upp till 10 minuter innan nya anpassade mått visas i Azure-portalen.

## <a name="sample-app"></a>Exempelapp

Kolla in [Application Insights reagera på demo](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Nästa steg

- Läs mer om Java Script SDK i [Application Insights JavaScript SDK-dokumentationen](javascript.md).
- Information om Kusto-frågespråket och frågor om data i Log Analytics finns i [Översikt över logg frågor](../../azure-monitor/log-query/log-query-overview.md).
