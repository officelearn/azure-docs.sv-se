---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67712547"
---
Med `ApplicationInsights` inställningen kan du lägga till [azure application insights-telemetristöd](https://docs.microsoft.com/azure/application-insights) i din behållare. Application Insights ger djupgående övervakning av din behållare. Du kan enkelt övervaka din behållare för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i behållaren.

I följande tabell beskrivs de `ApplicationInsights` konfigurationsinställningar som stöds under avsnittet.

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Inga| `InstrumentationKey` | String | Instrumenteringsnyckeln för application insights-instansen som telemetridata för behållaren skickas till. Mer information finns i [Application Insights för ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exempel:<br>`InstrumentationKey=123456789`|

