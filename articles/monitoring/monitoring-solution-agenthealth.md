---
title: Agenthälsolösning i Azure | Microsoft Docs
description: Den här artikeln är avsedd att hjälpa dig att förstå hur du använder den här lösningen för att övervaka hälsotillståndet hos dina agenter som rapporterar direkt till Log Analytics eller System Center Operations Manager.
services: operations-management-suite
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: magoedte
ms.openlocfilehash: f0737c6a6ff228b92a030242faf7f4d634bdd9f2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733200"
---
#  <a name="agent-health-solution-in-azure"></a>Agenthälsolösning i Azure
Agenthälsolösningen i Azure hjälper dig att förstå vilka av alla agenter som rapporterar direkt till Log Analytics-arbetsyta eller en System Center Operations Manager-hanteringsgrupp ansluten till Log Analytics, som inte svarar och skicka operativa data.  Du kan också hålla reda på hur många agenter distribueras, var de är fördelade geografiskt och utföra andra frågor för att övervaka distributionen av agenter i Azure, andra miljöer i molnet eller lokalt.    

## <a name="prerequisites"></a>Förutsättningar
Innan du distribuerar den här lösningen kan du bekräfta att du har för närvarande [Windows-agenter](../log-analytics/log-analytics-windows-agent.md) rapporterar till Log Analytics-arbetsytan eller till en [Operations Manager-hanteringsgrupp](../log-analytics/log-analytics-om-agents.md) integrerad med din arbetsyta.    

## <a name="solution-components"></a>Lösningskomponenter
Lösningen består av följande resurser som läggs till i din arbetsyta och ansluter direkt agenter eller Operations Manager-anslutna hanteringsgrupper.

### <a name="management-packs"></a>Hanteringspaket
Om din hanteringsgrupp för System Center Operations Manager är ansluten till en Log Analytics-arbetsyta, installeras följande hanteringspaket i Operations Manager.  Dessa hanteringspaket också har installerats på direktanslutna Windows-datorer när du lägger till den här lösningen. Det finns inget att konfigurera eller hantera med dessa hanteringspaket.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack  (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../log-analytics/log-analytics-om-agents.md).

## <a name="configuration"></a>Konfiguration
Lägga till agenthälsolösningen i Log Analytics-arbetsytan med processen som beskrivs i [lägga till lösningar](../log-analytics/log-analytics-add-solutions.md). Det krävs ingen ytterligare konfiguration.


## <a name="data-collection"></a>Datainsamling
### <a name="supported-agents"></a>Agenter som stöds
I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter | Ja | Pulsslagshändelser som samlas in direkt från Windows-agenter.|
| System Center Operations Manager-hanteringsgrupp | Ja | Pulsslagshändelser som samlas in från agenter som rapporterar till hanteringsgruppen var 60: e sekund och sedan vidarebefordras till logganalys. En direktanslutning från Operations Manager-agenten till Log Analytics krävs inte. Pulsslagshändelsedata vidarebefordras från hanteringsgruppen till Log Analytics-databasen.|

## <a name="using-the-solution"></a>Använda lösningen
När du lägger till lösningen i Log Analytics-arbetsytan i **Agenthälsa** läggs panelen på instrumentpanelen. Den här panelen visar det totala antalet agenter och antalet agenter som inte har svarat under de senaste 24 timmarna.<br><br> ![Panelen för Agenthälsa på instrumentpanelen](./media/monitoring-solution-agenthealth/agenthealth-solution-tile-homepage.png)

Klicka på panelen **Agenthälsa** för att öppna instrumentpanelen för **Agenthälsa**.  Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn visar de översta 10 händelserna med antalet som matchar den kolumnens kriterier för det angivna tidsintervallet. Du kan köra en loggsökning som innehåller hela listan genom att välja **Se alla** längst ned i varje kolumn, eller genom att klicka på kolumnrubriken.

