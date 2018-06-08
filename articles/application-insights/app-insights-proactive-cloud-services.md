---
title: Avisering om problem i Azure Cloud Services med Azure-diagnostik-integrering med Azure Application Insights | Microsoft Docs
description: Övervakare för problem som startfel, krascher och roll Återanvänd loopar i Azure Cloud Services med Programinsikter för Azure
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: harelbr; mbullwin
ms.openlocfilehash: 18817fd84a86a72d379f96973b71658f2cdf4afd
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851321"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Avisera om problem i Azure Cloud Services med Azure diagnostics-integrering med Azure Application Insights

I den här artikeln beskriver vi hur du ställer in Varningsregler som övervakar för problem som startfel, krascher och roll Återanvänd loopar i Azure-molntjänster (webb- och arbetsroller roller).

Den metod som beskrivs i den här artikeln är baserad på den [Azure Diagnostics integrering med Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/), och den nyligen utgivna [loggen aviseringar för Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) kapaciteten.

## <a name="define-a-base-query"></a>Definiera en grundläggande fråga

Om du vill komma igång definierar vi en grundläggande fråga som hämtar händelseloggen i Windows-händelser från Windows Azure-kanal som hämtats till Application Insights som poster för spårning.
Dessa poster kan användas för identifiering av olika orsaker i Azure Cloud Services som startfel, runtime, fel och återvinna slingor.

> [!NOTE]
> Grundläggande frågan nedan söker efter problem i ett tidsfönster på 30 minuter och förutsätter en 10 minuter fördröjning i vill föra in telemetri poster. Dessa standardinställningar kan konfigureras som du vill.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Sök efter specifika händelse-ID

När du har hämtat händelseloggen i Windows-händelser kan specifika problem som identifieras genom att söka efter sina respektive händelse-ID och ett meddelande-egenskaper (se exemplen nedan).
Kombinera bara grundläggande frågan ovan med en av frågorna nedan och används som kombineras frågan när du definierar varningsregeln loggen.

> [!NOTE]
> I exemplen nedan upptäcks ett problem om mer än tre händelser påträffas under det analyserade tidsfönstret. Den här standardinställningen kan konfigureras för att ändra känsligheten för regeln.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Skapa en avisering

Gå till i navigeringsmenyn i Application Insights-resurs **aviseringar**, och välj sedan **nya Varningsregeln**.

![Skärmbild av Skapa regel](./media/app-insights-proactive-cloud-services/001.png)

I den **Skapa regel** fönstret under den **definiera aviseringstillståndet** klickar du på **lägga till villkor**, och välj sedan **anpassad logg sökning**.

![Skärmbild av definiera villkor för avisering](./media/app-insights-proactive-cloud-services/002.png)

I den **sökfråga** klistra in den kombinerade frågan du förberett i föregående steg.

Fortsätt sedan till den **tröskelvärdet** rutan och ange värdet till 0. Du kan välja att justera det **Period** och frekvens **fält**.
Klicka på **Klar**.

![Skärmbild av Konfigurera signal logik fråga](./media/app-insights-proactive-cloud-services/003.png)

Under den **definiera aviseringsinformation** avsnittet, ange en **namn** och **beskrivning** till varningsregeln och ange dess **allvarlighetsgrad**.
Kontrollera också att som den **Aktivera regel när du skapar** knappen är inställd på **Ja**.

![Skärmbild aviseringsinformation](./media/app-insights-proactive-cloud-services/004.png)

Under den **definiera grupp** avsnitt, kan du välja en befintlig **grupp** eller skapa en ny.
Du har en åtgärdsgrupp som innehåller flera åtgärder av olika typer.

![Skärmbild grupp](./media/app-insights-proactive-cloud-services/005.png)

När du har definierat gruppen åtgärd bekräfta ändringarna och klicka på **skapa varningsregeln**.

## <a name="next-steps"></a>Nästa steg

Läs mer om att identifiera automatiskt:

[Fel avvikelser](app-insights-proactive-failure-diagnostics.md)
[minnesläckor](app-insights-proactive-potential-memory-leak.md)
[prestandaavvikelser](app-insights-proactive-performance-diagnostics.md)

