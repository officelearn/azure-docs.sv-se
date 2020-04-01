---
title: 'Självstudiekurs: Kom igång med Logganalysfrågor'
description: Lär dig av den här självstudien hur du skriver och hanterar Azure Monitor-loggfrågor med Hjälp av Log Analytics i Azure-portalen.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 29e24166218a6757cded9d1b002321800ab0c073
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80055510"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Självstudiekurs: Kom igång med Logganalysfrågor

Den här självstudien visar hur du använder Log Analytics för att skriva, köra och hantera Azure Monitor-loggfrågor i Azure-portalen. Du kan använda Log Analytics-frågor för att söka efter termer, identifiera trender, analysera mönster och ge många andra insikter från dina data. 

I den här självstudien får du lära dig hur du använder Log Analytics för att:

> [!div class="checklist"]
> * Förstå loggdataschemat
> * Skriva och köra enkla frågor och ändra tidsintervallet för frågor
> * Filtrera, sortera och gruppera frågeresultat
> * Visa, ändra och dela visuella objekt med frågeresultat
> * Spara, läsa in, exportera och kopiera frågor och resultat

Mer information om loggfrågor finns [i Översikt över loggfrågor i Azure Monitor](log-query-overview.md).<br/>
En detaljerad självstudiekurs om hur du skriver loggfrågor finns [i Komma igång med loggfrågor i Azure Monitor](get-started-queries.md).

