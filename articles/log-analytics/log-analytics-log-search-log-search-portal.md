---
title: "Med hjälp av loggen Sök-portal i Azure Log Analytics | Microsoft Docs"
description: "Den här artikeln innehåller en genomgång som beskriver hur du skapar loggen sökningar och analysera data som lagras i logganalys-arbetsytan med hjälp av loggen Sök-portalen.  Vägledningen innehåller köra några enkla frågor för att returnera olika typer av data och när resultat."
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
ms.date: 08/23/2017
ms.author: bwren
ms.openlocfilehash: 6fc556ceb34cde26d5f3789a2397cdaa34b0b84d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Skapa loggen sökningar i Azure Log Analytics med hjälp av loggen Sök-portalen

> [!NOTE]
> Den här artikeln beskriver loggen Sök-portal i Azure Log Analytics med hjälp av det nya språket i fråga.  Du lär dig mer om det nya språket och få proceduren för att uppgradera din arbetsyta på [uppgradera Azure logganalys-arbetsytan till ny logg sökning](log-analytics-log-search-upgrade.md).  
>
> Om ditt arbetsområde inte har uppgraderats till det nya språket i fråga, bör du gå till [söka efter data med hjälp av loggen sökningar i logganalys](log-analytics-log-searches.md) information om den aktuella versionen av loggen Sök-portalen.

