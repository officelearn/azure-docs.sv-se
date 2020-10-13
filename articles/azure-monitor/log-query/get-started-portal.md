---
title: 'Självstudie: kom igång med Log Analytics frågor'
description: Lär dig i den här självstudien om hur du skriver och hanterar Azure Monitor logg frågor med Log Analytics i Azure Portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: b337065f125d12e309dd1f7fcc56c2af72b1c28c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088365"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Självstudie: kom igång med Log Analytics frågor

Den här självstudien visar hur du använder Log Analytics för att skriva, köra och hantera Azure Monitor logg frågor i Azure Portal. Du kan använda Log Analytics frågor för att söka efter villkor, identifiera trender, analysera mönster och tillhandahålla många andra insikter från dina data. 

I den här självstudien får du lära dig hur du använder Log Analytics för att:

> [!div class="checklist"]
> * Förstå logg data schema
> * Skriv och kör enkla frågor och ändra tidsintervallet för frågor
> * Filtrera, sortera och gruppera frågeresultat
> * Visa, ändra och dela visuella objekt av frågeresultat
> * Spara, läsa in, exportera och kopiera frågor och resultat

Mer information om logg frågor finns i [Översikt över logg frågor i Azure Monitor](log-query-overview.md).<br/>
En detaljerad själv studie kurs om hur du skriver logg frågor finns [i kom igång med logg frågor i Azure Monitor](get-started-queries.md).

## <a name="open-log-analytics"></a>Öppna Log Analytics
Om du vill använda Log Analytics måste du vara inloggad på ett Azure-konto. Om du inte har ett Azure-konto kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

För att slutföra de flesta av stegen i den här självstudien kan du använda [den här demo miljön](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), som innehåller massor av exempel data. Med demo miljön kan du inte spara frågor eller fästa resultat på en instrument panel.

Du kan också använda en egen miljö om du använder Azure Monitor för att samla in loggdata på minst en Azure-resurs. Öppna en Log Analytics arbets yta genom att välja **loggar**i Azure Monitor vänster-navigering. 

## <a name="understand-the-schema"></a>Förstå schemat
 
Ett *schema* är en samling tabeller grupperade under logiska kategorier. Demo schemat har flera kategorier från övervaknings lösningar. Kategorin **LogManagement** innehåller till exempel Windows-och Syslog-händelser, prestanda data och agent pulsslag.

Schema tabellerna visas på fliken **tabeller** i arbets ytan Log Analytics. Tabellerna innehåller kolumner, var och en med en datatyp som visas med ikonen bredvid kolumn namnet. **Händelse** tabellen innehåller till exempel text kolumner som **dator** och numeriska kolumner som **EventCategory**.

