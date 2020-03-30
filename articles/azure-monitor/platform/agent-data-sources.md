---
title: Konfigurera agentdatakällor i Azure Monitor | Microsoft-dokument
description: Datakällor definierar loggdata som Azure Monitor samlar in från agenter och andra anslutna källor.  I den här artikeln beskrivs hur Azure Monitor använder datakällor, information om hur du konfigurerar dem och innehåller en sammanfattning av de olika datakällor som är tillgängliga.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aec3fe2386ce916c556f6da295a8554fff140259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249105"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Agentdatakällor i Azure Monitor
De data som Azure Monitor samlar in från agenter definieras av de datakällor som du konfigurerar.  Data från agenter lagras som [loggdata](data-platform-logs.md) med en uppsättning poster.  Varje datakälla skapar poster av en viss typ där varje typ har en egen uppsättning egenskaper.

![Logga indata](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Sammanfattning av datakällor
I följande tabell visas de agentdatakällor som för närvarande är tillgängliga i Azure Monitor.  Var och en har en länk till en separat artikel med information om den datakällan.   Det ger också information om deras metod och frekvens för insamling. 


| Datakälla | Plattform | Logganalysagent | Operations Manager-agent | Azure Storage | Krävs Operations Manager? | Operations Manager-agentdata som skickas via hanteringsgrupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Anpassade loggar](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | vid ankomsten |
| [Anpassade loggar](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | vid ankomsten |
| [IIS-loggar](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |beror på inställningen För loggfilsvältning |
| [Prestandaräknare](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |som planerat, minst 10 sekunder |
| [Prestandaräknare](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |som planerat, minst 10 sekunder |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |från Azure-lagring: 10 minuter; från agent: vid ankomsten |
| [Windows-händelseloggar](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | vid ankomsten |


## <a name="configuring-data-sources"></a>Konfigurera datakällor
Du konfigurerar datakällor från **datamenyn** i **Avancerade inställningar** för arbetsytan.  Alla konfigurationer levereras till alla anslutna källor på arbetsytan.  Du kan för närvarande inte utesluta några agenter från den här konfigurationen.

![Konfigurera Windows-händelser](media/agent-data-sources/configure-events.png)

1. I Azure-portalen väljer du **Log Analytics-arbetsytor** > arbetsytan > **avancerade inställningar**.
2. Välj **data**.
3. Klicka på den datakälla som du vill konfigurera.
4. Följ länken till dokumentationen för varje datakälla i tabellen ovan för mer information om deras konfiguration.


## <a name="data-collection"></a>Datainsamling
Datakällkonfigurationer levereras till agenter som är direkt anslutna till Azure Monitor inom några minuter.  De angivna data samlas in från agenten och levereras direkt till Azure Monitor med intervall som är specifika för varje datakälla.  Se dokumentationen för varje datakälla för dessa specifika uppgifter.

För System Center Operations Manager-agenter i en ansluten hanteringsgrupp översätts datakällkonfigurationer till hanteringspaket och levereras till hanteringsgruppen var femte minut som standard.  Agenten hämtar hanteringspaketet som alla andra och samlar in angivna data. Beroende på datakällan skickas data antingen till en hanteringsserver som vidarebefordrar data till Azure Monitor, eller så skickar agenten data till Azure Monitor utan att gå via hanteringsservern. Mer information finns [i Information om datainsamling för övervakningslösningar i Azure.](../insights/solutions-inventory.md)  Du kan läsa om information om hur du ansluter Operations Manager och Azure Monitor och ändrar hur ofta konfigurationen levereras vid [Konfigurera integrering med System Center Operations Manager](om-agents.md).

Om agenten inte kan ansluta till Azure Monitor eller Operations Manager fortsätter den att samla in data som den ska leverera när den upprättar en anslutning.  Data kan gå förlorade om mängden data når den maximala cachestorleken för klienten, eller om agenten inte kan upprätta en anslutning inom 24 timmar.

## <a name="log-records"></a>Logga poster
Alla loggdata som samlas in av Azure Monitor lagras på arbetsytan som poster.  Poster som samlas in av olika datakällor kommer att **Type** ha sin egen uppsättning egenskaper och identifieras av deras typegenskap.  Mer information om varje posttyp finns i dokumentationen för varje datakälla och -lösning.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [övervakningslösningar](../insights/solutions.md) som lägger till funktioner i Azure Monitor och även samlar in data på arbetsytan.
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att analysera data som samlats in från datakällor och övervakningslösningar.  
* Konfigurera [aviseringar så](alerts-overview.md) att du proaktivt meddelar dig om viktiga data som samlats in från datakällor och övervakningslösningar.