Den här artikeln innehåller en genomgång som beskriver hur du skapar loggen sökningar och analysera data som lagras i logganalys-arbetsytan med hjälp av loggen Sök-portalen.  Vägledningen innehåller köra några enkla frågor för att returnera olika typer av data och när resultat.  Den fokuserar på funktioner i loggen Sök portal för att ändra frågan i stället för att ändra direkt.  Mer information om hur du redigerar frågan direkt finns det [Query Language referens](https://go.microsoft.com/fwlink/?linkid=856079).

För att skapa sökningar i Advanced Analytics-portalen i stället för att logga Sök-portalen, se [komma igång med Analytics-portalen](https://go.microsoft.com/fwlink/?linkid=856587).  Båda portaler använda samma frågespråket tillgång till samma data i logganalys-arbetsytan.

## <a name="prerequisites"></a>Krav
Den här kursen förutsätter att du redan har en logganalys-arbetsytan med minst en ansluten datakälla som genererar data för frågor för att analysera.  

- Om du inte har en arbetsyta, du kan skapa en kostnadsfri med hjälp av proceduren på [komma igång med en logganalys-arbetsytan](log-analytics-get-started.md).
- Ansluta minst ett [Windows-agenten](log-analytics-windows-agents.md) eller en [Linux-agenten](log-analytics-linux-agents.md) till arbetsytan.  

## <a name="open-the-log-search-portal"></a>Öppna loggen Sök-portalen
Starta genom att öppna loggen Sök-portalen.  Du kan komma åt den i Azure-portalen eller OMS-portalen.

1. Öppna Azure-portalen.
2. Navigera till logganalys och markera arbetsytan.
3. Välj antingen **loggen Sök** förblir i Azure-portalen eller starta OMS-portalen genom att välja **OMS-portalen** och sedan klicka på knappen Sök i loggfilen.

![Logga sökknappen](media/log-analytics-log-search-log-search-portal/log-search-button.png)

## <a name="create-a-simple-search"></a>Skapa en enkel sökning
Det snabbaste sättet att hämta vissa data att arbeta med är en enkel fråga som returnerar alla poster i tabellen.  Om du har Windows- eller Linux-klienter som är anslutna till din arbetsyta och du har data i den händelse (Windows) eller Syslog (Linux) tabell.

Skriv ett följande frågor i sökrutan och klicka på sökknappen.  

```
Event
```
```
Syslog
```

Data returneras i standardlistvyn och du kan se hur många Totalt antal poster returnerades.

![Enkel fråga](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

Endast de första några egenskaperna för varje post visas.  Klicka på **visa fler** att visa alla egenskaper för en viss post.

![Registrera information](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>Ange omfånget tid
Alla poster som samlas in av logganalys har en **TimeGenerated** egenskap som innehåller datum och tid då posten skapades.  En fråga i loggen Sök portal returnerar bara poster med en **TimeGenerated** omfattas tid som visas på vänster sida av skärmen.  

Du kan ändra tidsfiltret genom att välja i listrutan eller genom att ändra skjutreglaget.  Skjutreglaget visar ett stapeldiagram som visar relativa antalet poster för varje gång segment inom intervallet.  Det här segmentet varierar beroende på intervallet.

Standard tid omfånget är **1 dag**.  Ändra värdet till **7 dagar**, och det totala antalet poster ska öka.

![Datum tid omfång](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>Filtrera resultatet av frågan
På vänster sida av skärmen är filterfönstret där du kan lägga till filtrering i frågan utan att ändra den direkt.  Med de tio främsta värdena med antal poster visas flera egenskaper poster returneras.

Om du arbetar med **händelse**, markera kryssrutan bredvid **fel** under **EVENTLEVELNAME**.   Om du arbetar med **Syslog**, markera kryssrutan bredvid **err** under **SEVERITYLEVEL**.  Detta ändrar frågan till något av följande för att begränsa resultatet till felhändelser.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filter](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

Lägga till egenskaper i filterrutan genom att välja **lägga till filter** på menyn egenskapen på en av posterna.

![Lägga till filter-menyn](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

Du kan ange samma filter genom att välja **Filter** på menyn för en post med värdet som du vill filtrera.  

Du har bara den **Filter** alternativ för egenskaper med deras namn i blått.  Dessa är *sökbara* fält som indexeras för sökvillkor.  Fält i grått *fritext sökbara* fält som bara har de **visa referenser** alternativet.  Det här alternativet returnerar poster som har detta värde i en egenskap.

![Filter-menyn](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

Du kan gruppera resultaten på en enskild egenskap genom att välja den **Gruppera efter** alternativ i post-menyn.  Detta lägger till en [sammanfatta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) operatorn för att din fråga som visar resultatet i ett diagram.  Du kan gruppera på mer än en egenskap, men du vill redigera frågan direkt.  Välj menyn post bredvid den den **datorn** egenskapen och välj **Gruppera efter ”dator”**.  

![Gruppera efter dator](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>Arbeta med resultat
Loggen Sök-portalen har en mängd funktioner för att arbeta med resultatet av en fråga.  Du kan sortera, filtrera och gruppera resultat att analysera data utan att ändra den faktiska frågan.  Resultatet av en fråga sorteras inte som standard.

Om du vill visa data i tabellformat som tillhandahåller ytterligare alternativ för att filtrera och sortera, klickar du på **tabellen**.  

![Tabellvy](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

Klicka på pilen genom en post för att visa detaljer för posten.

![Sortera resultat](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

Sortera efter något fält genom att klicka på dess kolumnrubriken.

![Sortera resultat](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

Filtrera resultaten för ett specifikt värde i kolumnen genom att klicka på filterknappen och ett filtervillkor.

![Filtrera resultat](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

Gruppera efter en kolumn genom att dra dess rubrik överst i resultaten.  Du kan gruppera på flera fält genom att dra flera kolumner på sidan.

![Gruppera resultat](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>Arbeta med prestandadata
Prestandadata för både Windows och Linux-agenter lagras i logganalys-arbetsytan i den **Perf** tabell.  Uppgifter som ser ut precis som andra poster och vi kan skriva en enkel fråga som returnerar alla uppgifter på samma sätt som med händelser.

```
Perf
```

![Prestandadata](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

Returnerar miljontals poster för alla prestandaobjekt och räknare men är inte användbar.  Du kan använda samma metoder som du använde för att filtrera data eller skriver du följande fråga direkt till log-sökrutan ovan.  Detta returnerar endast processor användning poster för både Windows och Linux-datorer.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Processorbelastning](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

Detta begränsar data till en viss prestandaräknare, men det fortfarande Placera inte den i ett formulär som är särskilt användbart.  Du kan visa data i ett linjediagram, men måste först Gruppera efter dator och TimeGenerated.  Om du vill gruppera efter flera fält, måste du ändra frågan direkt, så ändra frågan så här.  Här används den [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) fungera på den **CounterValue** egenskapen att beräkna medelvärdet för varje timme.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Data prestandadiagrammet](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

Nu när data är lämpligt grupperade kan du visa det i ett visual diagram genom att lägga till den [återge](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) operator.  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Linjediagram](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om Log Analytics-frågespråket i [komma igång med Analytics-portalen](https://go.microsoft.com/fwlink/?linkid=856079).
- Gå igenom en självstudiekurs om att använda den [Advanced Analytics portal](https://go.microsoft.com/fwlink/?linkid=856587) där du kan köra samma frågor och få åtkomst till samma data som loggen Sök-portalen.