![Skärm bild som visar sidan med Azure Portal loggar med en ny fråga och markerar rutan tabeller med dator-och EventCategory markerade.](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Skriv och kör grundläggande frågor

Log Analytics öppnas med en ny tom fråga i **Frågeredigeraren**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Skriv en fråga

Azure Monitor logg frågor använder en version av Kusto-frågespråket. Frågor kan inledas med antingen ett tabell namn eller ett [Sök](/azure/kusto/query/searchoperator) kommando. 

Följande fråga hämtar alla poster från **händelse** tabellen:

```Kusto
Event
```

Pipe-tecknet (|) separerar kommandon, så utdata från det första kommandot är indata för nästa kommando. Du kan lägga till ett valfritt antal kommandon i en enskild fråga. Följande fråga hämtar posterna från **händelse** tabellen och söker sedan efter ett term **fel** i alla egenskaper:

```Kusto
Event 
| search "error"
```

En enda rad brytning gör det lättare att läsa frågor. Mer än en rad brytning delar upp frågan i separata frågor.

Ett annat sätt att skriva samma fråga är:

```Kusto
search in (Event) "error"
```

I det andra exemplet söker **Sök** kommandot endast efter poster i tabellen **händelser** för term **felet**.

Som standard begränsar Log Analytics frågor till ett tidsintervall under de senaste 24 timmarna. Om du vill ange ett annat tidsintervall kan du lägga till ett explicit **TimeGenerated** -filter till frågan eller använda **tidsintervalls** kontrollen.

### <a name="use-the-time-range-control"></a>Använda tidsintervalls kontrollen
Om du vill använda kontrollen **tidsintervall** väljer du den i det översta fältet och väljer sedan ett värde i list rutan eller väljer **anpassad** om du vill skapa ett anpassat tidsintervall.

![Tids väljare](media/get-started-portal/time-picker.png)

- Tidsintervalls värden är UTC, vilket kan skilja sig från den lokala tids zonen.
- Om frågan uttryckligen ställer in ett filter för **TimeGenerated**, visar tids väljaren **set i Query**och är inaktive rad för att förhindra en konflikt.

### <a name="run-a-query"></a>Köra en fråga
Om du vill köra en fråga placerar du markören någonstans i frågan och väljer **Kör** i det översta fältet **eller trycker på** + **RETUR**. Frågan körs tills den hittar en tom rad.

## <a name="filter-results"></a>Filtrera resultat
Log Analytics begränsar resultatet till högst 10 000 poster. En allmän fråga som `Event` returnerar för många resultat för att vara användbar. Du kan filtrera frågeresultaten antingen genom att begränsa tabell elementen i frågan eller genom att uttryckligen lägga till ett filter i resultatet. Filtrering genom tabell elementen returnerar en ny resultat uppsättning, medan ett explicit filter används för den befintliga resultat uppsättningen.

### <a name="filter-by-restricting-table-elements"></a>Filtrera genom att begränsa tabell element
Filtrera `Event` Frågeresultat till **fel** händelser genom att begränsa tabell element i frågan:

1. I frågeresultaten väljer du den nedrullningsbara pilen bredvid alla poster som har **fel** i kolumnen **EventLevelName** . 
   
1. Hovra över i den expanderade informationen och välj **...** bredvid **EventLevelName**och välj sedan **inkludera "fel"**. 
   
   ![Lägg till filter i fråga](media/get-started-portal/add-filter.png)
   
1. Observera att frågan **i Frågeredigeraren nu har ändrats** till:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Välj **Kör** för att köra den nya frågan.

### <a name="filter-by-explicitly-filtering-results"></a>Filtrera genom att uttryckligen filtrera resultat
Filtrera `Event` frågeresultaten till **fel** händelser genom att filtrera frågeresultaten:

1. I frågeresultaten väljer du **filter** ikonen bredvid kolumn rubriken **EventLevelName**. 
   
1. I det första fältet i popup-fönstret väljer du **är lika med**och i nästa fält anger du *fel*. 
   
1. Välj **filter**.
   
   ![Skärm bild som visar en tabell med resultat med en snabb meny för att filtrera resultat efter EventLevelName.](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Sortera, gruppera och Välj kolumner
Om du vill sortera frågeresultaten efter en speciell kolumn, t. ex. **TimeGenerated [UTC]**, väljer du kolumn rubriken. Välj rubriken igen om du vill växla mellan stigande och fallande ordning.

![Sorterings kolumn](media/get-started-portal/sort-column.png)

Ett annat sätt att ordna resultaten är av grupper. Om du vill gruppera resultatet efter en speciell kolumn drar du kolumn rubriken till fältet ovanför resultat tabellen med etiketten **dra en kolumn rubrik och släpper den här för att gruppera efter den kolumnen**. Dra andra kolumner till den övre stapeln om du vill skapa under grupper. Du kan arrangera om hierarkin och sorteringen av grupper och under grupper i fältet.

![Skärm bild som visar frågeresultat med under grupper för EventLevelName och dator.](media/get-started-portal/groups.png)

Om du vill dölja eller Visa kolumner i resultaten väljer du **kolumner** ovanför tabellen och markerar eller avmarkerar sedan de kolumner som du vill använda i list rutan.

![Välja kolumner](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Visa och ändra diagram
Du kan också se frågeresultat i visuella format. Ange följande fråga som exempel:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Som standard visas resultaten i en tabell. Markera **diagrammet** ovanför tabellen om du vill se resultatet i en grafisk vy.

![Stapeldiagram](media/get-started-portal/bar-chart.png)

Resultaten visas i ett liggande stapeldiagram. Välj andra alternativ som **staplad kolumn** eller **cirkel** för att visa andra vyer av resultaten.

![Cirkeldiagram](media/get-started-portal/pie-chart.png)

Du kan ändra egenskaperna för vyn, till exempel x-och y-axlar, eller gruppering och dela inställningar, manuellt från kontroll fältet.

Du kan också ställa in den önskade vyn i själva frågan med operatorn [Render](/azure/kusto/query/renderoperator) .

## <a name="pin-results-to-a-dashboard"></a>Fästa resultat på en instrument panel

Om du vill fästa en resultat tabell eller ett diagram från Log Analytics till en delad Azure-instrumentpanel väljer du **Fäst på instrument panelen** i det översta fältet. 

![Fäst vid instrumentpanelen](media/get-started-portal/pin-dashboard.png)

I rutan **Fäst på en annan instrument panel** väljer eller skapar du en delad instrument panel att fästa på och väljer **tillämpa**. Tabellen eller diagrammet visas på den valda Azure-instrumentpanelen.

![Diagrammet har fästs på instrument panelen](media/get-started-portal/pin-dashboard2.png)

En tabell eller ett diagram som du fäster på en delad instrument panel har följande förenklingar: 

- Data är begränsade till de senaste 14 dagarna.
- En tabell visar bara upp till fyra kolumner och de sju översta raderna.
- Diagram med många diskreta kategorier grupperar automatiskt mindre fyllda kategorier till en **annan** lager plats.

## <a name="save-load-or-export-queries"></a>Spara, Läs in eller exportera frågor

När du har skapat en fråga kan du spara eller dela frågan eller resultatet med andra. 

### <a name="save-queries"></a>Spara frågor

Så här sparar du en fråga:

1. Välj **Spara** i det översta fältet.
   
1. I dialog rutan **Spara** ger du frågan ett **namn**med hjälp av tecknen A – Z, a – z, 0-9, blank steg, bindestreck, under streck, punkt, parentes eller pipe. 
   
1. Välj om du vill spara frågan som en **fråga** eller en **funktion**. Functions är frågor som andra frågor kan referera till. 
   
   Om du vill spara en fråga som en funktion anger du ett **funktions Ali Aset**, vilket är ett kort namn för andra frågor som ska användas för att anropa den här frågan.
   
1. Om du är i en Log Analytics arbets yta, anger du en **kategori** för **query Explorer** som ska användas för frågan. (Kategorier är inte tillgängliga för program insikts frågor)
   
1. Välj **Spara**.
   
   ![Spara funktion](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Läs in frågor
Om du vill läsa in en sparad fråga väljer du **query Explorer** längst upp till höger. Fönstret **query Explorer** öppnas och visar alla frågor per kategori. Expandera kategorierna eller ange ett frågenamn i Sök fältet och välj sedan en fråga för att läsa in den i **Frågeredigeraren**. Du kan markera en fråga som en **favorit** genom att välja stjärnan bredvid frågans namn.

![Query Explorer](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Exportera och dela frågor
Om du vill exportera en fråga väljer du **Exportera** i det översta fältet och väljer sedan **Exportera till CSV – alla kolumner**, **Exportera till CSV-visade kolumner**eller **Exportera till Power BI (M fråga)** i list rutan.

Följande video visar hur du integrerar Log Analytics med Excel.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Asme]

Om du vill dela en länk till en fråga väljer du **Kopiera länk** i det översta fältet och väljer sedan **Kopiera länk till fråga**, **Kopiera frågetext**eller **Kopiera frågeresultat** till kopiera till Urklipp. Du kan skicka fråg-länken till andra som har åtkomst till samma arbets yta.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa självstudie om du vill lära dig mer om att skriva Azure Monitor logg frågor.
> [!div class="nextstepaction"]
> [Skriv Azure Monitor logg frågor](get-started-queries.md)
