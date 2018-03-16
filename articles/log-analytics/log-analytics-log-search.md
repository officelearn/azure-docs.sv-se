---
title: "Logga sökningar i Azure Log Analytics | Microsoft Docs"
description: "Du behöver en logg-sökning för att hämta data från logganalys.  Den här artikeln beskriver hur ny logg sökningar används i logganalys och innehåller begrepp som du behöver förstå innan du skapar en."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 5f040d1480433ccf4c0b2b22c0cf1e25a7151d74
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="understanding-log-searches-in-log-analytics"></a>Förstå loggen söker i logganalys

Du behöver en logg-sökning för att hämta data från logganalys.  Om du analyserar data i portalen, vill konfigurera en aviseringsregel som ska meddelas om ett visst villkor eller hämta data med hjälp av API: et för Log Analytics du använda en logg sökning för att ange vilka data som du vill använda.  Den här artikeln beskriver hur du använder loggen sökningar i logganalys samt begrepp som du bör känna till innan du skapar en. Finns det [nästa steg](#next-steps) avsnittet för information om att skapa och redigera loggen sökningar och referenser på frågespråket.

## <a name="where-log-searches-are-used"></a>Där loggen sökningar används

Olika sätt som du ska använda loggen sökningar i logganalys inkluderar följande:

- **Portals.** Du kan utföra interaktiv analys av data i databasen i Azure-portalen eller [Advanced Analytics portal](https://go.microsoft.com/fwlink/?linkid=856587).  På så sätt kan du redigera din fråga och analysera resultaten i en mängd olika format och visualiseringar.  De flesta frågor som du skapar kommer att starta i någon av portalerna och kopieras sedan när du kontrollera att den fungerar som förväntat.
- **Varningsregler.** [Varna regler](log-analytics-alerts.md) proaktivt identifiera problem utifrån data i din arbetsyta.  Varje regel för varning baseras på en logg sökning som körs automatiskt med jämna mellanrum.  Resultatet är kontrolleras för att avgöra om en avisering ska skapas.
- **Vyer.**  Du kan skapa visualiseringar av data som ska inkluderas i instrumentpaneler för användare med [Vydesigner](log-analytics-view-designer.md).  Loggen sökningar tillhandahåller de data som används av [paneler](log-analytics-view-designer-tiles.md) och [visualiseringen delar](log-analytics-view-designer-parts.md) i varje vy.  Du kan öka detaljnivån från visualiseringen delar i loggen söksidan att utföra analyser på data.
- **Exportera.**  När du exporterar data från logganalys-arbetsytan till Excel eller [Power BI](log-analytics-powerbi.md), du skapar en logg-sökning för att definiera data som ska exporteras.
- **PowerShell.** Du kan köra ett PowerShell-skript från en kommandorad eller ett Azure Automation-runbook som använder [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) att hämta data från logganalys.  Den här cmdleten kräver en fråga för att fastställa vilka data som ska hämtas.
- **Log Analytics-API.**  Den [logganalys logga Sök API](log-analytics-log-search-api.md) tillåter alla REST API-klient att hämta data från arbetsytan.  API-begäran innehåller en fråga som körs mot Log Analytics för att fastställa data som ska hämtas.

![Log-sökningar](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Hur ordnas Log Analytics-data
När du skapar en fråga kan starta du genom att bestämma vilka tabeller innehåller de data som du letar efter. Varje [datakällan](log-analytics-data-sources.md) och [lösning](../operations-management-suite/operations-management-suite-solutions.md) lagrar data i dedikerade tabeller i logganalys-arbetsytan.  Dokumentation för varje datakälla och lösningen innehåller namnet på den datatyp som skapas och en beskrivning av var och en av dess egenskaper.  Många frågor kräver endast data från en enda tabeller, men andra kan använda en mängd olika alternativ för att ta med data från flera tabeller.

![Tabeller](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>Skriva en fråga
Kärnan i loggen söker i logganalys är [ett omfattande frågespråket](https://docs.loganalytics.io/) som låter dig hämta och analysera data från databasen på en mängd olika sätt.  Samma fråga språk används för [Programinsikter](../application-insights/app-insights-analytics.md).  Lär dig att skriva en fråga är viktigt att skapa loggfilen sökningar i logganalys.  Du normalt börjar med grundläggande frågor och sedan gå vidare om du vill använda mer avancerade funktioner enligt dina krav blir mer komplexa.

Den grundläggande strukturen i en fråga är en källtabell följt av en serie operatörer avgränsade med ett vertikalstreck `|`.  Du kan kedja samman flera operatorer för att begränsa data och utföra avancerade funktioner.

Anta exempelvis att du vill hitta de övre tio datorerna med de flesta felhändelser under den senaste dagen.

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

Eller så kanske du vill söka efter datorer som inte har haft ett pulsslag i den sista dagen.

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

Hur är det med linjediagram med processoranvändning för varje dator från förra veckan?

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

Du kan se dessa snabb prover att oavsett vilken typ av data som du arbetar med strukturen i frågan är liknande.  Du kan dela upp det i steg där den resulterande data från ett kommando skickas via pipeline till nästa kommando.

Du kan också fråga efter data över logganalys arbetsytor i din prenumeration.

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 


Fullständig dokumentation för Azure Log Analytics-frågespråket i inklusive självstudier och Språkreferens finns i [Azure logganalys fråga språk dokumentationen](https://docs.loganalytics.io/).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [portaler som används för att skapa och redigera logga sökningar](log-analytics-log-search-portals.md).
- Checka ut en [självstudier om hur du skriver frågor](log-analytics-tutorial-viewdata.md) med det nya språket i fråga.
