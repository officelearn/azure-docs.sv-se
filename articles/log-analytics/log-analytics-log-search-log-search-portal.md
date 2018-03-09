---
title: "Med hjälp av loggen Sök-portal i Azure Log Analytics | Microsoft Docs"
description: "Den här artikeln innehåller en genomgång som beskriver hur du skapar loggen sökningar och analysera data som lagras i logganalys-arbetsytan med hjälp av loggen Sök-portalen.  Självstudien innehåller körning av några enkla frågor för att returnera olika typer av data och för att analysera resultatet."
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
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 37213012e817f0fae21a47a4334a519bbbca206b
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Skapa loggen sökningar i Azure Log Analytics med hjälp av loggen Sök-portalen

> [!NOTE]
> Den här artikeln beskriver loggen Sök-portal i Azure Log Analytics med hjälp av det nya språket i fråga.  Du lär dig mer om det nya språket och få proceduren för att uppgradera din arbetsyta på [uppgradera Azure logganalys-arbetsytan till ny logg sökning](log-analytics-log-search-upgrade.md).  
>
> Om ditt arbetsområde inte har uppgraderats till det nya språket i fråga, bör du gå till [söka efter data med hjälp av loggen sökningar i logganalys](log-analytics-log-searches.md) information om den aktuella versionen av loggen Sök-portalen.

