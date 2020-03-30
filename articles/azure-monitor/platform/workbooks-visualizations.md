---
title: Visualiseringar av Azure Monitor-arbetsbok
description: Lär dig mer om alla Visualiseringar av Azure Monitor-arbetsboks visualiseringar, inklusive - text, diagram, rutnät, träd och diagram.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658038"
---
# <a name="azure-monitor-workbook-visualizations"></a>Visualiseringar av Azure Monitor-arbetsbok

Azure Monitor-arbetsböcker stöder ett antal olika visualiseringar för att tillgodose dina rapporteringsbehov. Den här artikeln innehåller exempel på varje typ av visualisering.

## <a name="text"></a>Text

Arbetsböcker gör det möjligt för författare att inkludera textblock i sina arbetsböcker. Texten kan vara mänsklig analys av telemetri, information för att hjälpa användare att tolka dina data, avsnittrubriker, etc.

![Skärmbild av Apdex-texttabell](./media/workbooks-visualizations/apdex.png)

Text läggs till genom en Markdown-kontroll som ger fullständig formateringskontroll.

![Skärmbild av råmarkdown som skapar den renderade tabellen](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Lägga till en textkontroll

1. Växla arbetsboken till redigeringsläge genom att klicka på alternativet **Redigera** verktygsfält.
2. Använd länken **Lägg till text** om du vill lägga till en textkontroll i arbetsboken.
3. Lägg till Markdown i kontrollen.
4. Klicka på knappen **Klar redigering** om du vill visa den formaterade texten.

> [!TIP]
> Använd den här [Markdown-lathunden](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) om du vill veta mer om olika formateringsalternativ.

## <a name="charts"></a>Diagram

Arbetsböcker gör det möjligt att presentera övervakningsdata som diagram. Diagramtyper som stöds inkluderar linje, stapel, stapelkategorisk, område, punktdiagram, cirkel och tid. Författare kan välja att anpassa höjd, bredd, färgpalett, legend, titlar, no-data meddelande, etc. i diagrammet.

Arbetsböcker stöder diagram för både loggar och måttdatakällor. 

### <a name="adding-a-log-chart"></a>Lägga till ett loggdiagram

1. Växla arbetsboken till redigeringsläge genom att klicka på alternativet **Redigera** verktygsfält.
2. Använd länken **Lägg till fråga** om du vill lägga till en loggfrågekontroll i arbetsboken.
3. Välj frågetypen som **Log**, resurstyp (till exempel Application Insights) och de resurser som ska riktas.
4. Använd Frågeredigeraren för att ange [KQL](https://docs.microsoft.com/azure/kusto/query/) för din analys (till exempel trend av begäranden).
5. Ange visualiseringen till en av: **Område,** **Stapel**, **Stapel (kategorisk)**, **Linje,** **Cirkel,** **Punkt**eller **Tid**.
6. Ange andra parametrar om det behövs - som tidsintervall, visualisering, storlek, färgpalett och förklaring.

![Skärmbild av loggdiagram i redigeringsläge](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parametrar för loggdiagram

| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `Query Type` | Den typ av fråga som ska användas | Logga, Azure Resource Graph, etc. |
| `Resource Type` | Resurstypen som ska riktas | Application Insights, Log Analytics eller Azure-first |
| `Resources` | En uppsättning resurser för att hämta måttvärdet från | MyApp1 |
| `Time Range` | Tidsfönstret för att visa loggdiagrammet | Sista timmen, Senaste 24 timmarna, etc. |
| `Visualization` | Visualiseringen som ska användas | Område, Bar, Linje, Cirkel, Punkt, Tid, bar kategorisk |
| `Size` | Den vertikala storleken på kontrollen | Små, medelstora, stora eller fulla |
| `Color palette` | Den färgpalett som ska användas i diagrammet. Ignoreras i multimetrid eller segmenterat läge. | Blå, grön, röd, etc. |
| `Legend` | Aggregeringsfunktionen som ska användas för förklaringen | Summa eller medelvärde av värden eller Max, Min, Först, Sista värde |
| `Query` | Alla KQL-frågor som returnerar data i det format som förväntas av diagramvisualiseringen | _begär \| begäranden om begäranden om make-series Requests = count() default = 0 på tidsstämpel från sedan(1d) till nu() steg 1h_ |

### <a name="adding-a-metric-chart"></a>Lägga till ett måttdiagram

1. Växla arbetsboken till redigeringsläge genom att klicka på alternativet **Redigera** verktygsfält.
2. Använd länken **Lägg till mått för** att lägga till en måttkontroll i arbetsboken.
3. Välj en resurstyp (till exempel Lagringskonto), de resurser som ska riktas, det måttnamnområde och det namn och det aggregering som ska användas.
4. Ange andra parametrar om det behövs – som tidsintervall, delad, visualisering, storlek och färgpalett.

![Skärmbild av måttdiagram i redigeringsläge](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parametrar för metriskt diagram

| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `Resource Type` | Resurstypen som ska riktas | Lagring eller virtuell dator. |
| `Resources` | En uppsättning resurser för att hämta måttvärdet från | MyStorage1 (På väg till) |
| `Namespace` | Namnområdet med måttet | Lagring > Blob |
| `Metric` | Måttet som ska visualiseras | Lagring > Blob >-transaktioner |
| `Aggregation` | Aggregeringsfunktionen som ska tillämpas på måttet | Summa, antal, medelvärde, etc. |
| `Time Range` | Tidsfönstret för att visa måttet i | Sista timmen, Senaste 24 timmarna, etc. |
| `Visualization` | Visualiseringen som ska användas | Område, Bar, Linje, Punkt, Rutnät |
| `Split By` | Du kan också dela upp måttet på en dimension | Transaktioner efter geotyp |
| `Size` | Den vertikala storleken på kontrollen | Små, medelstora eller stora |
| `Color palette` | Den färgpalett som ska användas i diagrammet. Ignoreras om `Split by` parametern används | Blå, grön, röd, etc. |

## <a name="grids"></a>Rutnät

Rutnät eller tabeller är ett vanligt sätt att presentera data för användare. Arbetsböcker gör det möjligt för användare att individuellt formatera kolumnerna i rutnätet för att tillhandahålla ett omfattande användargränssnitt för sina rapporter.

Exemplet nedan visar ett rutnät som kombinerar ikoner, värmekartor och sparkfält för att presentera komplex information. Arbetsboken innehåller också sortering, en sökruta och en go-to-analytics-knapp.

![Skärmbild av loggbaserat rutnät](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Lägga till ett loggbaserat rutnät

1. Växla arbetsboken till redigeringsläge genom att klicka på alternativet **Redigera** verktygsfält.
2. Använd länken **Lägg till fråga** om du vill lägga till en loggfrågekontroll i arbetsboken.
3. Välj frågetypen som **Log**, resurstyp (till exempel Application Insights) och de resurser som ska riktas.
4. Använd Frågeredigeraren för att ange KQL för din analys (till exempel virtuella datorer med minne under ett tröskelvärde)
5. Ställa in visualiseringen på **rutnät**
6. Ställ in andra parametrar om det behövs - som tidsintervall, storlek, färgpalett och förklaring.

![Skärmbild av loggbaserad rutnätsfråga](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Ikoner

Paneler är ett mycket användbart sätt att presentera sammanfattningsdata i arbetsböcker. Bilden nedan visar ett vanligt användningsfall för paneler - sammanfattning av appnivå ovanpå ett detaljerat rutnät.

![Skärmbild av sammanfattningsvy för paneler](./media/workbooks-visualizations/tiles-summary.png)

Arbetsbokspaneler stöder en rubrik, underrubrik, stor text, ikoner, måttbaserade övertoningar, sparklinje/staplar, sidfot, etc.

### <a name="adding-a-tile"></a>Lägga till en panel

1. Växla arbetsboken till redigeringsläge genom att klicka på alternativet _Redigera_ verktygsfält.
2. Använd länken **Lägg till fråga** om du vill lägga till en loggfrågekontroll i arbetsboken. 
3. Välj frågetypen som **Log**, resurstyp (till exempel Application Insights) och de resurser som ska riktas.
4. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Ange storlek till **full**
6. Ställa in visualiseringen på **paneler**
7. Klicka på knappen **Panelinställningar** för att öppna inställningsfönstret
8. I **Panelfält**anger du:
    * Titel:`name`
    * Vänster: `Requests`, Renderer: `Big Number`, `Green to Red`Färgpalett: , Min Värde:`0`
    * Botten:`appName`
9. Klicka på knappen **Spara och stäng** längst ned i fönstret.

![Skärmbild av sammanfattningsvy för paneler](./media/workbooks-visualizations/tile-settings.png)

Så här ser panelerna ut i läsläge:

![Skärmbild av sammanfattningsvy för paneler](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Träd

Arbetsböcker stöder hierarkiska vyer via trädrutnät. Träd tillåter vissa rader att kunna expanderas till nästa nivå för en detaljerad upplevelse.

Exemplet nedan visar behållarhälsomått (arbetsuppsättningsstorlek) som visualiseras som ett trädrutnät. De översta noderna här är AKS-noder (Azure Kubernetes Service), nästa nivå är poddar och den slutliga nivån är behållare. Observera att du fortfarande kan formatera kolumnerna som i ett rutnät (heatmap, ikoner, länk). Den underliggande datakällan i det här fallet är en Log Analytics-arbetsyta med AKS-loggar.

![Skärmbild av sammanfattningsvy för paneler](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Lägga till ett trädrutnät
1. Växla arbetsboken till redigeringsläge genom att klicka på alternativet _Redigera_ verktygsfält.
2. Använd länken **Lägg till fråga** om du vill lägga till en loggfrågekontroll i arbetsboken. 
3. Välj frågetypen som **Log**, resurstyp (till exempel Application Insights) och de resurser som ska riktas.
4. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Ställa in visualiseringen på **rutnät**
6. Klicka på knappen **Kolumninställningar** för att öppna inställningsfönstret
7. Ange: **Tree/Group By Settings**
    * Trädtyp:`Parent/Child`
    * Id-fält:`Id`
    * Fältet Överordnat ID:`ParentId`
    * Visa expandern på:`Name`
    * Expandera den översta nivån i trädet:`checked`
8. I avsnittet _Kolumner_ högst upp anger du:
    * _Id_ - Kolumn renderare:`Hidden`
    * _Överordnad ID_ - Kolumnåtergivning:`Hidden`
    * _Begäranden_ - Kolumnåtergivning: `Bar` `Blue`, Färg: , Lägsta värde:`0`
9. Klicka på knappen _Spara och stäng_ längst ned i fönstret.    

![Skärmbild av sammanfattningsvy för paneler](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Trädinställningar

| Inställning | Förklaring |
|:------------- |:-------------|
| `Id Field` | Det unika ID:et för varje rad i rutnätet |
| `Parent Id Field` | Id för den överordnade raden |
| `Show the expander on` | Den kolumn som trädet expanderar på. Det är vanligt att trädrutnät döljer sitt id- och överordnad id-fält eftersom de inte är särskilt läsbara. I stället visas expandern på ett fält med ett mer läsbart värde - som namnet på entiteten |
| `Expand the top level of the tree` | Om markerad expanderas trädrutnätet på den översta nivån. Användbart om du vill visa mer information som standard |

## <a name="graphs"></a>Grafer

Arbetsböcker stöder visualisering av godtyckliga diagram baserat på data från loggar för att visa relationerna mellan övervakningsentiteter.

Diagrammet nedan visar data som flödar in/ut från en dator via olika portar till/från externa datorer. Den färgas efter typ (dator kontra port kontra extern IP) och kantstorlekarna motsvarar mängden data som flödar däremellan. Underliggande data kommer från KQL-fråga som riktar sig till VM-anslutningar.

![Skärmbild av sammanfattningsvy för paneler](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Lägga till ett diagram
1. Växla arbetsboken till redigeringsläge genom att klicka på alternativet _Redigera_ verktygsfält.
2. Använd länken **Lägg till fråga** om du vill lägga till en loggfrågekontroll i arbetsboken. 
3. Välj frågetypen som **Log**, resurstyp (till exempel Application Insights) och de resurser som ska riktas.
4. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. Ställa in visualiseringen på **Diagram**
8. Klicka på knappen **Diagraminställningar** för att öppna inställningsfönstret
9. _Ange:_
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. _Ange:_
    * _Överst innehåll_ `Name`- Använd kolumn: , Kolumnåtergivning:`Text`
    * _Centerinnehåll_- Använd `Calls`kolumn: , `Big Number`Kolumnåtergivning: , Färgpalett:`None`
    * _Nedersta innehållet_ `Kind`- Använd kolumn: , Kolumnåtergivning:`Text`
10. Klicka på knappen _Spara och stäng_ längst ned i fönstret.

![Skärmbild av sammanfattningsvy för paneler](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Nästa steg

* [Distribuera](workbooks-automate.md) arbetsböcker med Azure Resource Manager.
* [Kontrollera](workbooks-access-control.md) och dela åtkomst till arbetsboksresurserna.
