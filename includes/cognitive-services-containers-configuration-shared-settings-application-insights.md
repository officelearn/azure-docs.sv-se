---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "67712547"
---
Med den `ApplicationInsights` här inställningen kan du lägga till stöd för [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) -telemetri till din behållare. Application Insights ger detaljerad övervakning av din behållare. Du kan enkelt övervaka din behållare för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i din behållare.

I följande tabell beskrivs de konfigurations inställningar som stöds i `ApplicationInsights` avsnittet.

|Obligatorisk| Name | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Nej| `InstrumentationKey` | Sträng | Instrumentation-nyckeln för Application Insights-instansen som telemetridata för behållaren skickas till. Mer information finns i [Application Insights för ASP.net Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exempel:<br>`InstrumentationKey=123456789`|

