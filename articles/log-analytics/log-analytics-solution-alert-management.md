---
title: "Varna hanteringslösning i Operations Management Suite (OMS) | Microsoft Docs"
description: "Aviseringen hanteringslösning i logganalys hjälper dig att analysera alla aviseringar i din miljö.  Förutom att konsolidera aviseringar som genereras inom OMS importerar det aviseringar från anslutna hanteringsgrupper för System Center Operations Manager till logganalys."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: bwren
ms.openlocfilehash: 4ec80fccdf4521792ff6be115ec66227f0fe1ed2
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="alert-management-solution-in-operations-management-suite-oms"></a>Lösning för avisering i Operations Management Suite (OMS)

![Varningsikon för hantering](media/log-analytics-solution-alert-management/icon.png)

Alert Management-lösningen hjälper dig att analysera alla aviseringar i logganalys-databasen.  Dessa aviseringar kan komma från olika källor, inklusive dessa källor [skapas av logganalys](log-analytics-alerts.md) eller [importeras från Nagios eller Zabbix](log-analytics-linux-agents.md).  Lösningen också importerar aviseringar från alla [anslutna hanteringsgrupper för System Center Operations Manager](log-analytics-om-agents.md).

## <a name="prerequisites"></a>Krav
Lösningen fungerar med alla poster i logganalys-databasen med en typ av **avisering**, så du måste utföra konfigurationen som krävs för att samla in dessa poster.

- För Log Analytics aviseringar [skapa Varningsregler](log-analytics-alerts.md) att skapa aviseringen poster direkt i databasen.
- För Nagios och Zabbix aviseringar [Konfigurera servrarna](log-analytics-linux-agents.md) att skicka aviseringar till logganalys.
- För System Center Operations Manager-aviseringar [Anslut din Operations Manager-hanteringsgrupp till logganalys-arbetsytan](log-analytics-om-agents.md).  Alla aviseringar som skapats i System Center Operations Manager importeras till logganalys.  

## <a name="configuration"></a>Konfiguration
Lägg till avisering hanteringslösningen i OMS-arbetsytan med processen som beskrivs i [lägga till lösningar](log-analytics-add-solutions.md).  Det krävs ingen ytterligare konfiguration.

## <a name="management-packs"></a>Hanteringspaket
Om din hanteringsgrupp för System Center Operations Manager är ansluten till din OMS-arbetsyta, installeras följande hanteringspaket i System Center Operations Manager när du lägger till den här lösningen.  Det finns ingen konfiguration eller underhåll av de hanteringspaket som krävs.  

* Microsoft System Center Advisor Alert Management (Microsoft.IntelligencePacks.AlertManagement)

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](log-analytics-om-agents.md).

## <a name="data-collection"></a>Datainsamling
### <a name="agents"></a>Agenter
I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Support | Beskrivning |
|:--- |:--- |:--- |
| [Windows-agenter](log-analytics-windows-agent.md) | Nej |Direkt Windows-agenter genererar inte aviseringar.  Log Analytics aviseringar kan skapas från händelserna och prestandadata som samlats in från Windows agenter. |
| [Linux-agenter](log-analytics-linux-agents.md) | Nej |Direkt Linux-agenter genererar inte aviseringar.  Log Analytics aviseringar kan skapas från händelserna och prestandadata som samlats in från Linux-agenter.  Nagios och Zabbix aviseringar som samlas in från dessa servrar som kräver Linux-agenten. |
| [System Center Operations Manager-hanteringsgruppen](log-analytics-om-agents.md) |Ja |Aviseringar som genereras på Operations Manager-agenter levereras till hanteringsgruppen och sedan vidarebefordras till logganalys.<br><br>En direkt anslutning från Operations Manager-agenter till logganalys krävs inte. Aviseringsdata vidarebefordras från hanteringsgruppen i logganalys-databasen. |


### <a name="collection-frequency"></a>Insamlingsfrekvens
- Aviseringen poster är tillgängliga för lösningen som lagrats i databasen.
- Aviseringen data skickas från Operations Manager-hanteringsgrupp till logganalys tre minuters mellanrum.  

