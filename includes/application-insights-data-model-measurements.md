---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187101"
---
Insamling av anpassade mått. Använd den här samlingen om du vill rapportera med namnet mått som är associerat med telemetriobjektet. Typiska användningsfall är:
- storleken på beroendet telemetri nyttolast
- antalet köobjekt som bearbetas av Begäran telemetri
- den tid som kunden tog för att slutföra steget i guiden steg slutförande Händelse telemetri.

Du kan fråga [anpassade mått](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) i Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Anpassade mått associeras med telemetriobjektet som de tillhör. De är föremål för provtagning med telemetriposten som innehåller dessa mätningar. Om du vill spåra ett mått som har ett värde som är oberoende av andra telemetrityper använder du [Måtttelemetri](../articles/azure-monitor/app/api-custom-events-metrics.md).

Max nyckellängd: 150