| Kolumn | Beskrivning |
|--------|-------------|
| Agentantal över tid | En trend över agentantal under en period på sju dagar för Windows- och Linux-agenter.|
| Antal agenter som inte svarar | En lista över agenter som inte har skickat ett pulsslag under de senaste 24 timmarna.|
| Distribution enligt OS-typ | En partition av hur många Windows och Linux-agenter du har i din miljö.|
| Distribution enligt Agent-version | En partition av olika agentversioner som installerats i din miljö och antalet av vardera.|
| Distribution enligt Agent-kategori | En partition av olika typer av agenter som skickar händelser för pulsslag: direkta agenter, OpsMgr-agenter eller OpsMgr Management Server.|
| Distribution enligt hanteringsgrupp | En partition av olika SCOM-hanteringsgrupper i din miljö.|
| Geoplats för agenter | En partition av olika länder där du har agenter och det sammanlagda antalet agenter som har installerats i varje land.|
| Antalet installerade Gateways | Antalet servrar som har installerat gatewayen för Log Analytics och en lista över dessa servrar.|

![Agenthälsa på instrumentpanelen - exempel](./media/monitoring-solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Log Analytics-poster
Lösningen skapar en typ av post i Log Analytics-arbetsytan.  

### <a name="heartbeat-records"></a>Pulsslagsposter
En post med en typ av **pulsslag** skapas.  Dessa poster har egenskaper enligt följande tabell.  

| Egenskap  | Beskrivning |
| --- | --- |
| Typ | *Pulsslag*|
| Kategori | Värdet är *Direct Agent*, *SCOM Agent* eller *SCOM Management Server*.|
| Dator | Datornamn.|
| OSType | Windows- eller Linux-operativsystem.|
| OSMajorVersion | Högre operativsystemversion.|
| OSMinorVersion | Lägre operativsystemversion.|
| Version | Log Analytics-agenten eller Operations Manager Agent-version.|
| SCAgentChannel | Värdet är *Direkt* och/eller *SCManagementServer*.|
| IsGatewayInstalled | Om Log Analytics Gateway har installerats är värdet *SANT*, annars är värdet *FALSKT*.|
| ComputerIP | IP-adressen för datorn.|
| RemoteIPCountry | Geografisk plats där datorn har distribuerats.|
| ManagementGroupName | Namn på Operations Manager-hanteringsgrupp.|
| SourceComputerId | Unikt ID för datorn.|
| RemoteIPLongitude | Longituden för datorns geografiska plats.|
| RemoteIPLatitude | Latituden för datorns geografiska plats.|

Varje agent som rapporterar till en hanteringsserver för Operations Manager skickar två pulsslag och egenskapsvärdet för SCAgentChannel innehåller både **direkt** och **SCManagementServer** beroende på vilka Log Analytics-datakällor och lösningar som du har aktiverat i din prenumeration. Om du kommer ihåg data från lösningar antingen skickas direkt från en hanteringsserver för Operations Manager till Log Analytics eller på grund av mängden data som samlas in från agenten, skickas direkt från agenten till Log Analytics. För pulsslaghändelser som har värdet **SCManagementServer**, är ComputerIP-värdet IP-adressen för hanteringsservern eftersom data faktiskt har laddats upp av den.  För pulsslag där SCAgentChannel är inställt på **Direkt** är det den offentliga IP-adressen för agenten.  

## <a name="sample-log-searches"></a>Exempel på loggsökningar
Följande tabell innehåller exempel på sökningar i loggen för poster som har samlats in av den här lösningen.

| Fråga | Beskrivning |
|:---|:---|
| Heartbeat &#124; distinct Computer |Totalt antal agenter |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Antal agenter som inte har svarat de senaste 24 timmarna |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Antal agenter som inte har svarat de senaste 15 minuterna |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Datorer online (under de senaste 24 timmarna) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Totalt antal agenter som har varit offline de senaste 30 minuterna (för de senaste 24 timmarna) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Hämta en trend över antalet agenter över tid enligt OSType|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribution enligt OS-typ |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribution enligt Agent-version |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribution enligt Agent-kategori |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribution enligt hanteringsgrupp |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geoplats för agenter |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Antalet installerade Log Analytics-Gateways |




## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Aviseringar i Log Analytics](../log-analytics/log-analytics-alerts.md) för information om att generera aviseringar från Log Analytics.
