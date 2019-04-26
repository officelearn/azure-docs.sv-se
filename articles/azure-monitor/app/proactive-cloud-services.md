---
title: Avisera om problem i Azure Cloud Services med hjälp av Azure Diagnostics-integrering med Azure Application Insights | Microsoft Docs
description: Övervakare för problem som startfel, krascher eller rollen Papperskorgen loopar i Azure Cloud Services med Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.author: mbullwin
ms.openlocfilehash: 219ba632d7688f1a428378309828b689698d2fe5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409551"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Avisera om problem i Azure Cloud Services med hjälp av Azure diagnostics-integrering med Azure Application Insights

I den här artikeln beskriver vi hur du ställer in aviseringsregler som övervakar för problem som startfel, krascher eller rollen Papperskorgen loopar i Azure Cloud Services (webb- och worker-roller).

Den metod som beskrivs i den här artikeln baseras på den [Azure Diagnostics-integrering med Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/), och den nyligen utgiven [Loggaviseringar för Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) kapaciteten.

## <a name="define-a-base-query"></a>Definiera en grundläggande fråga

För att komma igång, vi kommer att definiera en grundläggande fråga som hämtar händelseloggen i Windows-händelser från Windows Azure-kanal som ska samlas in i Application Insights som spårningen poster.
Dessa poster kan användas för att upptäcka olika problem i Azure Cloud Services, som startfel, runtime, fel och Papperskorgen slingor.

> [!NOTE]
> Söker efter problem i ett tidsfönster på 30 minuter grundläggande frågan nedan och förutsätter en 10 minuter fördröjning i föra in telemetri-poster. Dessa standardinställningar kan konfigureras som du vill.

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

När du hämtar Windows Event Log-händelser, kan specifika problem identifieras genom att söka efter deras respektive händelse-ID och ett meddelande-egenskaper (se exemplen nedan).
Kombinera bara grundläggande frågan ovan med någon av frågorna nedan och används som kombinerar frågan när du definierar loggvarningsregel.

> [!NOTE]
> I exemplen nedan är identifieras ett problem om fler än tre händelser påträffas under tidsperioden för analyseras. Det här standardvärdet kan konfigureras för att ändra hur känsliga varningsregeln.

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

På navigeringsmenyn i Application Insights-resursen, gå till **aviseringar**, och välj sedan **ny Aviseringsregel**.

![Skärmbild av Skapa regel](./media/proactive-cloud-services/001.png)

I den **skapa regeln** fönstret under den **definiera aviseringsvillkoret** klickar du på **lägga till villkor**, och välj sedan **anpassad loggsökning**.

![Skärmbild av definiera villkor för avisering](./media/proactive-cloud-services/002.png)

I den **sökfråga** rutan, klistra in den kombinerade fråga som du förberett i föregående steg.

Fortsätt sedan till den **tröskelvärdet** rutan och ange värdet till 0. Du kan välja att justera de **Period** och frekvens **fält**.
Klicka på **Klar**.

![Skärmbild av Konfigurera signalen logic-fråga](./media/proactive-cloud-services/003.png)

Under den **definiera Aviseringsinformationen** avsnittet tillhandahåller en **namn** och **beskrivning** till varningsregeln och Ställ in dess **allvarlighetsgrad**.
Kontrollera också att som den **aktivera regeln vid skapande** knappen är inställd på **Ja**.

![Skärmbild aviseringsinformation](./media/proactive-cloud-services/004.png)

Under den **definiera åtgärdsgruppen** avsnittet, du kan välja en befintlig **åtgärdsgrupp** eller skapa en ny.
Du kan välja att låta åtgärdsgruppen innehåller flera åtgärder för olika typer av.

![Skärmbild av åtgärdsgrupp](./media/proactive-cloud-services/005.png)

När du har definierat åtgärdsgruppen, bekräfta dina ändringar och klicka på **skapa varningsregel**.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att automatiskt identifiera:

[Felavvikelser](../../azure-monitor/app/proactive-failure-diagnostics.md)
[minnesläckor](../../azure-monitor/app/proactive-potential-memory-leak.md)
[prestandaavvikelser](../../azure-monitor/app/proactive-performance-diagnostics.md)

