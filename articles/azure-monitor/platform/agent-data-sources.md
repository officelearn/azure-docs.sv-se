---
title: Konfigurera agenten datakällor i Azure Monitor | Microsoft Docs
description: Datakällor definierar loggdata att Azure Monitor samlar in från agenter och andra anslutna datakällor.  Den här artikeln beskriver begreppet hur Azure Monitor datakällor, beskrivs detaljer om hur du konfigurerar dem och innehåller en sammanfattning av de olika datakällorna som är tillgängliga.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: d7d4aa89c4dcf2ac9cc0c393e0481cae1f3aeaf2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58847008"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Agent-datakällor i Azure Monitor
De data som Azure Monitor samlar in från agenter definieras av datakällor som du konfigurerar.  Data från agenter lagras som [logga data över](data-platform-logs.md) med en uppsättning poster.  Varje datakälla skapar poster i en viss typ med varje typ av att ha en egen uppsättning egenskaper.

![Insamling av logg](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Översikt över datakällor
I följande tabell visas de agent-datakällor som är tillgängliga i Azure Monitor.  Var och en har en länk till en separat artikel som tillhandahåller information för datakällan.   Det innehåller även information om deras metod och insamlingsfrekvensen. 


| Datakälla | Plattform | Microsoft övervakningsagent | Operations Manager-agent | Azure-lagring | Operations Manager som krävs? | Operations Manager agent-data skickas via hanteringsgruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Anpassade loggar](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | anländer |
| [Anpassade loggar](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | anländer |
| [IIS-loggar](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |beror på inställningen för loggfil |
| [Prestandaräknare](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |som schemalagts, minst 10 sekunder |
| [Prestandaräknare](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |som schemalagts, minst 10 sekunder |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |från Azure storage: 10 minuter. från agent: anländer |
| [Windows-händelseloggar](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | anländer |


## <a name="configuring-data-sources"></a>Konfigurera datakällor
Du konfigurerar datakällor från den **Data** menyn i **avancerade inställningar** för arbetsytan.  Valfri konfiguration levereras till alla anslutna källor i din arbetsyta.  Du kan inte för närvarande undanta alla eventuella agenter från den här konfigurationen.

![Konfigurera Windows-händelser](media/agent-data-sources/configure-events.png)

1. I Azure-portalen väljer du **Log Analytics-arbetsytor** > din arbetsyta > **avancerade inställningar**.
2. Välj **Data**.
3. Klicka på datakällan som du vill konfigurera.
4. Följer du länken till dokumentationen för varje datakälla i tabellen ovan för information på deras konfiguration.


## <a name="data-collection"></a>Datainsamling
Datakällskonfigurationer levereras till agenter som är direkt anslutna till Azure Monitor inom några minuter.  Angivna data samlas in från agenten och levereras direkt till Azure Monitor med intervall som är specifika för varje datakälla.  Finns i dokumentationen för varje datakälla dessa ge specifik information.

System Center Operations Manager-agenter i en ansluten hanteringsgrupp, datakällskonfigurationer översättas till hanteringspaket och levereras till hanteringsgruppen var femte minut som standard.  Agenten hämtar hanteringspaket som med andra och samlar in angivna data. Beroende på datakällan, data kommer att antingen skickas till en hanteringsserver som vidarebefordrar data till Azure Monitor eller agenten skickar data till Azure Monitor utan att gå via management-servern. Se [samling som finns för att övervaka lösningar i Azure](../insights/solutions-inventory.md) mer information.  Du kan läsa om information om att ansluta Operations Manager och Azure Monitor och ändra frekvensen konfigurationen levereras på [konfigurerar integrering med System Center Operations Manager](om-agents.md).

Om agenten inte kan ansluta till Azure Monitor eller Operations Manager, fortsätter den att samla in data som den ger när den upprättar en anslutning.  Data kan förloras om mängden data når den största möjliga cachestorleken för klienten, eller om agenten inte kan upprätta en anslutning inom 24 timmar.

## <a name="log-records"></a>Loggposter
Alla loggdata som samlas in av Azure Monitor lagras som poster i arbetsytan.  Poster som samlas in av olika datakällor har sin egen uppsättning egenskaper och identifieras av sina **typ** egenskapen.  Finns i dokumentationen för varje datakälla och lösningen information på varje posttyp.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [övervakningslösningar](../insights/solutions.md) som lägger till funktioner i Azure Monitor och också samla in data till arbetsytan.
* Lär dig mer om [logga frågor](../log-query/log-query-overview.md) att analysera data som samlas in från datakällor och övervakningslösningar.  
* Konfigurera [aviseringar](alerts-overview.md) att informera dig om viktiga data som samlas in från datakällor och övervakningslösningar.