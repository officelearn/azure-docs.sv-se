---
title: Skapa interaktiva rapporter med Azure Monitor arbetsböcker | Microsoft docs
description: Förenkla komplexa rapportering med fördefinierade och anpassade parametriserade arbetsböcker
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 6d84ad69b067f730bbfbcad9e46bdc9ae2036ead
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64569613"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Skapa interaktiva rapporter med Azure Monitor-arbetsböcker

Arbetsböcker kombinera text, [analysfrågor](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), Azure-mått och parametrar i interaktiva rapporter. Arbetsböcker kan redigeras av andra gruppmedlemmar som har åtkomst till samma Azure-resurser.

Arbetsböcker är användbart för scenarier som:

* Utforska användningen av din app när du inte vet mätvärden i förväg: antal användare, lagringshastighet, konverteringstakten osv. Till skillnad från andra analysverktyg för användning kan arbetsböcker du kombinera flera typer av visualiseringar och analyser, vilket gör dem bra för den här typen av egen utforskning.
* Förklarar hur du utför en nyligen utgiven funktion till ditt team, av användaren som visar antal för viktiga interaktioner och andra mått.
* Dela resultatet av en A / B experimentera i din app med andra medlemmar i ditt team. Du kan förklara mål för experiment med text och sedan visa varje användningsstatistik och Analytics-fråga som används för att utvärdera experiment, tillsammans med tydliga pratbubblor för om varje mått var ovan - eller nedan-mål.
* Rapporterar effekten av ett avbrott på användningen av din app, kombinera data, text förklaring och en beskrivning av nästa steg för att förhindra avbrott i framtiden.

## <a name="starting-with-a-template-or-saved-workbook"></a>Börja med en mall eller sparat arbetsboken

En arbetsbok består av avsnitt som består av oberoende redigerbara diagram, tabeller, text, och ange kontroller. För att bättre förstå arbetsböcker, är det bäst att öppna en. 

Välj **arbetsböcker** på den vänstra menyn från inuti upplevelse Application Insights för din app.

![Skärmbild av navigering till arbetsböcker](./media/usage-workbooks/001-workbooks.png)

Detta startar ett arbetsboken galleri med ett antal fördefinierade arbetsböcker hjälper dig att komma igång.

![Skärmbild av arbetsboken galleriet](./media/usage-workbooks/002-workbook-gallery.png)

Vi börjar med den **standardmall**, som finns under rubriken **Snabbstart**.

