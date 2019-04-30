---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900534"
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
 > Anpassade mått är associerade med telemetriobjekt som de tillhör. De är föremål för sampling med telemetriobjekt som innehåller dessa mätningar. Om du vill spåra ett mått som har ett värde som är oberoende av andra typer av telemetri, använda [mått telemetri](../articles/azure-monitor/app/api-custom-events-metrics.md).

Maximal nyckellängd: 150
