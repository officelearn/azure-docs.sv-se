---
title: Lösning i Azure Log Analytics för aviseringshantering | Microsoft Docs
description: Aviseringshantering-lösningen i Log Analytics kan du analysera alla aviseringar i din miljö.  Förutom att konsolidera aviseringar i Log Analytics, importerar det aviseringar från anslutna hanteringsgrupper i System Center Operations Manager till Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 1dee53f633d8b5edf893e2f6c83430d7c1a69022
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53341600"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Lösning i Azure Log Analytics för aviseringshantering

![Varningsikon för hantering](media/alert-management-solution/icon.png)

Aviseringshantering-lösningen hjälper dig att analysera alla aviseringar i Log Analytics-lagringsplatsen.  Dessa aviseringar kan härröra från olika källor, inklusive de källorna [skapats av Log Analytics](../../azure-monitor/platform/alerts-overview.md) eller [importeras från Nagios och Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). Lösningen också importerar aviseringar från någon [anslutna System Center Operations Manager-hanteringsgrupper](../../azure-monitor/platform/om-agents.md).

## <a name="prerequisites"></a>Förutsättningar
Den här lösningen fungerar med alla poster i Log Analytics-databasen med en typ av **avisering**, så du måste utföra konfigurationen som krävs för att samla in dessa poster.

- För Log Analytics-aviseringar [skapa Varningsregler](../../azure-monitor/platform/alerts-overview.md) skapa aviseringsposter direkt i databasen.
- För Nagios och Zabbix-aviseringar [Konfigurera servrarna](../../azure-monitor/learn/quick-collect-linux-computer.md) att skicka aviseringar till Log Analytics.
- För System Center Operations Manager-aviseringar [ansluta din Operations Manager-hanteringsgrupp till Log Analytics-arbetsytan](../../azure-monitor/platform/om-agents.md).  Alla varningar som skapats i System Center Operations Manager importeras till Log Analytics.  

## <a name="configuration"></a>Konfiguration
Lägg till lösning för aviseringshantering till Log Analytics-arbetsytan med processen som beskrivs i [lägga till lösningar](../../azure-monitor/insights/solutions.md). Det krävs ingen ytterligare konfiguration.

## <a name="management-packs"></a>Hanteringspaket
Om din hanteringsgrupp för System Center Operations Manager är ansluten till Log Analytics-arbetsytan, installeras följande hanteringspaket i System Center Operations Manager när du lägger till den här lösningen.  Det finns ingen konfigurering eller underhåll av de hanteringspaket som krävs.