## <a name="open-log-analytics"></a>Öppna Logganalys
Om du vill använda Log Analytics måste du vara inloggad på ett Azure-konto. Om du inte har ett Azure-konto [skapar du ett kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Om du vill slutföra de flesta stegen i den här självstudien kan du använda [den här demomiljön](https://portal.loganalytics.io/demo), som innehåller massor av exempeldata. Med demomiljön kan du inte spara frågor eller fästa resultat på en instrumentpanel.

Du kan också använda din egen miljö om du använder Azure Monitor för att samla in loggdata på minst en Azure-resurs. Om du vill öppna en Log Analytics-arbetsyta väljer du **Loggar**i den vänstra Azure Monitor-navigeringen . 

## <a name="understand-the-schema"></a>Förstå schemat
Ett *schema* är en samling tabeller grupperade under logiska kategorier. Demoschemat har flera kategorier från övervakningslösningar. Kategorin **LogManagement** innehåller till exempel Windows- och Syslog-händelser, prestandadata och agentpulser.

Schematabellerna visas på fliken **Tabeller** på arbetsytan Logganalys. Tabellerna innehåller kolumner, var och en med en datatyp som visas av ikonen bredvid kolumnnamnet. **Händelsetabellen** innehåller till exempel textkolumner som **Dator** och numeriska kolumner som **EventCategory**.

![Schema](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Skriva och köra grundläggande frågor

Log Analytics öppnas med en ny tom fråga i **Frågeredigeraren**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Skriva en fråga
Azure Monitor-loggfrågor använder en version av Kusto-frågespråket. Frågor kan börja med antingen ett tabellnamn eller ett [sökkommando.](/azure/kusto/query/searchoperator) 

Följande fråga hämtar alla poster från **tabellen Händelse:**

```Kusto
Event
```

Pipe -tecknet (|) separerar kommandon, så utdata från det första kommandot är indata för nästa kommando. Du kan lägga till valfritt antal kommandon i en enskild fråga. Följande fråga hämtar posterna från **tabellen Händelse** och söker sedan efter **termfelet** i valfri egenskap:

```Kusto
Event 
| search "error"
```

En enda radbrytning gör frågor lättare att läsa. Mer än en radbrytning delar upp frågan i separata frågor.

Ett annat sätt att skriva samma fråga är:

```Kusto
search in (Event) "error"
```

I det andra exemplet söker **sökkommandot** bara efter **termfel**i tabellen Händelser i tabellen **Händelser** .

Som standard begränsar Log Analytics frågor till ett tidsintervall för de senaste 24 timmarna. Om du vill ange ett annat tidsintervall kan du lägga till ett explicit **TimeGenerated-filter** i frågan eller använda kontrollen **Tidsområde.**

### <a name="use-the-time-range-control"></a>Använd kontrollen Tidsområde
Om du vill använda kontrollen **Tidsområde** markerar du den i det övre fältet och väljer sedan ett värde i listrutan, eller väljer **Anpassad** för att skapa ett anpassat tidsintervall.

![Tidplockare](media/get-started-portal/time-picker.png)

- Tidsintervallvärden finns i UTC, vilket kan vara annorlunda än den lokala tidszonen.
- Om frågan uttryckligen anger ett filter för **TimeGenerated**visas Ange i fråga när tidsväljaren visar **Ange i fråga**och är inaktiverad för att förhindra en konflikt.

### <a name="run-a-query"></a>Köra en fråga
Om du vill köra en fråga placerar du markören någonstans i frågan och väljer **Kör** i det övre fältet eller trycker på **Skift**+**retur**. Frågan körs tills den hittar en tom rad.

## <a name="filter-results"></a>Filtrera resultat
Log Analytics begränsar resultatet till högst 10 000 poster. En allmän `Event` fråga som returnerar för många resultat för att vara användbar. Du kan filtrera frågeresultat antingen genom att begränsa tabellelementen i frågan eller genom att uttryckligen lägga till ett filter i resultatet. Om du filtrerar genom tabellelementen returneras en ny resultatuppsättning, medan ett explicit filter gäller för den befintliga resultatuppsättningen.

### <a name="filter-by-restricting-table-elements"></a>Filtrera genom att begränsa tabellelement
Så `Event` här filtrerar du frågeresultat till **felhändelser** genom att begränsa tabellelement i frågan:

1. I frågeresultatet markerar du listpilen bredvid alla poster som har **fel** i kolumnen **EventLevelName.** 
   
1. I den utökade informationen hovrar du över och väljer **...** bredvid **EventLevelName**och väljer sedan **Inkludera "Fel".** 
   
   ![Lägga till filter i fråga](media/get-started-portal/add-filter.png)
   
1. Observera att frågan i **Frågeredigeraren** nu har ändrats till:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Välj **Kör** om du vill köra den nya frågan.

### <a name="filter-by-explicitly-filtering-results"></a>Filtrera efter att uttryckligen filtrera resultat
Så här `Event` filtrerar du frågeresultaten till **felhändelser** genom att filtrera frågeresultaten:

1. I frågeresultatet väljer du **filterikonen bredvid** kolumnrubriken **EventLevelName**. 
   
1. I det första fältet i popup-fönstret väljer du **Är lika med**, och i nästa fält anger du *fel*. 
   
1. Välj **Filter**.
   
   ![Filter](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Sortera, gruppera och markera kolumner
Om du vill sortera frågeresultat efter en viss kolumn, till exempel **TimeGenerated [UTC]**, markerar du kolumnrubriken. Markera rubriken igen om du vill växla mellan stigande och fallande ordning.

![Sortera kolumn](media/get-started-portal/sort-column.png)

Ett annat sätt att ordna resultat är efter grupper. Om du vill gruppera resultat efter en viss kolumn drar du kolumnrubriken till stapeln ovanför resultattabellen **Dra en kolumnrubrik och släpper det här för att gruppera efter den kolumnen**. Om du vill skapa undergrupper drar du andra kolumner till det övre fältet. Du kan ordna om hierarkin och sorteringen av grupperna och undergrupperna i fältet.

![Grupper](media/get-started-portal/groups.png)

Om du vill dölja eller visa kolumner i resultaten markerar du **Kolumner** ovanför tabellen och markerar eller avmarkerar önskade kolumner i listrutan.

![Välja kolumner](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Visa och ändra diagram
Du kan också se frågeresultat i visuella format. Ange följande fråga som ett exempel:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Som standard visas resultaten i en tabell. Välj **Diagram** ovanför tabellen om du vill visa resultaten i en grafisk vy.

![Stapeldiagram](media/get-started-portal/bar-chart.png)

Resultaten visas i ett staplat stapeldiagram. Välj andra alternativ som **Staplad kolumn** eller **Cirkel** om du vill visa andra vyer av resultaten.

![Cirkeldiagram](media/get-started-portal/pie-chart.png)

Du kan ändra egenskaperna för vyn, till exempel x- och y-axlar, eller gruppera och dela inställningar manuellt från kontrollfältet.

Du kan också ange önskad vy i själva frågan med hjälp av operatorn [render.](/azure/kusto/query/renderoperator)

## <a name="pin-results-to-a-dashboard"></a>Fästa resultat på en instrumentpanel
Om du vill fästa en resultattabell eller ett diagram från Log Analytics på en delad Azure-instrumentpanel väljer du Fäst på **instrumentpanelen** i det övre fältet. 

![Fäst vid instrumentpanelen](media/get-started-portal/pin-dashboard.png)

Markera eller skapa en delad instrumentpanel som du vill fästa vid i fönstret Fäst på en **annan instrumentpanel** och välj **Använd**. Tabellen eller diagrammet visas på den valda Azure-instrumentpanelen.

![Diagram fäst på instrumentpanelen](media/get-started-portal/pin-dashboard2.png)

En tabell eller ett diagram som du fäster på en delad instrumentpanel har följande förenklingar: 

- Data är begränsade till de senaste 14 dagarna.
- En tabell visar bara upp till fyra kolumner och de sju översta raderna.
- Diagram med många diskreta kategorier grupperar automatiskt mindre ifyllda kategorier på en enda **annans** lagerplats.

## <a name="save-load-or-export-queries"></a>Spara, läsa in eller exportera frågor
När du har skapat en fråga kan du spara eller dela frågan eller resultaten med andra. 

### <a name="save-queries"></a>Spara frågor
Så här sparar du en fråga:

1. Välj **Spara** i det övre fältet.
   
1. I dialogrutan **Spara** ger du frågan ett **namn**med tecknen a–z, A–Ö, 0–9, mellanslag, bindestreck, understreck, period, parentes eller pipe. 
   
1. Välj om frågan ska sparas som en **fråga** eller en **funktion**. Funktioner är frågor som andra frågor kan referera till. 
   
   Om du vill spara en fråga som en funktion anger du ett **funktionsalias**, som är ett kort namn för andra frågor som ska användas för att anropa den här frågan.
   
1. Ange en **kategori** för **frågeutforskare som** ska användas för frågan.
   
1. Välj **Spara**.
   
   ![Spara funktion](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Läsa in frågor
Om du vill läsa in en sparad fråga väljer du **Frågeutforskare längst** upp till höger. Fönstret **Frågeutforskaren** öppnas med en lista över alla frågor efter kategori. Expandera kategorierna eller ange ett frågenamn i sökfältet och välj sedan en fråga för att läsa in den i **Frågeredigeraren**. Du kan markera en fråga som en **favorit** genom att välja stjärnan bredvid frågenamnet.

![Frågeutforskaren](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Exportera och dela frågor
Om du vill exportera en fråga väljer du **Exportera** i det övre fältet och väljer sedan **Exportera till CSV – alla kolumner**, Exportera till **CSV – visade kolumner**eller Exportera till Power BI **(M-fråga)** i listrutan.

Om du vill dela en länk till en fråga väljer du **Kopiera länk** i det övre fältet och väljer sedan Kopiera länk **till fråga,** **Kopiera frågetext**eller **Kopiera frågeresultat** som ska kopieras till Urklipp. Du kan skicka frågelänken till andra som har åtkomst till samma arbetsyta.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa självstudiekurs om du vill veta mer om hur du skriver Azure Monitor-loggfrågor.
> [!div class="nextstepaction"]
> [Skriv Azure Monitor-loggfrågor](get-started-queries.md)
