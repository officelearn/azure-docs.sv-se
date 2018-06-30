---
title: Konfigurera datakällor i Azure Log Analytics | Microsoft Docs
description: Datakällor definierar vilka data att logganalys samlar in från agenter och andra anslutna källor.  Den här artikeln beskriver konceptet med hur Log Analytics använder datakällor beskrivs detaljer om hur du konfigurerar dem och innehåller en översikt över olika datakällor.
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
ms.component: na
ms.openlocfilehash: 9a43774a5327536ae4fa1346ae933739ea629771
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130092"
---
# <a name="data-sources-in-log-analytics"></a>Datakällor i logganalys
Logganalys samlar in data från dina anslutna källor och lagrar den i logganalys-arbetsytan.  De data som samlas in från varje definieras av datakällor som du konfigurerar.  Data i logganalys lagras som en uppsättning poster.  Varje datakälla skapar poster för en viss typ med varje typ med en egen uppsättning egenskaper.

![Logga Analytics datainsamling](./media/log-analytics-data-sources/overview.png)

Datakällor skiljer sig från [hanteringslösningar](log-analytics-add-solutions.md), som också samla in data från anslutna källor och skapa poster i logganalys.  Förutom att samla in data, inkluderar vanligtvis lösningar loggen sökningar och vyer som hjälper dig att analysera driften av ett visst program eller tjänst.


## <a name="summary-of-data-sources"></a>Översikt över datakällor
I följande tabell visas de datakällor som är tillgängliga i logganalys.  Varje har en länk till en separat artikel som tillhandahåller information för datakällan.   Det ger också information på deras metod och frekvens för datainsamling till logganalys.  Du kan använda informationen i den här artikeln för att identifiera de olika lösningarna som är tillgänglig och att förstå de trafikflöde och anslutningen kraven för olika lösningar. Beskrivningar av kolumnerna som finns i [Data samling information om lösningar i Azure](../monitoring/monitoring-solutions-inventory.md).


| Datakälla | Plattform | Microsoft övervakningsagent | Operations Manager-agent | Azure-lagring | Operations Manager som krävs? | Operations Manager agent-data som skickas via management-grupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Anpassade loggar](log-analytics-data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | anländer |
| [Anpassade loggar](log-analytics-data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | anländer |
| [IIS-loggar](log-analytics-data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |beror på inställningen för loggfil |
| [Prestandaräknare](log-analytics-data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |som schemalagts minst 10 sekunder |
| [Prestandaräknare](log-analytics-data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |som schemalagts minst 10 sekunder |
| [Syslog](log-analytics-data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |från Azure storage: 10 minuter. från agent: anländer |
| [Windows-händelseloggar](log-analytics-data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | anländer |


## <a name="configuring-data-sources"></a>Konfigurera datakällor
Du konfigurerar datakällor från den **Data** -menyn i logganalys **avancerade inställningar**.  Ingen konfiguration levereras till alla anslutna källor i arbetsytan.  Du kan för närvarande utesluta alla eventuella agenter från den här konfigurationen.

![Konfigurera Windows-händelser](./media/log-analytics-data-sources/configure-events.png)

1. Välj i Azure-portalen **logganalys** > din arbetsyta > **avancerade inställningar**.
2. Välj **Data**.
3. Klicka på datakällan som du vill konfigurera.
4. Följ länken i dokumentationen för varje datakälla i tabellen ovan information på deras konfiguration.


## <a name="data-collection"></a>Datainsamling
Konfigurationer för datakällan levereras till agenter som är anslutna direkt till Log Analytics inom några minuter.  Angivna data samlas in från agenten och levereras direkt till Log Analytics med intervall som är specifika för varje datakälla.  Finns i dokumentationen för varje datakälla som innehåller dessa uppgifter.

System Center Operations Manager-agenter i en ansluten hanteringsgrupp, konfigurationer för datakällan översättas till hanteringspaket och levereras till hanteringsgruppen var femte minut som standard.  Agenten hämtar management pack som andra och samlar in angivna data. Beroende på datakällan, kommer att antingen skickas till en hanteringsserver som vidarebefordrar data till Log Analytics eller agenten skickar data till logganalys utan att gå via management-servern. Referera till [information om samlingen](log-analytics-add-solutions.md#data-collection-details) mer information.  Du kan läsa om information om att ansluta Operations Manager och logganalys och ändra frekvensen konfigurationen levereras på [konfigurerar integrering med System Center Operations Manager](log-analytics-om-agents.md).

Om agenten inte kan ansluta till logganalys eller Operations Manager fortsätter den att samla in data som den levererar när den upprättar en anslutning.  Data kan gå förlorade om mängden data når maximal cachestorlek för klienten, eller om agenten inte kan upprätta en anslutning inom 24 timmar.

## <a name="log-analytics-records"></a>Log Analytics-poster
Alla data som samlas in av logganalys lagras i arbetsytan som poster.  Innehåller information som samlas in av olika datakällor har sin egen uppsättning egenskaper och identifieras genom sina **typen** egenskapen.  Finns i dokumentationen för varje datakälla och lösning för information om varje typ av post.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [lösningar](log-analytics-add-solutions.md) att lägga till funktioner till logganalys och också samla in data till arbetsytan.
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar.  
* Konfigurera [aviseringar](log-analytics-alerts.md) att proaktivt meddela dig om viktiga data som samlas in från datakällor och lösningar.
