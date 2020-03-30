---
title: Varningshanteringslösning i Azure Log Analytics | Microsoft-dokument
description: Alert Management-lösningen i Log Analytics hjälper dig att analysera alla aviseringar i din miljö.  Förutom att konsolidera aviseringar som genereras i Log Analytics importeras aviseringar från anslutna System Center Operations Manager-hanteringsgrupper till Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668391"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Varningshanteringslösning i Azure Log Analytics

![Ikon för varningshantering](media/alert-management-solution/icon.png)

> [!NOTE]
>  Azure Monitor har nu stöd för förbättrade funktioner för [att hantera dina aviseringar i stor skala,](https://aka.ms/azure-alerts-overview)inklusive de som genereras av [övervakningsverktyg som System Center Operations Manager, Zabbix eller Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


Alert Management-lösningen hjälper dig att analysera alla aviseringar i log analytics-databasen.  Dessa varningar kan ha kommit från en mängd olika källor, inklusive de källor som [skapats av Log Analytics](../../azure-monitor/platform/alerts-overview.md) eller [importeras från Nagios eller Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). Lösningen importerar också aviseringar från alla [anslutna hanteringsgrupper](../../azure-monitor/platform/om-agents.md)för System Center Operations Manager .

## <a name="prerequisites"></a>Krav
Lösningen fungerar med alla poster i Log Analytics-databasen med en typ av **avisering**, så du måste utföra den konfiguration som krävs för att samla in dessa poster.

- För Log Analytics-aviseringar [skapar du varningsregler](../../azure-monitor/platform/alerts-overview.md) för att skapa varningsposter direkt i databasen.
- För Nagios- och Zabbix-aviseringar [konfigurerar du dessa servrar](../../azure-monitor/learn/quick-collect-linux-computer.md) så att aviseringar skickas till Log Analytics.
- För System Center Operations Manager-aviseringar [ansluter du hanteringsgruppen för Operations Manager till logganalysarbetsytan](../../azure-monitor/platform/om-agents.md).  Alla aviseringar som skapas i System Center Operations Manager importeras till Logganalys.  

## <a name="configuration"></a>Konfiguration
Lägg till alert management-lösningen på din Log Analytics-arbetsyta med hjälp av processen som beskrivs i [Lägg till lösningar](../../azure-monitor/insights/solutions.md). Det krävs ingen ytterligare konfiguration.

## <a name="management-packs"></a>Hanteringspaket
Om hanteringsgruppen för System Center Operations Manager är ansluten till logganalysarbetsytan installeras följande hanteringspaket i System Center Operations Manager när du lägger till den här lösningen.  Det krävs ingen konfiguration eller underhåll av hanteringspaketen.

* Varningshantering för Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Datainsamling
### <a name="agents"></a>Agenter
I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Support | Beskrivning |
|:--- |:--- |:--- |
| [Windows-agenter](agent-windows.md) | Inga |Direkt Windows-agenter genererar inte aviseringar.  Logganalysvarningar kan skapas från händelser och prestandadata som samlas in från Windows-agenter. |
| [Linux-agenter](../../azure-monitor/learn/quick-collect-linux-computer.md) | Inga |Direkta Linux-agenter genererar inte aviseringar.  Logganalysvarningar kan skapas från händelser och prestandadata som samlas in från Linux-agenter.  Nagios och Zabbix varningar samlas in från de servrar som kräver Linux-agenten. |
| [System Center Operations Manager-hanteringsgrupp](../../azure-monitor/platform/om-agents.md) |Ja |Aviseringar som genereras på Operations Manager-agenter levereras till hanteringsgruppen och vidarebefordras sedan till Log Analytics.<br><br>En direkt anslutning från Operations Manager-agenter till Log Analytics krävs inte. Varningsdata vidarebefordras från hanteringsgruppen till Log Analytics-databasen. |


### <a name="collection-frequency"></a>Insamlingsfrekvens
- Varningsposter är tillgängliga för lösningen så snart de lagras i databasen.
- Varningsdata skickas från hanteringsgruppen För Operations Manager till Log Analytics var tredje minut.  

## <a name="using-the-solution"></a>Använda lösningen
När du lägger till alerthanteringslösningen på logganalysarbetsytan läggs panelen **Varningshantering** till på instrumentpanelen.  Den här panelen visar en antal och grafisk representation av antalet aktiva aviseringar som har genererats under de senaste 24 timmarna.  Du kan inte ändra det här tidsintervallet.

![Panelen Varningshantering](media/alert-management-solution/tile.png)

Klicka på panelen **Aviseringshantering** för att öppna instrumentpanelen **för varningshantering.**  Instrumentpanelen innehåller kolumnerna i följande tabell.  Varje kolumn visar de 10 aviseringar som visas efter att räkna matchande den kolumnens villkor för det angivna scopet och tidsintervallet.  Du kan köra en loggsökning som ger hela listan genom att klicka på **Visa alla** längst ned i kolumnen eller genom att klicka på kolumnrubriken.

| Kolumn | Beskrivning |
|:--- |:--- |
| Kritiska varningar |Alla aviseringar med allvarlighetsgraden kritisk grupperad efter aviseringsnamn.  Klicka på ett aviseringsnamn om du vill köra en loggsökning som returnerar alla poster för den aviseringen. |
| Varningsvarningar |Alla aviseringar med allvarlighetsgraden Varning grupperad efter varningsnamn.  Klicka på ett aviseringsnamn om du vill köra en loggsökning som returnerar alla poster för den aviseringen. |
| Varningar för Aktiv System Center Operations Manager |Alla aviseringar som samlats in från Operations Manager med något annat tillstånd än *Stängd* grupperad efter källa som genererade aviseringen. |
| Alla aktiva aviseringar |Alla aviseringar med eventuell allvarlighetsgrad grupperade efter aviseringsnamn. Inkluderar endast Operations Manager-aviseringar med något annat tillstånd än *Stängt*. |

Om du bläddrar åt höger listar instrumentpanelen flera vanliga frågor som du kan klicka på för att utföra en [loggsökning](../../azure-monitor/log-query/log-query-overview.md) efter varningsdata.

![Instrumentpanel för varningshantering](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-poster
Aviseringshanteringslösningen analyserar alla poster med en typ av **avisering**.  Varningar som skapats av Log Analytics eller samlats in från Nagios eller Zabbix samlas inte in direkt av lösningen.

Lösningen importerar aviseringar från System Center Operations Manager och skapar en motsvarande post för var och en med en typ av **avisering** och ett SourceSystem of **OpsManager**.  Dessa poster har egenskaperna i följande tabell:  

| Egenskap | Beskrivning |
|:--- |:--- |
| `Type` |*Varning* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Information om det dataobjekt som gjorde att aviseringen genererades i XML-format. |
| `AlertDescription` |Detaljerad beskrivning av varningen. |
| `AlertId` |GUID för aviseringen. |
| `AlertName` |Aviseringens namn. |
| `AlertPriority` |Prioritetsnivå för aviseringen. |
| `AlertSeverity` |Allvarlighetsgrad för aviseringen. |
| `AlertState` |Senaste upplösningstillståndet för aviseringen. |
| `LastModifiedBy` |Namn på den användare som senast ändrade aviseringen. |
| `ManagementGroupName` |Namn på den hanteringsgrupp där aviseringen genererades. |
| `RepeatCount` |Antal gånger samma avisering genererades för samma övervakade objekt sedan det löstes. |
| `ResolvedBy` |Namn på den användare som löste aviseringen. Tom om aviseringen ännu inte har lösts. |
| `SourceDisplayName` |Visa namnet på det övervakningsobjekt som genererade aviseringen. |
| `SourceFullName` |Fullständigt namn på övervakningsobjektet som genererade aviseringen. |
| `TicketId` |Biljett-ID för aviseringen om System Center Operations Manager-miljön är integrerad med en process för tilldelning av biljetter för aviseringar.  Tömd på inget biljett-ID har tilldelats. |
| `TimeGenerated` |Datum och tid då aviseringen skapades. |
| `TimeLastModified` |Datum och tid då aviseringen senast ändrades. |
| `TimeRaised` |Datum och tid då aviseringen genererades. |
| `TimeResolved` |Datum och tid då aviseringen löstes. Tom om aviseringen ännu inte har lösts. |

## <a name="sample-log-searches"></a>Exempel på loggsökningar
I följande tabell visas exempelloggsökningar efter varningsposter som samlats in av den här lösningen: 

| Söka i data | Beskrivning |
|:---|:---|
| Varning &#124; där SourceSystem == "OpsManager" och AlertSeverity == "error" och TimeRaised > sedan(24h) |Kritiska varningar som höjts under de senaste 24 timmarna |
| Varning &#124; där AlertSeverity == "varning" och TimeRaised > sedan(24h) |Varningsvarningar som höjts under de senaste 24 timmarna |
| Avisering &#124; där SourceSystem == "OpsManager" och AlertState != "Closed" och TimeRaised > sedan(24h) &#124; sammanfatta Antal = antal() av SourceDisplayName |Källor med aktiva varningar som höjts under de senaste 24 timmarna |
| Varning &#124; där SourceSystem == "OpsManager" och AlertSeverity == "error" och TimeRaised > sedan(24h) och AlertState != "Stängd" |Kritiska varningar som tagits upp under de senaste 24 timmarna och som fortfarande är aktiva |
| Varning &#124; där SourceSystem == "OpsManager" och TimeRaised > sedan(24h) och AlertState == "Stängd" |Varningar som höjts under de senaste 24 timmarna som nu är stängda |
| Avisering &#124; där SourceSystem == "OpsManager" och TimeRaised > sedan(1d) &#124; sammanfatta Antal = antal() efter AlertSeverity |Varningar som tagits upp under de senaste 1 dagarna grupperade efter deras allvarlighetsgrad |
| Avisering &#124; där SourceSystem == "OpsManager" och TimeRaised > sedan(1d) &#124; sortera efter RepeatCount desc |Aviseringar som höjts under de senaste 1 dagarna sorterade efter deras upprepade räknevärde |



## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Aviseringar i Log Analytics](../../azure-monitor/platform/alerts-overview.md) för information om att generera aviseringar från Log Analytics.