* Microsoft System Center Advisor-aviseringshantering (Microsoft.IntelligencePacks.AlertManagement)

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Datainsamling
### <a name="agents"></a>Agenter
I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Support | Beskrivning |
|:--- |:--- |:--- |
| [Windows-agenter](agent-windows.md) | Nej |Direkta Windows-agenter genererar inte aviseringar.  Log Analytics-aviseringar kan skapas från händelser och prestandadata som samlats in från Windows agenter. |
| [Linux-agenter](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nej |Direct Linux-agenter genererar inte aviseringar.  Log Analytics-aviseringar kan skapas från händelserna och prestandadata som samlats in från Linux-agenter.  Nagios och Zabbix-aviseringar har samlats in från dessa servrar som kräver Linux-agenten. |
| [System Center Operations Manager-hanteringsgrupp](../../azure-monitor/platform/om-agents.md) |Ja |Aviseringar som genereras i Operations Manager-agenter levereras till hanteringsgruppen och sedan vidarebefordras till logganalys.<br><br>En direktanslutning från Operations Manager-agenter till Log Analytics krävs inte. Aviseringsdata vidarebefordras från hanteringsgruppen till Log Analytics-databasen. |


### <a name="collection-frequency"></a>Insamlingsfrekvens
- Aviseringsposter som är tillgängliga för lösningen när de lagras i databasen.
- Aviseringsdata skickas från Operations Manager-hanteringsgrupp till Log Analytics var tredje minut.  

## <a name="using-the-solution"></a>Använda lösningen
När du lägger till lösningen för hantering av varningar i Log Analytics-arbetsytan i **aviseringshantering** panel har lagts till i instrumentpanelen.  Den här panelen visar antal och grafisk representation av antalet aktiva aviseringar som har genererats under de senaste 24 timmarna.  Du kan inte ändra det här tidsintervallet.

![Avisera panelen hantering](media/alert-management-solution/tile.png)

Klicka på den **aviseringshantering** panelen för att öppna den **aviseringshantering** instrumentpanelen.  Instrumentpanelen innehåller kolumnerna i följande tabell.  Varje kolumn visar de översta 10 aviseringarna efter antal som matchar denna kolumns kriterier för specificerat omfång och tidsintervall.  Du kan köra en loggsökning som innehåller hela listan genom att klicka på **se alla** längst ned i kolumnen eller genom att klicka på kolumnrubriken.

| Kolumn | Beskrivning |
|:--- |:--- |
| Kritiska aviseringar |Alla aviseringar med en allvarlighetsgraden kritiskt grupperade efter aviseringens namn.  Klicka på ett namn på avisering att köra en loggsökning som returnerar alla poster för den här aviseringen. |
| Varningsaviseringar |Alla aviseringar med en allvarlighetsgrad för varning grupperade efter aviseringsnamn.  Klicka på ett namn på avisering att köra en loggsökning som returnerar alla poster för den här aviseringen. |
| Aktiva SCOM-aviseringar |Alla aviseringar som samlas in från Operations Manager med några tillstånd än *stängd* grupperade efter källa som skapade aviseringen. |
| Alla aktiva aviseringar |Alla aviseringar med alla allvarlighetsgrad grupperade efter aviseringens namn. Bara innehåller Operations Manager-varningar med några tillstånd än *stängd*. |

Om du bläddrar till höger på instrumentpanelen visar flera vanliga frågor som du kan klicka på för att utföra en [loggsökning](../../azure-monitor/log-query/log-query-overview.md) för aviseringsdata.

![Hanteringspanel för avisering](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-poster
Aviseringshantering lösningen analyserar en post med en typ av **avisering**.  Aviseringar skapas av Log Analytics eller som samlas in från Nagios och Zabbix samlas inte direkt av lösningen.

Lösningen går att importera aviseringar från System Center Operations Manager och skapar en post för var och en med en typ av **avisering** och SourceSystem **OpsManager**.  Dessa poster har egenskaper i följande tabell:  

| Egenskap  | Beskrivning |
|:--- |:--- |
| Typ |*Avisera* |
| SourceSystem |*OpsManager* |
| AlertContext |Information om de dataobjekt som orsakade aviseringen ska genereras i XML-format. |
| AlertDescription |Detaljerad beskrivning av aviseringen. |
| AlertId |GUID för aviseringen. |
| AlertName |Namnet på aviseringen. |
| AlertPriority |Prioritetsnivån för aviseringen. |
| AlertSeverity |Allvarlighetsgraden för aviseringen. |
| AlertState |Senaste lösningstillståndet för aviseringen. |
| LastModifiedBy |Namn på den användare som senast ändrade aviseringen. |
| ManagementGroupName |Namnet på hanteringsgruppen där aviseringen genererades. |
| RepeatCount |Många gånger samma avisering har genererats för samma övervakade objekt sedan matchas. |
| ResolvedBy |Namnet på användaren som stängde aviseringen. Tomt om aviseringen inte har åtgärdats. |
| SourceDisplayName |Visningsnamnet för övervakningsobjektet som skapade aviseringen. |
| SourceFullName |Övervakningsobjektet som skapade aviseringen fullständiga namn. |
| TicketId |Biljett-ID för avisering om System Center Operations Manager-miljö är integrerad med en process för att tilldela biljetter för aviseringar.  Tom av inga biljett ID tilldelas. |
| TimeGenerated |Datum och tid då aviseringen skapades. |
| TimeLastModified |Datum och tid då aviseringen senast ändrades. |
| TimeRaised |Datum och tid då aviseringen genererades. |
| TimeResolved |Datum och tid då aviseringen löstes. Tomt om aviseringen inte har åtgärdats. |

## <a name="sample-log-searches"></a>Exempel på loggsökningar
Följande tabell innehåller exempel på loggsökningar för aviseringsposter som samlas in av den här lösningen: 

| Söka i data | Beskrivning |
|:---|:---|
| Aviseringen &#124; där SourceSystem == ”OpsManager” och AlertSeverity == ”error” och TimeRaised > ago(24h) |Kritiska aviseringar som har genererats under de senaste 24 timmarna |
| Aviseringen &#124; där AlertSeverity == ”varning” och TimeRaised > ago(24h) |Varningsaviseringar som har genererats under de senaste 24 timmarna |
| Aviseringen &#124; där SourceSystem == ”OpsManager” och AlertState! = ”stängd” och TimeRaised > ago(24h) &#124; sammanfatta Count = antal() efter SourceDisplayName |Källor med aktiva aviseringar som genererats under de senaste 24 timmarna |
| Aviseringen &#124; där SourceSystem == ”OpsManager” och AlertSeverity == ”error” och TimeRaised > ago(24h) och AlertState! = ”stängd” |Kritiska aviseringar som genererats under de senaste 24 timmarna som fortfarande är aktiva |
| Aviseringen &#124; där SourceSystem == ”OpsManager” och TimeRaised > ago(24h) och AlertState == ”stängd” |Aviseringar som genererats under de senaste 24 timmarna som nu har stängts |
| Aviseringen &#124; där SourceSystem == ”OpsManager” och TimeRaised > ago(1d) &#124; sammanfatta Count = antal() efter AlertSeverity |Aviseringar som genererats under den senaste 1 dagen grupperat efter allvarlighetsgrad |
| Aviseringen &#124; där SourceSystem == ”OpsManager” och TimeRaised > ago(1d) &#124; sortera efter RepeatCount desc |Aviseringar som har genererats under den senaste 1 dagen sorterat efter upprepat antalsvärde |



## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Aviseringar i Log Analytics](../../azure-monitor/platform/alerts-overview.md) för information om att generera aviseringar från Log Analytics.
