---
title: Avisering om problem i Azure Cloud Services med Azure Diagnostics-integrering med Azure Application Insights | Microsoft-dokument
description: Övervaka för problem som startfel, krascher och rollåtervinningsloopar i Azure Cloud Services med Azure Application Insights
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 997c5e063c4181a597520e60e2a7669401b9677d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669751"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Avisering om problem i Azure Cloud Services med hjälp av Azure-diagnostikintegrering med Azure Application Insights

I den här artikeln beskriver vi hur du ställer in varningsregler som övervakar för problem som startfel, krascher och rollåtervinningsloopar i Azure Cloud Services (webb- och arbetsroller).

Metoden som beskrivs i den här artikeln baseras på [Azure Diagnostics-integreringen med Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)och den nyligen utgivna [loggaviseringar för Application Insights-funktionen.](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/)

## <a name="define-a-base-query"></a>Definiera en basfråga

För att komma igång definierar vi en basfråga som hämtar Windows-händelselogghändelserna från Windows Azure-kanalen, som fångas in i Application Insights som spårningsposter.
Dessa poster kan användas för att identifiera en mängd olika problem i Azure Cloud Services, till exempel startfel, körningsfel och återvinningsloopar.

> [!NOTE]
> Basfrågan nedan söker efter problem i ett tidsfönster på 30 minuter och förutsätter en svarstid på 10 minuter när du intar telemetriposterna. Dessa standardvärden kan konfigureras efter lämpligt.

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

## <a name="check-for-specific-event-ids"></a>Sök efter specifika händelse-ID:er

När du har hämtat händelserna i Windows-händelseloggen kan specifika problem identifieras genom att söka efter respektive händelse-ID och meddelandeegenskaper (se exempel nedan).
Kombinera helt enkelt basfrågan ovan med en av frågorna nedan och använd den kombinerade frågan när loggvarningsregeln definierades.

> [!NOTE]
> I exemplen nedan identifieras ett problem om fler än tre händelser hittas under det analyserade tidsfönstret. Den här standardinställningen kan konfigureras för att ändra känsligheten för varningsregeln.

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

Gå till Aviseringar på navigeringsmenyn i sidan Application Insights och välj sedan **Ny varningsregel**. **Alerts**

![Skärmbild av skapa regel](./media/proactive-cloud-services/001.png)

Klicka på **Lägg till villkor**under avsnittet Definiera **varningsvillkor** i fönstret **Skapa regel** och välj sedan **Anpassad loggsökning**.

![Skärmbild av definiera villkorsvillkor för avisering](./media/proactive-cloud-services/002.png)

Klistra in den kombinerade fråga som du förberett i föregående steg i rutan **Sökfråga.**

Fortsätt sedan till rutan **Tröskelvärde** och ange värdet till 0. Du kan eventuellt justera **fälten** **Period** och Frekvens .
Klicka på **Klar**.

![Skärmbild av konfigurera signallogikfråga](./media/proactive-cloud-services/003.png)

Under avsnittet **Definiera aviseringsinformation** anger du ett **namn** och **en beskrivning** till varningsregeln och anger dess **allvarlighetsgrad**.
Kontrollera också att **knappen Aktivera när** du skapar är inställd på **Ja**.

![Information om skärmbildsavisering](./media/proactive-cloud-services/004.png)

Under avsnittet **Definiera åtgärdsgrupp** kan du välja en befintlig **åtgärdsgrupp** eller skapa en ny.
Du kan välja att låta åtgärdsgruppen innehålla flera åtgärder av olika typer.

![Åtgärdsgrupp för skärmbild](./media/proactive-cloud-services/005.png)

När du har definierat åtgärdsgruppen bekräftar du ändringarna och klickar på **Skapa varningsregel**.

## <a name="next-steps"></a>Efterföljande moment

Läs mer om att automatiskt identifiera:

[Felavvikelser](../../azure-monitor/app/proactive-failure-diagnostics.md)
[Minnesläckning](../../azure-monitor/app/proactive-potential-memory-leak.md)
[Prestandaavvikelser](../../azure-monitor/app/proactive-performance-diagnostics.md)

