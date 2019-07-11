---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712547"
---
Den `ApplicationInsights` inställningen kan du lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren. Application Insights erbjuder djupgående övervakning av din behållare. Du kan enkelt övervaka din behållare för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i din behållare.

I följande tabell beskrivs de konfigurationsinställningar som stöds den `ApplicationInsights` avsnittet.

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Nej| `InstrumentationKey` | Sträng | Instrumenteringsnyckeln för Application Insights-instans till vilken telemetri skickas data för behållaren. Mer information finns i [Application Insights för ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exempel:<br>`InstrumentationKey=123456789`|

