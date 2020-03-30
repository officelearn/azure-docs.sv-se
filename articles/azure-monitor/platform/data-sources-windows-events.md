---
title: Samla in och analysera Windows-händelseloggar i Azure Monitor | Microsoft-dokument
description: Beskriver hur du konfigurerar samlingen av Windows-händelseloggar av Azure Monitor och information om de poster de skapar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa34196233ce4037ef6fa49b782b9aa958f7632d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274689"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Datakällor för Windows-händelselogg i Azure Monitor
Windows-händelseloggar är en av de vanligaste [datakällorna](agent-data-sources.md) för att samla in data med Windows-agenter eftersom många program skriver till Windows-händelseloggen.  Du kan samla in händelser från standardloggar som System och Program förutom att ange anpassade loggar som skapats av program som du behöver övervaka.

![Windows-händelser](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfigurera Windows-händelseloggar
Konfigurera Windows-händelseloggar från [datamenyn i Avancerade inställningar](agent-data-sources.md#configuring-data-sources).

Azure Monitor samlar bara in händelser från Windows-händelseloggarna som anges i inställningarna.  Du kan lägga till en händelselogg genom att skriva **+** in loggens namn och klicka på .  För varje logg samlas endast händelser med de valda allvarlighetsgraderna in.  Kontrollera allvarlighetsgraden för den logg som du vill samla in.  Du kan inte ange några ytterligare kriterier för att filtrera händelser.

När du skriver namnet på en händelselogg ger Azure Monitor förslag på vanliga händelseloggnamn. Om loggen du vill lägga till inte visas i listan kan du fortfarande lägga till den genom att skriva in loggens fullständiga namn. Du hittar loggens fullständiga namn med hjälp av loggboken. Öppna sidan *Egenskaper* för loggen i Loggen i Loggen och kopiera strängen från fältet *Fullständigt namn.*

![Konfigurera Windows-händelser](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Kritiska händelser från Windows-händelseloggen har allvarlighetsgraden "Fel" i Azure Monitor Logs.

## <a name="data-collection"></a>Datainsamling
Azure Monitor samlar in varje händelse som matchar en vald allvarlighetsgrad från en övervakad händelselogg när händelsen skapas.  Agenten registrerar sin plats i varje händelselogg som den samlar in från.  Om agenten går offline under en viss tid samlar den in händelser från där den senast slutade, även om dessa händelser skapades medan agenten var offline.  Det finns en risk för att dessa händelser inte samlas in om händelseloggen radbryts med ej insamlade händelser som skrivs över medan agenten är offline.

>[!NOTE]
>Azure Monitor samlar inte in granskningshändelser som skapats av SQL Server från källan *MSSQLSERVER* med händelse-ID 18453 som innehåller nyckelord - *Klassisk* eller *Granskningsframgång* och nyckelord *0xa000000000000*.
>

## <a name="windows-event-records-properties"></a>Egenskaper för Windows-händelseposter
Windows-händelseposter har en typ av **händelse** och har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Namnet på den dator som händelsen hämtades från. |
| EventKategori |Kategori av händelsen. |
| EventData |Alla händelsedata i råformat. |
| EventID |Händelsens nummer. |
| EventLevel |Allvarlighetsgrad för händelsen i numerisk form. |
| EventLevelName |Allvarlighetsgrad för händelsen i textform. |
| Eventlog |Namn på händelseloggen som händelsen hämtades från. |
| ParameterXml |Händelseparametervärden i XML-format. |
| ManagementGroupName |Namn på hanteringsgruppen för System Center Operations Manager-agenter.  För andra agenter är detta värde`AOI-<workspace ID>` |
| RenderedDescription |Händelsebeskrivning med parametervärden |
| Källa |Källan till händelsen. |
| SourceSystem |Typ av agent händelsen samlades in från. <br> OpsManager – Windows-agent, antingen direktanslutning eller Operations Manager hanterad <br> Linux – Alla Linux-agenter  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Datum och tid då händelsen skapades i Windows. |
| UserName |Användarnamn för kontot som loggade händelsen. |

## <a name="log-queries-with-windows-events"></a>Logga frågor med Windows-händelser
Följande tabell innehåller olika exempel på loggfrågor som hämtar Windows-händelseposter.

| Söka i data | Beskrivning |
|:---|:---|
| Händelse |Alla Windows-händelser. |
| Händelse &#124; där EventLevelName == "fel" |Alla Windows-händelser med felens allvarlighetsgrad. |
| Händelse &#124; summera antal() efter källa |Antal Windows-händelser efter källa. |
| Händelse &#124; där EventLevelName == "fel" &#124; summera antal() efter källa |Antal Windows-felhändelser efter källa. |


## <a name="next-steps"></a>Nästa steg
* Konfigurera Logganalys för att samla in andra [datakällor](agent-data-sources.md) för analys.
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att analysera data som samlas in från datakällor och lösningar.  
* Konfigurera [insamling av prestandaräknare](data-sources-performance-counters.md) från dina Windows-agenter.
