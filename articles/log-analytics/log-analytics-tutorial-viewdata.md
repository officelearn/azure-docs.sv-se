---
title: Visa och analysera Azure Log Analytics-data som samlas in | Microsoft Docs
description: "Den här artikeln innehåller en genomgång som beskriver hur du skapar loggen sökningar och analysera data som lagras i logganalys resursen med hjälp av loggen Sök-portalen.  Vägledningen innehåller köra några enkla frågor för att returnera olika typer av data och när resultat."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/26/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: dfcbb925a16ca1e53d10b7bf70d03e62bc9dae69
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="view-or-analyze-data-collected-with-log-analytics-log-search"></a>Visa och analysera data som samlas in med logganalys loggen search

Du kan utnyttja loggen sökningar genom att skapa frågor för att analysera insamlade data, använda befintliga instrumentpaneler som du kan anpassa med grafiska vyer av dina mest värdefulla sökningar i logganalys.  Nu när du har definierat insamling av användningsdata från din virtuella Azure-datorer och aktivitetsloggar i den här kursen lär du dig hur du:

> [!div class="checklist"]
> * Uppgradera Azure logganalys-resursen till nya frågespråket 
> * Utföra en enkel sökning i händelsedata och använda funktioner för att ändra och filtrera resultaten 
> * Lär dig hur du arbetar med prestandadata

Du måste ha en befintlig virtuell dator för att slutföra exemplet i den här självstudiekursen [ansluten till logganalys-arbetsytan](log-analytics-quick-collect-azurevm.md).  

Skapa och redigera frågor, förutom att arbeta interaktivt med returnerade data kan vara utfört ett av två sätt.  Använd sidan logg i Azure-portalen för grundläggande frågor eller för avancerade frågor kan du använda den avancerade analyser-portalen. Mer information om funktionsändringar skillnaden mellan två portaler finns [portaler för att skapa och redigera loggen frågor i Azure Log Analytics](log-analytics-log-search-portals.md)

I den här självstudiekursen kommer vi att arbeta med loggen Sök i Azure-portalen. 

## <a name="log-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 

## <a name="open-the-log-search-portal"></a>Öppna loggen Sök-portalen 
Starta genom att öppna loggen Sök-portalen.   

1. I Azure-portalen klickar du på **fler tjänster** hittades i det nedre vänstra hörnet. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **logga Analytics**.
2. Välj en arbetsyta i fönstret logganalys prenumerationer och välj sedan den **loggen Sök** panelen.<br> ![Logga sökknappen](media/log-analytics-tutorial-viewdata/azure-portal-01.png)

Kanske såg du på banderoll överst på sidan logganalys resurs i portalen bjuda in dig att uppgradera.<br> ![Logganalys uppgraderas i Azure-portalen](media/log-analytics-tutorial-viewdata/log-analytics-portal-upgradebanner.png)

Logganalys nyligen lades en ny frågespråket till gör det lättare att skapa frågor, korrelera data från olika källor och analysera för att snabbt identifiera trender eller problem.

Det är enkelt att uppgradera.  Starta processen genom att klicka på listen som säger **mer och uppgradera**.  Läs igenom informationen om uppgraderingen på sidan information om uppgradering och klicka sedan på **uppgradera nu**.

Ta några minuter att slutföra och under tiden du kan följa förloppet under **meddelanden** på menyn. Du kan lära dig mer om den [fördelarna med nya frågespråket](log-analytics-log-search-upgrade.md#why-the-new-language).

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

![Enkel fråga](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-01.png)

Endast de första några egenskaperna för varje post visas.  Klicka på **visa fler** att visa alla egenskaper för en viss post.

## <a name="filter-results-of-the-query"></a>Filtrera resultatet av frågan
På vänster sida av skärmen är filterfönstret där du kan lägga till filtrering i frågan utan att ändra den direkt.  Flera poster egenskaper visas för posttypen och du kan välja en eller flera egenskapsvärden för att begränsa sökresultaten.

Om du arbetar med **händelse**, markera kryssrutan bredvid **fel** under **EVENTLEVELNAME**.   Om du arbetar med **Syslog**, markera kryssrutan bredvid **err** under **SEVERITYLEVEL**.  Detta ändrar frågan till något av följande för att begränsa resultatet till felhändelser.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filter](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-02.png)

