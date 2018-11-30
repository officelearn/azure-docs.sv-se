---
title: Konfigurera datakällor i Azure Log Analytics | Microsoft Docs
description: Datakällor definierar vilka data som Log Analytics samlar in från agenter och andra anslutna datakällor.  Den här artikeln beskriver begreppet hur Log Analytics använder datakällor, beskrivs detaljer om hur du konfigurerar dem och innehåller en sammanfattning av de olika datakällorna som är tillgängliga.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 8dad4c11ac309d959675d525fbeb48fe385cf4a5
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52337054"
---
# <a name="data-sources-in-log-analytics"></a>Datakällor i Log Analytics
Log Analytics samlar in data från dina anslutna källor och lagrar dem i Log Analytics-arbetsytan.  De data som samlas in från varje definieras av datakällor som du konfigurerar.  Data i Log Analytics lagras som en uppsättning poster.  Varje datakälla skapar poster i en viss typ med varje typ av att ha en egen uppsättning egenskaper.

![Logga Analytics datainsamling](./media/agent-data-sources/overview.png)

Datakällor skiljer sig från [hanteringslösningar](../../azure-monitor/insights/solutions.md), som också samla in data från anslutna källor och skapa poster i Log Analytics.  Förutom att samla in data är vanligtvis lösningar loggsökningar och vyer som hjälper dig att analysera driften av ett visst program eller tjänst.


## <a name="summary-of-data-sources"></a>Översikt över datakällor
I följande tabell visas de datakällor som är tillgängliga i Log Analytics.  Var och en har en länk till en separat artikel som tillhandahåller information för datakällan.   Det innehåller även information om deras metod och frekvensen för insamling av data till Log Analytics.  Du kan använda informationen i den här artikeln för att identifiera de olika lösningarna som är tillgängliga och förstå datakrav för flödet och anslutningen för olika lösningar. Förklaringar av kolumnerna finns i [Data samling information om lösningar i Azure](../../azure-monitor/insights/solutions-inventory.md).


| Datakälla | Plattform | Microsoft övervakningsagent | Operations Manager-agent | Azure-lagring | Operations Manager som krävs? | Operations Manager agent-data skickas via hanteringsgruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Anpassade loggar](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | anländer |
| [Anpassade loggar](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | anländer |
| [IIS-loggar](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |beror på inställningen för loggfil |
| [Prestandaräknare](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |som schemalagts, minst 10 sekunder |
| [Prestandaräknare](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |som schemalagts, minst 10 sekunder |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |från Azure storage: 10 minuter. från agent: anländer |
| [Windows-händelseloggar](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | anländer |


## <a name="configuring-data-sources"></a>Konfigurera datakällor
Du konfigurerar datakällor från den **Data** menyn i Log Analytics **avancerade inställningar**.  Valfri konfiguration levereras till alla anslutna källor i din arbetsyta.  Du kan inte för närvarande undanta alla eventuella agenter från den här konfigurationen.

![Konfigurera Windows-händelser](./media/agent-data-sources/configure-events.png)

1. I Azure-portalen väljer du **Log Analytics** > din arbetsyta > **avancerade inställningar**.
2. Välj **Data**.
3. Klicka på datakällan som du vill konfigurera.
4. Följer du länken till dokumentationen för varje datakälla i tabellen ovan för information på deras konfiguration.


## <a name="data-collection"></a>Datainsamling
Datakällskonfigurationer levereras till agenter som är anslutna direkt till Log Analytics inom några minuter.  Angivna data samlas in från agenten och levereras direkt till Log Analytics med intervall som är specifika för varje datakälla.  Finns i dokumentationen för varje datakälla dessa ge specifik information.

System Center Operations Manager-agenter i en ansluten hanteringsgrupp, datakällskonfigurationer översättas till hanteringspaket och levereras till hanteringsgruppen var femte minut som standard.  Agenten hämtar hanteringspaket som med andra och samlar in angivna data. Beroende på datakällan, data kommer att antingen skickas till en hanteringsserver som vidarebefordrar data till Log Analytics eller agenten skickar data till Log Analytics utan att gå via management-servern. Se [Data samling information om lösningar i Azure](../../azure-monitor/insights/solutions-inventory.md) mer information.  Du kan läsa om information om att ansluta Operations Manager och Log Analytics och ändra frekvensen konfigurationen levereras på [konfigurerar integrering med System Center Operations Manager](../../log-analytics/log-analytics-om-agents.md).

Om agenten inte kan ansluta till Log Analytics eller Operations Manager, fortsätter den att samla in data som den ger när den upprättar en anslutning.  Data kan förloras om mängden data når den största möjliga cachestorleken för klienten, eller om agenten inte kan upprätta en anslutning inom 24 timmar.

## <a name="log-analytics-records"></a>Log Analytics-poster
Alla data som samlas in av Log Analytics lagras som poster i arbetsytan.  Poster som samlas in av olika datakällor har sin egen uppsättning egenskaper och identifieras av sina **typ** egenskapen.  Finns i dokumentationen för varje datakälla och lösningen information på varje posttyp.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [lösningar](../../azure-monitor/insights/solutions.md) som lägger till funktioner i Log Analytics och också samla in data till arbetsytan.
* Lär dig mer om [loggsökningar](../../log-analytics/log-analytics-queries.md) att analysera data som samlas in från datakällor och lösningar.  
* Konfigurera [aviseringar](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) att informera dig om viktiga data som samlas in från datakällor och lösningar.