## <a name="using-the-solution"></a>Använda lösningen
När du lägger till avisering hanteringslösningen OMS-arbetsytan i **Alert Management** panel har lagts till OMS-instrumentpanelen.  Den här panelen visar antalet och grafisk representation av antalet aktiva aviseringar som genererades under de senaste 24 timmarna.  Du kan inte ändra den här tidsintervall.

![Alert Management sida vid sida](media/log-analytics-solution-alert-management/tile.png)

Klicka på den **Aviseringshanteringen** öppna den **Alert Management** instrumentpanelen.  Instrumentpanelen innehåller kolumnerna i följande tabell.  Varje kolumnen visas de översta 10 aviseringarna med antalet som matchar den kolumnen villkoren för angivet omfång och tidsintervall.  Du kan köra en sökning i logg som innehåller hela listan genom att klicka på **se alla** längst ned i kolumnen eller genom att klicka på kolumnrubriken.

| Kolumn | Beskrivning |
|:--- |:--- |
| Kritiska aviseringar |Alla aviseringar med en allvarlighetsgraden kritiskt grupperade efter namn på avisering.  Klicka på ett namn på avisering att köra en logg sökning returnera alla poster för den här aviseringen. |
| Varningsaviseringar |Alla aviseringar med en allvarlighetsgrad för varning grupperade efter namn på avisering.  Klicka på ett namn på avisering att köra en logg sökning returnera alla poster för den här aviseringen. |
| Aktiva SCOM aviseringar |Alla aviseringar som samlas in från Operations Manager med några tillstånd än *stängd* grupperade efter källa som skapade aviseringen. |
| Alla aktiva aviseringar |Alla varningar med några allvarlighetsgrad grupperade efter namn på avisering. Endast innehåller Operations Manager-varningar med några tillstånd än *stängd*. |

Om du bläddrar till höger på instrumentpanelen visar några vanliga frågor som du kan klicka på för att utföra en [loggen Sök](log-analytics-log-searches.md) för aviseringsdata.