Lägga till egenskaper i filterrutan genom att välja **lägga till filter** på menyn egenskapen på en av posterna.

![Lägga till filter-menyn](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-03.png)

Du kan ange samma filter genom att välja **Filter** på menyn för en post med värdet som du vill filtrera.  

Du har bara den **Filter** alternativ för egenskaper med deras namn i blått när du hovrar över dem.  Dessa är *sökbara* fält som indexeras för sökvillkor.  Fält i grått *fritext sökbara* fält som bara har de **visa referenser** alternativet.  Det här alternativet returnerar poster som har detta värde i en egenskap.

Du kan gruppera resultaten på en enskild egenskap genom att välja den **Gruppera efter** alternativ i post-menyn.  Detta lägger till en [sammanfatta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) operatorn för att din fråga som visar resultatet i ett diagram.  Du kan gruppera på mer än en egenskap, men du vill redigera frågan direkt.  Välj menyn post bredvid den den **datorn** egenskapen och välj **Gruppera efter ”dator”**.  

![Gruppera efter dator](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-04.png)

## <a name="work-with-results"></a>Arbeta med resultat
Loggen Sök-portalen har en mängd funktioner för att arbeta med resultatet av en fråga.  Du kan sortera, filtrera och gruppera resultat att analysera data utan att ändra den faktiska frågan.  Resultatet av en fråga sorteras inte som standard.

Om du vill visa data i tabellformat som tillhandahåller ytterligare alternativ för att filtrera och sortera, klickar du på **tabellen**.  

![Tabellvy](media/log-analytics-tutorial-viewdata/log-search-portal-table-01.png)

Klicka på pilen genom en post för att visa detaljer för posten.

![Sortera resultat](media/log-analytics-tutorial-viewdata/log-search-portal-table-02.png)

Sortera efter något fält genom att klicka på dess kolumnrubriken.

![Sortera resultat](media/log-analytics-tutorial-viewdata/log-search-portal-table-03.png)

Filtrera resultaten för ett specifikt värde i kolumnen genom att klicka på filterknappen och ett filtervillkor.

![Filtrera resultat](media/log-analytics-tutorial-viewdata/log-search-portal-table-04.png)

Gruppera efter en kolumn genom att dra dess rubrik överst i resultaten.  Du kan gruppera på flera fält genom att dra flera kolumner på sidan.

![Gruppera resultat](media/log-analytics-tutorial-viewdata/log-search-portal-table-05.png)


## <a name="work-with-performance-data"></a>Arbeta med prestandadata
Prestandadata för både Windows och Linux-agenter lagras i logganalys-arbetsytan i den **Perf** tabell.  Uppgifter som ser ut som andra poster och det dags att skriva en enkel fråga som returnerar alla uppgifter på samma sätt som med händelser.

```
Perf
```

![Prestandadata](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-01.png)

Returnerar miljontals poster för alla prestandaobjekt och räknare men är inte användbar.  Du kan använda samma metoder som du använde för att filtrera data eller skriver du följande fråga direkt till log-sökrutan ovan.  Detta returnerar endast processor användning poster för både Windows och Linux-datorer.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Processorbelastning](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-02.png)

Detta begränsar data till en viss prestandaräknare, men det fortfarande Placera inte den i ett formulär som är särskilt användbart.  Du kan visa data i ett linjediagram, men måste först Gruppera efter dator och TimeGenerated.  Om du vill gruppera efter flera fält, måste du ändra frågan direkt, så ändra frågan så här.  Här används den [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) fungera på den **CounterValue** egenskapen att beräkna medelvärdet för varje timme.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Data prestandadiagrammet](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-03.png)

Nu när data är lämpligt grupperade kan du visa det i ett visual diagram genom att lägga till den [återge](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) operator.  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Linjediagram](media/log-analytics-tutorial-viewdata/log-analytics-portal-linechart-01.png)

## <a name="next-steps"></a>Nästa steg
I kursen får du har lärt dig hur du skapar grundläggande loggen sökningar för att analysera händelse-och prestandadata.  Gå vidare till nästa kursen lär dig hur du visualisera data genom att skapa en instrumentpanel.

> [!div class="nextstepaction"]
> [Skapa och dela logganalys instrumentpaneler](log-analytics-tutorial-dashboards.md)