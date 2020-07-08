---
title: Azure Monitor arbets bok visualiseringar
description: Lär dig mer om alla Azure Monitor-arbetsböckers visualiserings komponenter, inklusive text, diagram, rutnät, träd och diagram.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658038"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure Monitor arbets bok visualiseringar

Azure Monitor arbets böcker har stöd för ett antal olika typer av visualiseringar som passar dina rapporterings behov. Den här artikeln innehåller exempel på varje typ av visualisering.

## <a name="text"></a>Text

Med arbets böcker kan författarna innehålla textblock i sina arbets böcker. Texten kan vara mänsklig analys av telemetri, information som hjälper användarna att tolka dina data, avsnitts rubriker osv.

![Skärm bild av Apdex text tabell](./media/workbooks-visualizations/apdex.png)

Text läggs till via en markdown-kontroll som ger fullständig formatering.

![Skärm bild av RAW-markdown som bygger den återgivna tabellen](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Lägg till en text kontroll

1. Ändra arbets boken till redigerings läge genom att klicka på objektet **Redigera** verktygsfält.
2. Använd länken **Lägg till text** för att lägga till en text kontroll i arbets boken.
3. Lägg till markdown i kontrollen.
4. Klicka på knappen **klar redigering** för att se den formaterade texten.

> [!TIP]
> Använd det här [markdown lathund-bladet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) för att lära dig om olika formateringsalternativ.

## <a name="charts"></a>Diagram

Med arbets böcker kan övervaknings data visas som diagram. Diagram typer som stöds innehåller linje, stapel, kategoriska, område, punkt diagram, cirkel och tid. Författare kan välja att anpassa höjden, bredden, färgpaletten, förklaringen, titlar, inga data meddelanden osv.

Arbets böcker stöder diagram för både loggar och metriska data källor. 

### <a name="adding-a-log-chart"></a>Lägga till ett logg diagram

1. Ändra arbets boken till redigerings läge genom att klicka på objektet **Redigera** verktygsfält.
2. Använd länken **Lägg till fråga** för att lägga till en logg frågas kontroll i arbets boken.
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange [KQL](https://docs.microsoft.com/azure/kusto/query/) för din analys (till exempel trend för förfrågningar).
5. Ange visualiseringen till något av: **Area**, **bar**, **bar (kategoriska)**, **linje**, **cirkel**, **punkt**eller **tid**.
6. Ange andra parametrar om det behövs, till exempel tidsintervall, visualisering, storlek, färgpalett och förklaring.

![Skärm bild av logg diagram i redigerings läge](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parametrar för logg diagram

| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `Query Type` | Typ av fråga som ska användas | Logg, Azure Resource Graph osv. |
| `Resource Type` | Resurs typen som mål | Application Insights, Log Analytics eller Azure-First |
| `Resources` | En uppsättning resurser som mått värde hämtas från | MyApp1 |
| `Time Range` | Tids perioden för att Visa logg diagrammet | Senaste timmen, senaste 24 timmarna osv. |
| `Visualization` | Visualiseringen som ska användas | Yt-, stapel-, linje-, cirkel-, punkt-, Time-, bar-kategoriska |
| `Size` | Kontrollens lodräta storlek | Liten, medel, stor eller fullständig |
| `Color palette` | Den färgpalett som ska användas i diagrammet. Ignoreras i multi-Metric-eller segment läge. | Blått, grönt, rött osv. |
| `Legend` | Sammansättnings funktionen som ska användas för förklaringen | Sum eller medelvärdet av värden eller Max, min, First, sista värdet |
| `Query` | Alla KQL-frågor som returnerar data i det format som förväntas av diagrammets visualisering | _begär Anden om \| make-serien = Count () standard = 0 på tidsstämpeln från sedan (1d) till Now () steg 1H_ |

### <a name="adding-a-metric-chart"></a>Lägga till ett mått diagram

1. Ändra arbets boken till redigerings läge genom att klicka på objektet **Redigera** verktygsfält.
2. Använd länken **Lägg till mått** om du vill lägga till en mått kontroll i arbets boken.
3. Välj en resurs typ (t. ex. lagrings konto), de resurser som ska riktas till, mått namn området och namnet och vilken agg regering som ska användas.
4. Ange andra parametrar vid behov, t. ex. tidsintervall, delning för, visualisering, storlek och färgpalett.

![Skärm bild av mått diagram i redigerings läge](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Mått diagram parametrar

| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `Resource Type` | Resurs typen som mål | Lagring eller virtuell dator. |
| `Resources` | En uppsättning resurser som mått värde hämtas från | MyStorage1 |
| `Namespace` | Namn området med måttet | Lagring > BLOB |
| `Metric` | Måttet som ska visualiseras | Lagrings > BLOB-> transaktioner |
| `Aggregation` | Sammansättnings funktionen som ska tillämpas på måttet | Sum, Count, Average osv. |
| `Time Range` | Tids perioden för att Visa måttet i | Senaste timmen, senaste 24 timmarna osv. |
| `Visualization` | Visualiseringen som ska användas | Yt-, stapel-, linje-, punkt-, rutnät |
| `Split By` | Du kan också dela måttet på en dimension | Transaktioner per geo-typ |
| `Size` | Kontrollens lodräta storlek | Liten, medel eller stor |
| `Color palette` | Den färgpalett som ska användas i diagrammet. Ignoreras om `Split by` parametern används | Blått, grönt, rött osv. |

## <a name="grids"></a>Rutnät

Rutnät eller tabeller är ett vanligt sätt att presentera data för användare. Med arbets böcker kan användarna individuellt formatera kolumnerna i rutnätet för att ge ett omfattande användar gränssnitt för sina rapporter.

Exemplet nedan visar ett rutnät som kombinerar ikoner, termiska kartor och Spark-staplar för att presentera komplex information. Arbets boken innehåller också sortering, en sökruta och en go-till-Analytics-knapp.

![Skärm bild av logga baserat rutnät](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Lägga till ett logg baserat rutnät

1. Ändra arbets boken till redigerings läge genom att klicka på objektet **Redigera** verktygsfält.
2. Använd länken **Lägg till fråga** för att lägga till en logg frågas kontroll i arbets boken.
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys (till exempel virtuella datorer med minne under ett tröskelvärde)
5. Ange visualiseringen till **rutnät**
6. Ange andra parametrar om det behövs, till exempel tidsintervall, storlek, färgpalett och förklaring.

![Skärm bild av fråga om logg beroende rutnät](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Ikoner

Paneler är ett mycket användbart sätt att Visa sammanfattnings data i arbets böcker. I bilden nedan visas ett vanligt användnings fall av sammanfattningar på App-nivå ovanpå ett detaljerat rutnät.

![Skärm bild av vyn Sammanfattning av panel](./media/workbooks-visualizations/tiles-summary.png)

Stöd för arbets boks paneler visar en rubrik, under rubrik, stor text, ikoner, metriska övertoningar, Spark-linje/staplar, sidfot osv.

### <a name="adding-a-tile"></a>Lägga till en panel

1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Använd länken **Lägg till fråga** för att lägga till en logg frågas kontroll i arbets boken. 
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Ange **fullständig** storlek
6. Ange visualiseringen som **paneler**
7. Klicka på knappen **panel inställningar** för att öppna fönstret inställningar
8. I **panel fält**ställer du in:
    * Avdelning`name`
    * Left: `Requests` , renderare: `Big Number` , färgpalett: `Green to Red` , minsta värde:`0`
    * Kanten`appName`
9. Klicka på knappen **Spara och Stäng** längst ned i fönstret.

![Skärm bild av vyn Sammanfattning av panel](./media/workbooks-visualizations/tile-settings.png)

Så här kommer panelerna att se ut i läsläge:

![Skärm bild av vyn Sammanfattning av panel](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Träd

Arbets böcker har stöd för hierarkiska vyer via träd rutnät. Trees tillåter att vissa rader kan utökas till nästa nivå för att öka detalj nivån.

Exemplet nedan visar hälso mått för behållare (storleken på arbets minnet) visualiserade som ett träd rutnät. Noder på den översta nivån här är Azure Kubernetes service-noder (AKS), nästa nivå är poddar och den slutgiltiga nivån är behållare. Observera att du fortfarande kan formatera dina kolumner som i ett rutnät (termisk karta, ikoner, länk). Den underliggande data källan i det här fallet är en Log Analytics arbets yta med AKS-loggar.

![Skärm bild av vyn Sammanfattning av panel](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Lägga till ett träd rutnät
1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Använd länken **Lägg till fråga** för att lägga till en logg frågas kontroll i arbets boken. 
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
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
5. Ange visualiseringen till **rutnät**
6. Klicka på knappen för **kolumn inställningar** för att öppna fönstret inställningar
7. I avsnittet **träd/gruppera efter inställningar** längst ned anger du:
    * Träd typ:`Parent/Child`
    * ID-fält:`Id`
    * Överordnat ID-fält:`ParentId`
    * Visa expanderingen på:`Name`
    * Expandera trädets översta nivå:`checked`
8. I avsnittet _kolumner_ högst upp anger du:
    * _ID_ – kolumn åter givning:`Hidden`
    * _Överordnat ID_ -kolumn åter givning:`Hidden`
    * _Begär Anden_ -kolumn åter givning: `Bar` , färg: `Blue` , minimalt värde:`0`
9. Klicka på knappen _Spara och Stäng_ längst ned i fönstret.    

![Skärm bild av vyn Sammanfattning av panel](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Träd inställningar

| Inställningen | Förklaring |
|:------------- |:-------------|
| `Id Field` | Unikt ID för varje rad i rutnätet |
| `Parent Id Field` | ID för överordnad för den aktuella raden |
| `Show the expander on` | Den kolumn där träd expanderingen ska visas. Det är vanligt att träd rutnät döljer sitt ID och ett överordnat ID-fält eftersom de inte är särskilt läsbara. I stället visas expanderingen i ett fält med ett mer läsbart värde, som namnet på entiteten |
| `Expand the top level of the tree` | Om det här alternativet är markerat expanderas träd rutnätet på den översta nivån. Användbart om du vill visa mer information som standard |

## <a name="graphs"></a>Diagram

Arbets böcker stöder visualisering av godtyckliga grafer som baseras på data från loggar för att Visa relationerna mellan övervakning av entiteter.

Diagrammet nedan visar data som flödar in/ut ur en dator via olika portar till/från externa datorer. Det är färgat efter typ (dator vs. port vs. extern IP) och kant storlekarna motsvarar mängden data som flödar in mellan. Underliggande data kommer från KQL-frågor riktade mot virtuella dator anslutningar.

![Skärm bild av vyn Sammanfattning av panel](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Lägga till ett diagram
1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Använd länken **Lägg till fråga** för att lägga till en logg frågas kontroll i arbets boken. 
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
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
7. Ange visualiseringen som **graf**
8. Klicka på knappen **Graph-inställningar** för att öppna fönstret inställningar
9. I _Nedlayout fälten_ längst ned anger du:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. I _format inställningar för Node_ överst anger du:
    * _Topp innehåll_– Använd kolumn: `Name` , kolumn åter givning:`Text`
    * _Centrera innehåll_– Använd kolumn: `Calls` , kolumn åter givning: `Big Number` , färgpalett:`None`
    * _Nedre innehålls_användning – Använd kolumn: `Kind` , kolumn åter givning:`Text`
10. Klicka på knappen _Spara och Stäng_ längst ned i fönstret.

![Skärm bild av vyn Sammanfattning av panel](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Nästa steg

* [Distribuera](workbooks-automate.md) arbets böcker med Azure Resource Manager.
* [Kontrol lera](workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.
