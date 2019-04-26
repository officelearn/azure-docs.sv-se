---
title: Samla in och analysera händelseloggarna för Windows i Azure Monitor | Microsoft Docs
description: Beskriver hur du konfigurerar insamling av Windows-händelseloggarna från Azure Monitor och information om de poster som de skapar.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: 8fcab1ead4ab6135e715dc173829178e43f8af2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236921"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Windows-händelseloggen datakällor i Azure Monitor
Windows-händelseloggar är en av de vanligaste [datakällor](agent-data-sources.md) för att samla in data med hjälp av Windows-agenter eftersom många program skriva till händelseloggen i Windows.  Du kan samla in händelser från standard loggarna, t.ex System- och förutom att ange några anpassade loggar som skapats av program som du behöver övervaka.

![Windows-händelser](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfigurera Windows-händelse loggar
Konfigurera Windows-händelseloggar från den [Data-menyn i avancerade inställningar](agent-data-sources.md#configuring-data-sources).

Azure Monitor samlar endast in händelser från händelseloggarna för Windows som anges i inställningarna.  Du kan lägga till en händelselogg genom att skriva namnet på loggen och klicka på **+**.  Endast händelser med valda allvarlighetsgraderna samlas in för varje logg.  Kontrollera allvarlighetsgraderna för viss loggen som du vill samla in.  Du kan inte ange några ytterligare kriterier för att filtrera händelser.

När du skriver namnet på en händelselogg innehåller Azure Monitor förslag på vanliga Händelseloggnamn. Om du vill lägga till loggen inte visas i listan, du kan fortfarande lägga till den genom att skriva i det fullständiga namnet på loggen. Du hittar det fullständiga namnet på loggen med hjälp av Loggboken. I Loggboken, öppna den *egenskaper* för loggen och kopierar du strängen från den *fullständigt namn* fält.

![Konfigurera Windows-händelser](media/data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Datainsamling
Azure Monitor samlar in varje händelse som matchar en vald allvarlighetsgrad från en övervakade händelseloggen när händelsen skapas.  Agenten registrerar transformeringsfilen i varje händelseloggen som det samlar in från.  Om agenten kopplas från för en viss tidsperiod, sedan den samlar in händelser från där den senast slutade, även om de händelserna som skapades när agenten var offline.  Finns det risk för dessa händelser som inte ska samlas in om händelseloggen radbryts med obetalda händelser att skrivas över när agenten är offline.

>[!NOTE]
>Azure Monitor samlar inte in granskningshändelser som skapats av SQL Server från källa *MSSQLSERVER* med händelse-ID 18453 som innehåller nyckelord - *klassiska* eller *granska lyckade* och nyckelordet *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Egenskaper för Windows händelse-poster
Windows-händelseposter har en typ av **händelse** och har egenskaperna i följande tabell:

| Egenskap  | Beskrivning |
|:--- |:--- |
| Computer |Namnet på den dator som händelsen har samlats in från. |
| EventCategory |Händelsens kategori. |
| EventData |Alla händelsedata i obearbetat format. |
| EventID |Antal händelsen. |
| eventLevel |Allvarlighetsgrad för händelsen i numeriskt format. |
| EventLevelName |Allvarlighetsgrad för händelsen i textformat. |
| Händelseloggen |Namnet på händelseloggen som händelsen har samlats in från. |
| ParameterXml |Händelsen parametervärden i XML-format. |
| ManagementGroupName |Namnet på hanteringsgruppen för System Center Operations Manager-agenter.  För övriga agenter är är det här värdet `AOI-<workspace ID>` |
| RenderedDescription |Händelsebeskrivning med parametervärden |
| Källa |Källa för händelsen. |
| SourceSystem |Typ av händelsen har samlats in från agenten. <br> Ansluta OpsManager – Windows-agenten, antingen direkt eller Operations Manager-hanterad <br> Linux – alla Linux-agenter  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Datum och tid då händelsen skapades i Windows. |
| Användarnamn |Användarnamn för det konto som loggade händelsen. |

## <a name="log-queries-with-windows-events"></a>Loggfrågor med Windows-händelser
I följande tabell innehåller olika exempel på loggfrågor som hämtar Windows händelseposter.

| Fråga | Beskrivning |
|:---|:---|
| Händelse |Alla Windows-händelser. |
| Händelsen &#124; där EventLevelName == ”error” |Alla Windows-händelser med allvarlighetsgrad för fel. |
| Händelsen &#124; sammanfatta antal() efter källa |Antal Windows-händelser efter källa. |
| Händelsen &#124; där EventLevelName == ”error” &#124; sammanfatta antal() efter källa |Antal Windows felhändelser av källa. |


## <a name="next-steps"></a>Nästa steg
* Konfigurera Log Analytics för att samla in andra [datakällor](agent-data-sources.md) för analys.
* Lär dig mer om [logga frågor](../log-query/log-query-overview.md) att analysera data som samlas in från datakällor och lösningar.  
* Konfigurera [insamling av prestandaräknare](data-sources-performance-counters.md) från din Windows-agenter.