![Hanteringspanel för avisering](media/log-analytics-solution-alert-management/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-poster
Aviseringen hanteringslösningen analyserar en post med en typ av **avisering**.  Aviseringar som skapats av logganalys eller samlas in från Nagios eller Zabbix samlas inte direkt av lösningen.

Lösningen går att importera aviseringar från System Center Operations Manager och skapar en post för var och en med en typ av **avisering** och en SourceSystem av **OpsManager**.  Dessa poster har egenskaper i följande tabell:  

| Egenskap | Beskrivning |
|:--- |:--- |
| Typ |*Varning* |
| SourceSystem |*OpsManager* |
| AlertContext |Information om dataobjekt som orsakade aviseringen ska genereras i XML-format. |
| AlertDescription |Detaljerad beskrivning av aviseringen. |
| AlertId |GUID för aviseringen. |
| AlertName |Namnet på aviseringen. |
| AlertPriority |Prioritetsnivån för aviseringen. |
| AlertSeverity |Allvarlighetsgrad för aviseringen. |
| In AlertState |Senaste lösningstillståndet för aviseringen. |
| LastModifiedBy |Namn på den användare som senast ändrade aviseringen. |
| ManagementGroupName |Namnet på hanteringsgruppen där aviseringen genererades. |
| RepeatCount |Antal gånger som samma avisering har genererats för samma övervakade objekt eftersom som matchas. |
| ResolvedBy |Namnet på användaren som stängde aviseringen. Tomt om aviseringen inte ännu har lösts. |
| SourceDisplayName |Visningsnamnet för övervakningsobjektet som skapade aviseringen. |
| SourceFullName |Övervakningsobjektet som skapade aviseringen fullständiga namn. |
| TicketId |Biljett-ID för avisering om System Center Operations Manager-miljö är integrerad med en process för att tilldela biljetter för aviseringar.  Tom av inga tjänstbiljett ID tilldelas. |
| TimeGenerated |Datum och tid då aviseringen skapades. |
| TimeLastModified |Datum och tid då aviseringen senast ändrades. |
| TimeRaised |Datum och tid då aviseringen genererades. |
| TimeResolved |Datum och tid då aviseringen har lösts. Tomt om aviseringen inte ännu har lösts. |

## <a name="sample-log-searches"></a>Exempel på loggsökningar
Följande tabell innehåller exempel loggen söker efter avisering innehåller information som samlas in av den här lösningen: 

| Fråga | Beskrivning |
|:--- |:--- |
| Typ = avisering SourceSystem = OpsManager AlertSeverity = fel TimeRaised > nu 24 timmar |Kritiska aviseringar som genererats under de senaste 24 timmarna |
| Typ = avisering AlertSeverity = varning TimeRaised > nu 24 timmar |Varningsaviseringar som genererats under de senaste 24 timmarna |
| Typ = avisering SourceSystem = OpsManager in AlertState! = stängd TimeRaised > nu 24 TIMMARS &#124; måttet count() som antal av SourceDisplayName |Källor med aktiva aviseringar som genererats under de senaste 24 timmarna |
| Typ = avisering SourceSystem = OpsManager AlertSeverity = fel TimeRaised > nu 24 TIMMARS in AlertState! = stängd |Kritiska aviseringar som genererats under de senaste 24 timmarna som fortfarande är aktiva |
| Typ = avisering SourceSystem = OpsManager TimeRaised > nu 24 TIMMARS in AlertState = stängd |Aviseringar som genererats under de senaste 24 timmarna som nu har stängts |
| Typ = avisering SourceSystem = OpsManager TimeRaised > nu 1 dag &#124; måttet count() som antal av AlertSeverity |Aviseringar som genererats under de senaste 1 dagen grupperat efter allvarlighetsgrad |
| Typ = avisering SourceSystem = OpsManager TimeRaised > nu 1 dag &#124; Sortera RepeatCount desc |Aviseringar som genererats under de senaste 1 dagen sorterat efter upprepat antalsvärde |


>[!NOTE]
> Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), sedan föregående frågorna skulle ändra till följande:
>
>| Fråga | Beskrivning |
|:---|:---|
| Varna n &#124; där SourceSystem == ”OpsManager” och AlertSeverity == ”error” och TimeRaised > ago(24h) |Kritiska aviseringar som genererats under de senaste 24 timmarna |
| Varna n &#124; där AlertSeverity == ”varning” och TimeRaised > ago(24h) |Varningsaviseringar som genererats under de senaste 24 timmarna |
| Varna n &#124; där SourceSystem == ”OpsManager” och in AlertState! = ”stängd” och TimeRaised > ago(24h) &#124; Sammanfatta Count = count() av SourceDisplayName |Källor med aktiva aviseringar som genererats under de senaste 24 timmarna |
| Varna n &#124; där SourceSystem == ”OpsManager” och AlertSeverity == ”error” och TimeRaised > ago(24h) och in AlertState! = ”stängd” |Kritiska aviseringar som genererats under de senaste 24 timmarna som fortfarande är aktiva |
| Varna n &#124; där SourceSystem == ”OpsManager” och TimeRaised > ago(24h) och in AlertState == ”stängd” |Aviseringar som genererats under de senaste 24 timmarna som nu har stängts |
| Varna n &#124; där SourceSystem == ”OpsManager” och TimeRaised > ago(1d) &#124; Sammanfatta Count = count() av AlertSeverity |Aviseringar som genererats under de senaste 1 dagen grupperat efter allvarlighetsgrad |
| Varna n &#124; där SourceSystem == ”OpsManager” och TimeRaised > ago(1d) &#124; Sortera efter RepeatCount desc |Aviseringar som genererats under de senaste 1 dagen sorterat efter upprepat antalsvärde |


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Aviseringar i Log Analytics](log-analytics-alerts.md) för information om att generera aviseringar från Log Analytics.
