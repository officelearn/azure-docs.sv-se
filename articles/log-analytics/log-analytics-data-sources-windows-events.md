---
title: "Samla in och analysera händelseloggarna för Windows i Azure Log Analytics | Microsoft Docs"
description: "Windows-händelseloggar är en av de vanligaste datakällor som används av logganalys.  Den här artikeln beskriver hur du konfigurerar insamling av Windows-händelseloggar och information om poster skapas i logganalys-arbetsytan."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: bwren
ms.openlocfilehash: 7a7deb4d7a287b2e9613e6035a7ffd7bb6f14f9c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Windows-händelseloggen datakällor i logganalys
Windows-händelseloggar är en av de vanligaste [datakällor](log-analytics-data-sources.md) för att samla in data med hjälp av Windows-agenter eftersom många program skriva till händelseloggen i Windows.  Du kan samla in händelser från standard loggar, till exempel System och program samt göra eventuella anpassade loggar som skapas av program som du behöver övervaka.

![Windows-händelser](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfigurera Windows-händelse loggar
Konfigurera Windows-händelseloggar från den [Data-menyn i logganalys-inställningar](log-analytics-data-sources.md#configuring-data-sources).

Logganalys endast samlar in händelser från händelseloggarna i Windows som anges i inställningarna.  Du kan lägga till en händelselogg genom att skriva namnet på loggen och klicka på  **+** .  Endast händelser med de valda allvarlighetsgraderna samlas in för varje logg.  Kontrollera allvarlighetsgraderna för viss loggen som du vill samla in.  Du kan inte ange några ytterligare kriterier för att filtrera händelser.

När du skriver namnet på en händelselogg innehåller logganalys förslag på Händelseloggnamn. Om den logg som du vill lägga till inte visas i listan kan du fortfarande lägga till den genom att skriva i det fullständiga namnet på loggen. Du kan söka efter det fullständiga namnet på loggen i Loggboken. I Loggboken, öppna den *egenskaper* för loggen och kopiera strängen från den *fullständiga namn* fältet.

![Konfigurera Windows-händelser](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Datainsamling
Logganalys samlar in varje händelse som matchar en vald allvarlighetsgrad från en övervakade händelseloggen som händelsen skapas.  Agenten registrerar dess plats i varje händelseloggen som samlas in från.  Om agenten tas offline under en tidsperiod, sedan logganalys samlar in händelser från där den senast slutade, även om de händelserna som skapades när agenten var offline.  Det finns risk för att dessa händelser som inte ska samlas in om händelseloggen radbryts med oinsamlat händelser att skrivas över när agenten är offline.

>[!NOTE]
>Logganalys samlar inte in granskningshändelser som skapats av SQL Server från källan *MSSQLSERVER* med händelse-ID 18453 som innehåller nyckelord - *klassiska* eller *granska lyckade* och nyckelordet *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Windows-händelse registrerar egenskaper
Windows händelseposter har en typ av **händelse** och ha egenskaper i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Namnet på den dator som händelsen samlats in från. |
| EventCategory |Kategori för händelsen. |
| EventData |Alla händelsedata i raw-format. |
| Händelse-ID |Antal händelsen. |
| EventLevel |Allvarlighetsgrad för händelsen i numeriska formatet. |
| EventLevelName |Allvarlighetsgrad för händelsen i textformat. |
| Händelseloggen |Namnet på den händelselogg som händelsen samlats in från. |
| ParameterXml |Händelsen parametervärden i XML-format. |
| ManagementGroupName |Namnet på hanteringsgruppen för System Center Operations Manager-agenter.  Det här värdet är för andra agenter AOI-<workspace ID> |
| RenderedDescription |Händelsebeskrivning med parametervärden |
| Källa |Källan för händelsen. |
| SourceSystem |Typ av agenten händelsen samlats in från. <br> Anslut OpsManager – Windows-agenten, antingen direkt eller hanteras av Operations Manager <br> Linux – alla Linux-agenter  <br> AzureStorage – Azure-diagnostik |
| TimeGenerated |Datum och tid då händelsen skapades i Windows. |
| Användarnamn |Användarnamnet för det konto som loggade händelsen. |

## <a name="log-searches-with-windows-events"></a>Loggen sökningar med Windows-händelser
Följande tabell innehåller olika exempel på loggen sökningar som hämtar poster för Windows-händelse.

| Fråga | Beskrivning |
|:---|:---|
| Händelse |Alla Windows-händelser. |
| Händelsen &#124; där EventLevelName == ”error” |Alla Windows-händelser med allvarlighetsgraden fel. |
| Händelsen &#124; Sammanfatta count() av källa |Antal Windows-händelser efter källa. |
| Händelsen &#124; där EventLevelName == ”error” &#124; Sammanfatta count() av källa |Antal Windows felhändelser efter källa. |


## <a name="next-steps"></a>Nästa steg
* Konfigurera Log Analytics för att samla in andra [datakällor](log-analytics-data-sources.md) för analys.
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar.  
* Använd [anpassade fält](log-analytics-custom-fields.md) att parsa händelseposter till enskilda fält.
* Konfigurera [insamling av prestandaräknare](log-analytics-data-sources-performance-counters.md) från Windows-agenter.