Den här artikeln innehåller en genomgång som beskriver hur du skapar loggen sökningar och analysera data som lagras i logganalys-arbetsytan med hjälp av loggen Sök-portalen.  Självstudien innehåller körning av några enkla frågor för att returnera olika typer av data och för att analysera resultatet.  Den fokuserar på funktioner i loggen Sök portal för att ändra frågan i stället för att ändra direkt.  Mer information om hur du redigerar frågan direkt finns det [Query Language referens](https://go.microsoft.com/fwlink/?linkid=856079).

För att skapa sökningar i Advanced Analytics-portalen i stället för att logga Sök-portalen, se [komma igång med Analytics-portalen](https://go.microsoft.com/fwlink/?linkid=856587).  Båda portaler använda samma frågespråket tillgång till samma data i logganalys-arbetsytan.

## <a name="prerequisites"></a>Förutsättningar
Den här kursen förutsätter att du redan har en logganalys-arbetsytan med minst en ansluten datakälla som genererar data för frågor för att analysera.  

- Om du inte har en arbetsyta, du kan skapa en kostnadsfri med hjälp av proceduren på [komma igång med en logganalys-arbetsytan](log-analytics-get-started.md).
- Ansluta minst ett [Windows-agenten](log-analytics-windows-agent.md) eller en [Linux-agenten](log-analytics-linux-agents.md) till arbetsytan.  

## <a name="open-the-log-search-portal"></a>Öppna loggsökningsportalen
Börja med att öppna loggsökningsportalen. 

1. Öppna Azure Portal.
2. Navigera till logganalys och markera arbetsytan.
3. Välj **logga Sök**.

![Logga sökknappen](media/log-analytics-log-search-log-search-portal/log-search-button.png)

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

![Exempelfråga](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

Endast de första egenskaperna för varje post visas.  Klicka på alternativet för att **visa fler** om du vill visa alla egenskaper för en viss post.

![Registrera information](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>Ange omfånget tid
Alla poster som samlas in av logganalys har en **TimeGenerated** egenskap som innehåller datum och tid då posten skapades.  En fråga i loggen Sök portal returnerar bara poster med en **TimeGenerated** omfattas tid som visas på vänster sida av skärmen.  

Du kan ändra tidsfiltret genom att välja i listrutan eller genom att ändra skjutreglaget.  Skjutreglaget visar ett stapeldiagram som visar relativa antalet poster för varje gång segment inom intervallet.  Det här segmentet varierar beroende på intervallet.

Standard tid omfånget är **1 dag**.  Ändra värdet till **7 dagar**, och det totala antalet poster ska öka.

![Datum tid omfång](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>Filtrera frågans resultat
På vänster sida på skärmen finns filtreringsfönstret där du kan lägga till filtrering till frågan utan att ändra den direkt.  Med de tio främsta värdena med antal poster visas flera egenskaper poster returneras.

Om du arbetar med **Händelse** markerar du kryssrutan bredvid **Fel** under **EVENTLEVELNAME**.   Om du arbetar med **Syslog** markerar du kryssrutan bredvid **Fel** under **SEVERITYLEVEL**.  Det ändrar frågan till något av följande för att begränsa resultatet för felhändelserna.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filter](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

Lägg till egenskaper till filtreringsfönstret genom att välja  **	Lägg till i filter** på egenskapsmenyn för någon av posterna.

![Menyn Lägg till i filter](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

Du kan ställa in samma filter genom att välja **Filter** på egenskapsmenyn för en post med det värdet du vill filtrera.  

Du har bara den **Filter** alternativ för egenskaper med deras namn i blått.  Dessa är *sökbara* fält som indexeras för sökvillkor.  Fält i grått är *fritextsökbara* fält som endast har alternativet **Visa referenser**.  Det här alternativet returnerar poster som har det värdet i en egenskap.

![Filter-menyn](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

Du kan gruppera resultatet för en enda egenskap genom att välja alternativet **Gruppera efter** på postmenyn.  Då läggs en [sammanfattningsoperator](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) till för frågan som visar resultatet i ett diagram.  Du kan gruppera efter mer än en egenskap, men då måste du redigera frågan direkt.  Välj menyn post bredvid den **datorn** egenskapen och välj **Gruppera efter ”dator”**.  

![Gruppera efter dator](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>Arbeta med resultat
Loggsökningsportalen har en mängd funktioner för att arbeta med en frågas resultat.  Du kan sortera, filtrera och gruppera resultat för att analysera data utan att ändra den faktiska frågan.  En frågas resultat sorteras inte som standard.

Om du vill visa data i tabellform, vilket ger ytterligare alternativ för filtrering och sortering, klickar du på **Tabell**.  

![Tabellvy](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

Klicka på pilen vid en post för att visa information för den posten.

![Sortera resultat](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

Sortera på ett fält genom att klicka på dess kolumnrubrik.

![Sortera resultat](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

Filtrera resultatet för ett specifikt värde i kolumnen genom att klicka på filtreringsknappen och ange ett filtreringsvillkor.

![Filtrera resultat](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

Gruppera efter en kolumn genom att dra dess kolumnrubrik överst bland resultaten.  Du kan gruppera på flera fält genom att dra flera kolumner på sidan.

![Gruppera resultat](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>Arbeta med prestandadata
Prestandadata för både Windows- och Linux-agenter lagras i Log Analytics-arbetsytan i tabellen **Perf**.  Uppgifter som ser ut precis som andra poster och vi kan skriva en enkel fråga som returnerar alla uppgifter på samma sätt som med händelser.

```
Perf
```

![Prestandadata](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

Det är dock inte särskilt användbart att returnera miljontals poster för alla prestandaobjekt och räknare.  Du kan använda samma metoder som du använde ovan för att filtrera data eller så skriver du följande fråga direkt i loggsökningsrutan.  Det returnerar enbart processoranvändningsposter för både Windows- och Linux-datorer.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Processoranvändning](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

Det begränsar data till en särskild räknare, men det placerar dem fortfarande inte i ett formulär som är särskilt användbart.  Du kan visa data i ett linjediagram, men först måste du gruppera dem via Dator och TimeGenerated.  Om du vill gruppera flera fält måste du ändra frågan direkt, så ändra frågan så här.  Funktionen [medel](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) för egenskapen **CounterValue** används för att beräkna medelvärdet för varje timme.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Diagram över prestandadata](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

Nu när dina data är lämpligt grupperade kan du visa dem i ett visuellt diagram genom att lägga till operatorn [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator).  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Linjediagram](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om Log Analytics-frågespråket i [komma igång med Analytics-portalen](https://go.microsoft.com/fwlink/?linkid=856079).
- Gå igenom en självstudiekurs om att använda den [Advanced Analytics portal](https://go.microsoft.com/fwlink/?linkid=856587) där du kan köra samma frågor och få åtkomst till samma data som loggen Sök-portalen.