![Skärmbild av arbetsboken galleriet](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Redigera, ändra sorteringen, kloning och ta bort arbetsbok avsnitt

Arbetsböcker har två lägen: **redigeringsläge**, och **läsläge**. När standardarbetsboken startas första gången, öppnas den i **redigeringsläge**. Detta visar allt innehåll i arbetsboken, inklusive alla steg och parametrar som annars är dolda. **Läsläge** anger formatet för förenklad rapportvyn. På så sätt kan du att abstrahera bort komplexiteten som skickades i att skapa en rapport medan du fortfarande har underliggande säkerhetsnivån endast några få klick vid behov ändras.

![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/usage-workbooks/editing-controls-new.png)

1. När du är klar redigerar ett avsnitt, klickar du på **klar redigera** i det nedre vänstra hörnet i avsnittet.

2. Om du vill skapa en dubblett av ett avsnitt, klickar du på den **klona det här avsnittet** ikon. Det är en bra sätt att iterera på en fråga utan att förlora tidigare iterationer för att skapa dubbla avsnitt.

3. Om du vill flytta upp ett avsnitt i en arbetsbok, klickar du på den **Flytta upp** eller **Flytta ned** ikon.

4. Om du vill ta bort ett avsnitt permanent, klickar du på den **ta bort** ikon.

## <a name="adding-text-and-markdown-sections"></a>Att lägga till text- och Markdown-avsnitt

Att lägga till rubriker, förklaringar och kommentarer till dina arbetsböcker omvandla hjälper dig att en uppsättning tabeller och diagram till en berättelse. Delar av texten i stödet för arbetsböcker på [markdownsyntax](https://daringfireball.net/projects/markdown/) för textformatering som rubriker, fet stil, kursiv stil och punktlistor.

Lägg till ett textavsnitt i din arbetsbok, använda den **Lägg till text** knappen längst ned i arbetsboken eller längst ned i ett avsnitt.

## <a name="adding-query-sections"></a>Att lägga till frågan avsnitt

![Frågeavsnitt i arbetsböcker](./media/usage-workbooks/analytics-section-new.png)

Lägg till frågeavsnitt i din arbetsbok, använda den **Lägg till fråga** knappen längst ned i arbetsboken eller längst ned i ett avsnitt.

Fråga avsnitt är mycket flexibel och kan användas för att besvara frågor som:

* Hur många undantag webbplatsen throw under samma tidsperiod som en nedgång i användningen?
* Vad var distributionen av sidans belastning för användare som visar några sidan?
* Hur många användare visas en uppsättning sidor på webbplatsen, men inte en annan uppsättning sidor? Detta kan vara användbar för att förstå om du har kluster av användare som använder olika delmängder av webbplatsens funktioner (Använd den `join` operator med den `kind=leftanti` modifieraren i den [Kusto-frågespråket](/azure/kusto/query/)).

Du också är inte endast begränsad till frågor från kontexten för programmet som du startade arbetsboken från. Du kan fråga över flera Application Insights övervakas appar samt Log Analytics-arbetsytor som du har åtkomstbehörighet till dessa resurser.

Att frågan från ytterligare extern Application Insights-resurser används den **app** identifierare.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Den här frågan som ska slås samman begäranden från tre olika program. En app med namnet app01, en app med namnet app02 och förfrågningar från den lokala Application Insights-resursen.

Att dra in data från en extern Log Analytics-arbetsyta används den **arbetsytan** identifierare.

Lär dig mer om frågor mellan resurser finns på den [officiella vägledning](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Avancerade inställningar för analytisk fråga

Varje avsnitt har sin egen avancerade inställningar är tillgängliga via ikonen för inställningar ![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/usage-workbooks/005-settings.png) finns till höger om den **lägga till parametrar** knappen.

![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Anpassad storlek**    | Ange det här alternativet om du vill göra ett objekt en godtycklig storlek, så att du kan anpassa många objekt på en enda rad så att du kan organisera dina diagram och tabeller i interaktiva rapporter.  |
   | **Villkorligt synligt** | Används för att dölja stegen utifrån en parameter i läsläge. |
   | **Exportera en parameter**| På så sätt kan en vald rad i rutnätet eller diagrammet kan orsaka senare steg för att ändra värden eller bli synliga.  |
   | **Visa fråga när du inte redigerar** | Detta visar frågan ovanför diagrammet eller tabellen även när du i läsläge.
   | **Visa öppna i analytics knappen när du inte redigerar** | Ikonen Analytics läggs till det högra hörnet i diagrammet för att tillåta åtkomst med ett klick.|

De flesta av de här inställningarna är ganska intuitiva, men att förstå **exportera en parameter** är det bättre att undersöka en arbetsbok som använder den här funktionen.

En av de fördefinierade arbetsböckerna ger information om aktiva användare.

Den första delen av arbetsboken baseras på analytisk fråga data:

![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/usage-workbooks/003-active-users.png)

Det andra avsnittet bygger också på analytisk fråga data, men att välja en rad i den första tabellen interaktivt uppdaterar innehållet i diagrammet:

![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/usage-workbooks/004-active-users-trend.png)

 Det är möjligt med hjälp av den **när ett objekt är markerat kan du exportera en parameter** avancerade inställningar som är aktiverade i tabellens Analytics-fråga.

![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/usage-workbooks/007-settings-export.png)

Den andra analytics-frågan använder sedan de exporterade värdena när en rad har valts. Om ingen rad väljs standard den rad som representerar de övergripande värdena. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Att lägga till mått-avsnitt

Mått avsnitt får du fullständig åtkomst att införliva Azure Monitor metrics data i dina interaktiva rapporter. Många av de fördefinierade arbetsböckerna innehåller både analytisk fråga data- och måttdata som gör att du kan dra full nytta av bäst av båda funktionerna på samma plats. Du har också möjlighet att dra in måttdata från resurser i någon av de prenumerationer som du har åtkomst till.

Här är ett exempel på VM-data att hämtas i en arbetsbok att tillhandahålla en grid visualisering av CPU-prestanda:

![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Att lägga till parametern-avsnitt

Arbetsboksparametrar kan du ändra värden i arbetsboken utan att behöva manuellt redigera avsnitten fråga eller text.  Detta undanröjer behovet av behöver förstå underliggande analytics-frågespråket och utökar avsevärt potentiella målgrupp arbetsboken-baserade rapporter.

Värdena för parametrarna ersätts i fråga, text eller andra avsnitt i parametern genom att ange namnet på parametern inom klammerparenteser, till exempel ``{parameterName}``.  Parameternamn är begränsade till liknande regler som JavaScript-identifierare, i princip alfabetiska tecken eller understreck, följt av alfanumeriska tecken eller understreck. Till exempel **a1** tillåts, men **1a** tillåts inte.

Parametrar är linjär börjar högst upp på en arbetsbok och flödar ned senare steg.  Parametrarna som har deklarerats senare i en arbetsbok kan åsidosätta de som deklarerades högre upp.  Detta kan också att använda frågor för att komma åt värden från parametrar som definierats upp ytterligare parametrar.  Inom en parameter steget själva är parametrar heller linjär, vänster till höger, där parametrar till höger kan lita på en parameter som deklarerats tidigare i det samma steget.
 
Det finns fyra olika typer av parametrar som stöds för närvarande:

  |         |          |
   | ---------------- |:-----|
   | **Text**    | användaren kommer att redigera en textruta och du kan också ange en fråga för att fylla i standardvärdet. |
   | **Nedrullningsbar listruta** | Användaren ska välja från en uppsättning värden. |
   | **Tidsintervallsväljare**| Användaren ska välja från en fördefinierad uppsättning intervallet tidsvärden eller välj från ett anpassat tidsintervall.|
   | **Resursväljare** | Användaren ska välja från resurser som valts för arbetsboken.|

### <a name="using-a-text-parameter"></a>Med hjälp av en textparameter

Det värde som en användare skriver i textrutan ersätts direkt i frågan, utan undantagstecken eller citera. Om värdet som du behöver är en sträng är frågan bör ha citattecken runt parametern (t.ex. **'{parameter}'**).

Detta gör att värdet i en textruta som ska användas var som helst. Det kan vara ett tabellnamn, kolumnnamn, funktionsnamn, operatorn osv.

Parametertypen text har en inställning **hämta standardvärdet från analysfrågan**, vilket gör att arbetsboksförfattare att använda en fråga för att fylla i standardvärdet för den textrutan.

När du använder standardvärdet från en analytics-fråga, används bara det första värdet i den första raden (rad 0, kolumn 0) som standardvärde. Därför rekommenderas det att begränsa din fråga för att returnera bara en rad och en kolumn. Andra data som returneras av frågan ignoreras. 

Det värde frågan returnerar ersätts direkt med ingen undantagstecken eller citera. Om frågan returnerar inga rader, resultatet av parametern är en tom sträng (om parametern inte är obligatoriskt) eller odefinierad (om parametern krävs).

### <a name="using-a-dropdown"></a>Med hjälp av en listruta

Parametertypen listrutan kan du skapa en listmeny-kontroll, vilket gör att valet av en eller flera värden.

Listrutan innehåller en analytics-fråga. Om frågan returnerar en kolumn, värdena i kolumnen är både den **värdet** och **etikett** i listmeny-kontroll. Om frågan returnerar två kolumner, den första kolumnen är den **värdet**, och den andra kolumnen är den **etikett** visas i listrutan.  Om frågan returnerar tre kolumner, används den 3: e kolumnen för att indikera standardvalet på den nedrullningsbara.  Den här kolumnen kan vara valfri, men det enklaste är att använda bool eller numeriska typer, där 0 är FALSKT och 1 är sant.

 Om kolumnen är en strängtyp, null/tomt sträng anses FALSKT och andra värden anses vara sant. Det första värdet med värdet SANT används för enstaka markering listrutorna som standardvalet.  Alla värden med värdet SANT används för flera val av listrutor, som standarduppsättningen valt. Objekten i listrutan visas i ordningen frågan returnerade rader. 

Låt oss titta på parametrarna som finns i rapporten aktiva användare. Klicka på Redigera symbolen bredvid **TimeRange**.

![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/usage-workbooks/009-time-range.png)

Redigera parameterobjekt startas:

![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/usage-workbooks/010-time-range-edit.png)

Frågan använder en funktion i analytics-frågespråket som kallas en **datatable** som hjälper dig att generera en godtycklig tabell fulla av innehåll, utanför tunn air! Till exempel följande analytics-frågan:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Genererar resultatet:

![Application Insights arbetsböcker avsnittet redigeringskontroller](./media/usage-workbooks/011-data-table.png)

En lämpligare exempel är att använda en listruta för att välja från en uppsättning länder/regioner efter namn:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

Frågan visar resultat på följande sätt:

![Land listrutan](./media/usage-workbooks/012-country-dropdown.png)

Listrutor är otroligt kraftfulla verktyg för att anpassa och skapa interaktiva rapporter.

### <a name="time-range-parameters"></a>Intervallet körningsparametrar

Medan du kan göra dina egna anpassade tid intervallet parametern via parametertypen listrutan, kan du också använda parametertypen för out-of-box tid intervall om du inte behöver samma grad av flexibilitet. 

Parametertyper intervall för tid har 15 intervall av förvalda som går mellan fem minuter till de senaste 90 dagarna. Det finns också ett alternativ för att tillåta val av anpassat tidsintervall, vilket gör att operatorn i rapporten att välja explicit starta och stoppa värden för tidsintervall.

### <a name="resource-picker"></a>Resursväljare

Resurstypen väljare parametern ger dig möjlighet att definiera omfattningen av rapporten för att vissa typer av resurser. Ett exempel på fördefinierade arbetsbok som utnyttjar väljare resurstypen är den **fel insikter** arbetsboken.

![Land listrutan](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Spara och dela arbetsböcker med ditt team

Arbetsböcker sparas i en Application Insights-resurs i den **Mina rapporter** avsnitt som är privata för dig eller i den **delade rapporter** avsnitt som är tillgängliga för alla med åtkomst till den Application Insights-resurs. Om du vill visa alla arbetsböcker i resursen, klickar du på den **öppna** knappen i åtgärdsfältet.

Dela en arbetsbok som för närvarande finns i **Mina rapporter**:

1. Klicka på **öppna** i Åtgärdsfältet
2. Klicka på knappen ”...” bredvid arbetsboken som du vill dela
3. Klicka på **flytta till delade rapporter**.

Om du vill dela en arbetsbok med en länk eller via e-post, klickar du på **dela** i åtgärdsfältet. Tänk på att mottagarna av länken behöver åtkomst till den här resursen i Azure portal för att visa arbetsboken. När du vill redigera mottagarna behöver minst deltagarbehörighet för resursen.

Att fästa en länk till en arbetsbok till en Azure-instrumentpanelen:

1. Klicka på **öppna** i Åtgärdsfältet
2. Klicka på knappen ”...” bredvid arbetsboken som du vill fästa
3. Klicka på **Fäst vid instrumentpanelen**.

## <a name="contributing-workbook-templates"></a>Bidra med arbetsboksmallar

Har du skapat en arbetsboksmall för fantastiska och vill dela den med communityn? Mer information, Besök vår [GitHub-lagringsplatsen](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg
- Om du vill aktivera användning upplevelser börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du redan skicka anpassade händelser eller sidvisningar, utforska användningsverktygen om du vill veta hur användarna använder din tjänst.
    - [Användare, sessioner, händelser](../../azure-monitor/app/usage-segmentation.md)
    - [Trattar](../../azure-monitor/app/usage-funnels.md)
    - [Kvarhållning](../../azure-monitor/app/usage-retention.md)
    - [Användarflöden](../../azure-monitor/app/usage-flows.md)
    - [Lägg till användarkontext](../../azure-monitor/app/usage-send-user-context.md)