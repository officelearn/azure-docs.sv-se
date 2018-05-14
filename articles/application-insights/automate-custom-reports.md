---
title: Automatisera anpassade rapporter med Azure Application Insights-data
description: Automatisera anpassade rapporter för varje dag/per vecka/månad med Azure Application Insights-data
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatisera anpassade rapporter med Azure Application Insights-data

Periodiska rapporter skydda ett team dig informerad om hur de kritiska företagstjänster gör. Utvecklare, DevOps/SRE team och deras chefer kan vara produktiva med automatisk rapporter som levereras på ett tillförlitligt sätt insikter utan alla att logga in på portalen. Dessa rapporter kan också identifiera stegvis ökar i fördröjningar, load eller fel priser som inte kan utlösa någon varning regler.

Varje företaget har dess unika rapporteringskrav som: 

* Specifika percentil aggregeringar av mått eller anpassade mått i en rapport.
* Har olika rapporter för dagliga, veckovisa och månatliga översikter över data för olika målgrupper.
* Segmentering av anpassade attribut som region eller miljö. 
* Gruppera vissa AI-resurser i en enda rapport, även om de finnas i olika prenumerationer eller resurs grupper osv.
* Separata rapporter som innehåller känsliga mått som skickas till selektiv målgrupp.
* Rapporter för att intressenter som inte kanske har åtkomst till portalen resurser.

> [!NOTE] 
> Varje vecka Application Insights sammanfattad e-postmeddelandet tillät inte någon anpassning och kommer att upphöra för anpassade alternativ nedan. Senaste veckovisa sammanfattad e-postmeddelandet skickas på den 11 juni 2018. Konfigurera en av följande alternativ för att hämta liknande anpassade rapporter (Använd frågan förslag nedan).

## <a name="to-automate-custom-report-emails"></a>Automatisera anpassad rapport e-post

Du kan [programmässigt fråga Application Insights](https://dev.applicationinsights.io/) data att skapa anpassade rapporter på ett schema. Följande alternativ kan hjälpa dig att komma igång snabbt:

* [Automatisera rapporter med Microsoft Flow](app-insights-automate-with-flow.md)
* [Automatisera rapporter med Logic Apps](automate-with-logic-apps.md)
* Använd ”Application Insights schemalagda digest” [Azure funktionen](https://azure.microsoft.com/services/functions/) mall i scenariot för övervakning. Den här funktionen använder SendGrid för att leverera e-postmeddelandet. 

    ![Mall för Azure-funktion](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Exempelfråga för en vecka sammanfattad e-post
Följande fråga visar anslutning över flera datamängder för en vecka sammanfattad e-postadress som rapporten. Anpassa den efter behov och använda den med något av alternativen som anges ovan för att automatisera en vecka rapport.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du skapar [Analytics-frågor](app-insights-analytics-using.md).
- Lär dig mer om [programmässigt datafrågor Application Insights](https://dev.applicationinsights.io/)
- Läs mer om [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
- Lär dig mer om [Microsoft Flow](https://ms.flow.microsoft.com).


