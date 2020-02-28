---
title: Samla in och analysera Windows-händelseloggar i Azure Monitor | Microsoft Docs
description: Beskriver hur du konfigurerar Windows-händelseloggen med Azure Monitor och information om de poster som de skapar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa34196233ce4037ef6fa49b782b9aa958f7632d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670516"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Windows händelse logg data källor i Azure Monitor
Windows-händelseloggar är en av de vanligaste [data källorna](agent-data-sources.md) för att samla in data med Windows-agenter sedan många program skriver till händelse loggen i Windows.  Du kan samla in händelser från standard loggar som system och program, förutom att ange anpassade loggar som skapats av program som du behöver övervaka.

![Windows-händelser](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfigurera händelse loggar i Windows
Konfigurera Windows-händelseloggar från [Data-menyn i avancerade inställningar](agent-data-sources.md#configuring-data-sources).

Azure Monitor samlar endast in händelser från Windows-händelseloggen som anges i inställningarna.  Du kan lägga till en händelse logg genom att skriva namnet på loggen och klicka på **+** .  Endast händelser med de valda allvarlighets graderna samlas in för varje logg.  Kontrol lera allvarlighets graderna för den specifika logg som du vill samla in.  Du kan inte ange några ytterligare kriterier för att filtrera händelser.

När du skriver namnet på en händelse logg ger Azure Monitor förslag på vanliga händelse logg namn. Om loggen som du vill lägga till inte visas i listan kan du fortfarande lägga till den genom att skriva i det fullständiga namnet på loggen. Du kan hitta det fullständiga namnet på loggen med hjälp av logg boken. I logg boken öppnar du sidan *Egenskaper* för loggen och kopierar strängen från fältet *fullständigt namn* .

![Konfigurera Windows-händelser](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Kritiska händelser från händelse loggen i Windows har allvarlighets graden "Error" i Azure Monitor loggar.

## <a name="data-collection"></a>Datainsamling
Azure Monitor samlar in varje händelse som matchar en vald allvarlighets grad från en övervakad händelse logg när händelsen skapas.  Agenten registrerar sitt ställe i varje händelse logg som samlas in från.  Om agenten blir offline under en viss tids period samlar den in händelser från den sista som den senast slutade, även om dessa händelser skapades medan agenten var offline.  Det är möjligt att dessa händelser inte samlas in om händelse loggen bryts med insamlade händelser som skrivs över medan agenten är offline.

>[!NOTE]
>Azure Monitor samlar inte in gransknings händelser som skapats av SQL Server från källan *MSSQLServer* med händelse-ID 18453 som innehåller nyckelord – *klassisk* eller *Granska lyckade* och nyckelords *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Egenskaper för Windows händelse poster
Händelse poster i Windows har en typ av **händelse** och har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Namnet på datorn som händelsen samlades in från. |
| EventCategory |Händelsens kategori. |
| EventData |Alla händelse data i RAW-format. |
| EventID |Händelsens nummer. |
| eventLevel |Händelsens allvarlighets grad i numerisk form. |
| EventLevelName |Allvarlighets grad för händelsen i text form. |
| EventLog |Namnet på händelse loggen som händelsen samlades in från. |
| ParameterXml |Händelse parameter värden i XML-format. |
| ManagementGroupName |Namnet på hanterings gruppen för System Center Operations Managers agenter.  För andra agenter är det här värdet `AOI-<workspace ID>` |
| RenderedDescription |Händelse Beskrivning med parameter värden |
| Källa |Händelsens källa. |
| SourceSystem |Typ av agent som händelsen samlades in från. <br> OpsManager – Windows-agent, antingen direkt anslutning eller Operations Manager hanterat <br> Linux – alla Linux-agenter  <br> AzureStorage – Azure-diagnostik |
| TimeGenerated |Datum och tid då händelsen skapades i Windows. |
| UserName |Användar namnet för det konto som loggade händelsen. |

## <a name="log-queries-with-windows-events"></a>Logga frågor med Windows-händelser
Följande tabell innehåller olika exempel på logg frågor som hämtar Windows-händelseloggar.

| Fråga | Beskrivning |
|:---|:---|
| Händelse |Alla Windows-händelser. |
| Händelse &#124; där EventLevelName = = "Error" |Alla Windows-händelser med allvarlighets graden fel. |
| Händelse &#124; sammanfattande antal () efter källa |Antal Windows-händelser efter källa. |
| Händelse &#124; där EventLevelName = = "Error" &#124; sammanfattar antal () efter källa |Antal Windows fel händelser per källa. |


## <a name="next-steps"></a>Nästa steg
* Konfigurera Log Analytics för att samla in andra [data källor](agent-data-sources.md) för analys.
* Lär dig mer om [logg frågor](../log-query/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.  
* Konfigurera [insamling av prestanda räknare](data-sources-performance-counters.md) från dina Windows-agenter.
