---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 3986b77cfad167134bf8ada1e3cbb73ad64dd3ca
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572772"
---
Samling med anpassade mått. Använd den här samlingen i rapport med namnet mätning som är associerade med objektet telemetri. Vanliga användningsområden är:
- storleken på Beroendetelemetri nyttolast
- antalet köobjekt som bearbetas av begär telemetri
- tid som kunden tog för att slutföra steg i steget Slutför i guiden Händelsetelemetri.

Du kan fråga [anpassade mått](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) i Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Anpassade mått är associerade med telemetriobjekt som de tillhör. De är föremål för sampling med telemetriobjekt som innehåller dessa mätningar. Om du vill spåra ett mått som har ett värde som är oberoende av andra typer av telemetri, använda [mått telemetri](../articles/application-insights/app-insights-api-custom-events-metrics.md).

Maximal nyckellängd: 150
