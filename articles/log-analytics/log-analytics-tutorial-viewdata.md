---
title: Visa eller analysera insamlade Azure Log Analytics-data | Microsoft Docs
description: Den här artikeln innehåller en självstudie som beskriver hur du skapar loggsökningar och analyserar data som lagrats i din Log Analytics-resurs med Log Search-portalen.  Självstudien innehåller körning av några enkla frågor för att returnera olika typer av data och för att analysera resultatet.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/26/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: fc5dcc945750b4ab4eef337dbd96bd051bb4dd81
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="view-or-analyze-data-collected-with-log-analytics-log-search"></a>Visa eller analysera data som samlats in med Log Analytics-loggsökning

I Log Analytics kan du använda loggsökningar genom att konstruera frågor för att analysera insamlade data och använda befintliga instrumentpaneler som du kan anpassa med grafiska vyer över dina mest värdefulla sökningar.  Nu när du har definierat en samling av driftdata från dina virtuella Azure-datorer och aktivitetsloggar får du lära dig i den här självstudien att:

> [!div class="checklist"]
> * Uppgradera Azure Log Analytics-resursen till det nya frågespråket 
> * Gör en enkel sökning av händelsedata och använd funktioner för att ändra och filtrera resultatet 
> * Lär dig att arbeta med prestandadata

Du måste ha en befintlig virtuell dator [som är ansluten till Log Analytics-arbetsytan](log-analytics-quick-collect-azurevm.md) för att kunna utföra exemplet i självstudien.  

Du kan skapa och redigera frågor på två olika sätt utöver att arbeta interaktivt med returnerade data.  För grundläggande frågor använder du loggsökningssidan i Azure-portalen, och för avancerade frågor kan du använda portalen Avancerad analys. Om du vill veta mer om funktionsskillnaderna mellan de två portalerna kan du läsa informationen om [portaler för att skapa och redigera loggfrågor i Azure Log Analytics](log-analytics-log-search-portals.md)

I den här självstudien kommer vi att arbeta med loggsökning i Azure-portalen. 

## <a name="log-in-to-azure-portal"></a>Logga in på Azure Portal
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="open-the-log-search-portal"></a>Öppna loggsökningsportalen 
Börja med att öppna loggsökningsportalen.   

1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. I prenumerationsfönstret för Log Analytics väljer du en arbetsyta och sedan panelen **Loggsökning**.<br> ![Loggsökningsknapp](media/log-analytics-tutorial-viewdata/azure-portal-01.png)

Du kanske ser en banderoll överst på resurssidan för Log Analytics i portalen som uppmanar dig att uppgradera.<br> ![Meddelande om uppgradering av Log Analytics i Azure Portal](media/log-analytics-tutorial-viewdata/log-analytics-portal-upgradebanner.png)

Log Analytics introducerade nyligen ett nytt frågespråk för att göra det enklare att skapa frågor, korrelera data från olika källor och analysera för att snabbt identifiera trender eller problem.

Det är enkelt att uppgradera.  Starta processen genom att klicka på banderollen där det står **Lär dig mer och uppgradera**.  Läs igenom informationen om uppgraderingen på uppgraderingsinformationssidan och klicka sedan på **Uppgradera nu**.

Den här processen tar några minuter att slutföra och du kan under tiden spåra förloppet under **Meddelanden** på menyn. Du kan lära dig mer om [fördelarna med det nya frågespråket](log-analytics-log-search-upgrade.md#why-the-new-language).

## <a name="create-a-simple-search"></a>Skapa en enkel sökning
Det snabbaste sättet att hämta vissa data att arbeta med är en enkel fråga som returnerar alla poster i en tabell.  Om du har några Windows- eller Linux-klienter anslutna till din arbetsyta har du data i antingen tabellen Händelse (Windows) eller Syslog (Linux).

Skriv någon av följande frågor i sökrutan och klicka på sökknappen.  

```
Event
```
```
Syslog
```

Data returneras i standardlistvyn och du kan se hur många poster som returnerades totalt.

![Exempelfråga](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-01.png)

Endast de första egenskaperna för varje post visas.  Klicka på alternativet för att **visa fler** om du vill visa alla egenskaper för en viss post.

## <a name="filter-results-of-the-query"></a>Filtrera frågans resultat
På vänster sida på skärmen finns filtreringsfönstret där du kan lägga till filtrering till frågan utan att ändra den direkt.  Flera postegenskaper visas för den posttypen, och du kan välja ett eller flera egenskapsvärden för att begränsa sökresultatet.

Om du arbetar med **Händelse** markerar du kryssrutan bredvid **Fel** under **EVENTLEVELNAME**.   Om du arbetar med **Syslog** markerar du kryssrutan bredvid **Fel** under **SEVERITYLEVEL**.  Det ändrar frågan till något av följande för att begränsa resultatet för felhändelserna.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filter](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-02.png)

Lägg till egenskaper till filtreringsfönstret genom att välja **Lägg till i filter** på egenskapsmenyn för någon av posterna.

![Menyn Lägg till i filter](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-03.png)

Du kan ställa in samma filter genom att välja **Filter** på egenskapsmenyn för en post med det värdet du vill filtrera.  

Du ser bara alternativet **Filter** för egenskaper med namnet i blått när du hovrar över dem.  Dessa är *sökbara* fält som indexeras för sökvillkor.  Fält i grått är *fritextsökbara* fält som endast har alternativet **Visa referenser**.  Det här alternativet returnerar poster som har det värdet i en egenskap.

Du kan gruppera resultatet för en enda egenskap genom att välja alternativet **Gruppera efter** på postmenyn.  Då läggs en [sammanfattningsoperator](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) till för frågan som visar resultatet i ett diagram.  Du kan gruppera efter mer än en egenskap, men då måste du redigera frågan direkt.  Välj postmenyn bredvid egenskapen **Dator** och välj **Gruppera efter "dator"**.  

![Gruppera efter dator](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-04.png)

## <a name="work-with-results"></a>Arbeta med resultat
Loggsökningsportalen har en mängd funktioner för att arbeta med en frågas resultat.  Du kan sortera, filtrera och gruppera resultat för att analysera data utan att ändra den faktiska frågan.  En frågas resultat sorteras inte som standard.

Om du vill visa data i tabellform, vilket ger ytterligare alternativ för filtrering och sortering, klickar du på **Tabell**.  

![Tabellvy](media/log-analytics-tutorial-viewdata/log-search-portal-table-01.png)

Klicka på pilen vid en post för att visa information för den posten.

![Sortera resultat](media/log-analytics-tutorial-viewdata/log-search-portal-table-02.png)

Sortera på ett fält genom att klicka på dess kolumnrubrik.

![Sortera resultat](media/log-analytics-tutorial-viewdata/log-search-portal-table-03.png)

Filtrera resultatet för ett specifikt värde i kolumnen genom att klicka på filtreringsknappen och ange ett filtreringsvillkor.

![Filtrera resultat](media/log-analytics-tutorial-viewdata/log-search-portal-table-04.png)

Gruppera efter en kolumn genom att dra dess kolumnrubrik överst bland resultaten.  Du kan gruppera på flera fält genom att dra flera kolumner på sidan.

![Gruppera resultat](media/log-analytics-tutorial-viewdata/log-search-portal-table-05.png)


## <a name="work-with-performance-data"></a>Arbeta med prestandadata
Prestandadata för både Windows- och Linux-agenter lagras i Log Analytics-arbetsytan i tabellen **Perf**.  Prestandaposter ser precis ut som andra poster, och vi ska skriva en enkel fråga som returnerar alla prestandaposter precis som med händelser.

```
Perf
```

![Prestandadata](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-01.png)

Det är dock inte särskilt användbart att returnera miljontals poster för alla prestandaobjekt och räknare.  Du kan använda samma metoder som du använde ovan för att filtrera data eller så skriver du följande fråga direkt i loggsökningsrutan.  Det returnerar enbart processoranvändningsposter för både Windows- och Linux-datorer.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Processoranvändning](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-02.png)

Det begränsar data till en särskild räknare, men det placerar dem fortfarande inte i ett formulär som är särskilt användbart.  Du kan visa data i ett linjediagram, men först måste du gruppera dem via Dator och TimeGenerated.  Om du vill gruppera flera fält måste du ändra frågan direkt, så ändra frågan så här.  Funktionen [medel](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) för egenskapen **CounterValue** används för att beräkna medelvärdet för varje timme.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Diagram över prestandadata](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-03.png)

Nu när dina data är lämpligt grupperade kan du visa dem i ett visuellt diagram genom att lägga till operatorn [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator).  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Linjediagram](media/log-analytics-tutorial-viewdata/log-analytics-portal-linechart-01.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig at skapa grundläggande loggsökningar för att analysera händelse- och prestandadata.  Fortsätt till nästa självstudie för att lära dig att visualisera data genom att skapa en instrumentpanel.

> [!div class="nextstepaction"]
> [Skapa och dela Log Analytics-instrumentpaneler](log-analytics-tutorial-dashboards.md)