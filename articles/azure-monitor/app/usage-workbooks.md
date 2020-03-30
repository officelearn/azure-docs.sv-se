---
title: Skapa interaktiva rapporter med Azure Monitor-arbetsböcker | Microsoft-dokument
description: Förenkla komplex rapportering med färdiga och anpassade parameteriserade arbetsböcker
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 15543f7f761c707e8eff8e0cc0a0e4532475ddf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671009"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Skapa interaktiva rapporter med Azure Monitor-arbetsböcker

Arbetsböcker kombinerar text, [Analytics-frågor,](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)Azure-mått och parametrar i avancerade interaktiva rapporter. Arbetsböcker kan redigeras av andra teammedlemmar som har åtkomst till samma Azure-resurser.

Arbetsböcker är användbara för scenarier som:

* Utforska användningen av din app när du inte känner till statistiken över intresse i förväg: antal användare, kvarhållningsfrekvens, konverteringsfrekvens, etc. Till skillnad från andra verktyg för användningsanalys kan du med arbetsböcker kombinera flera typer av visualiseringar och analyser, vilket gör dem bra för den här typen av frihandsutforskning.
* Förklara för ditt team hur en nyutgiven funktion fungerar genom att visa användarantal för viktiga interaktioner och andra mätvärden.
* Dela resultaten av ett A/B-experiment i din app med andra medlemmar i ditt team. Du kan förklara målen för experimentet med text och sedan visa varje användningsmått och Analytics-fråga som används för att utvärdera experimentet, tillsammans med tydliga länktexter för om varje mått var över- eller undermål.
* Rapportera hur ett avbrott påverkar användningen av din app, kombinerar data, textförklaring och en diskussion om nästa steg för att förhindra avbrott i framtiden.

## <a name="starting-with-a-template-or-saved-workbook"></a>Börja med en mall eller sparad arbetsbok

En arbetsbok består av avsnitt som består av oberoende redigerbara diagram, tabeller, text och inmatningskontroller. För att bättre förstå arbetsböcker är det bäst att öppna en. 

Välj **Arbetsböcker** på menyn till vänster från appens programstatistik.

![Skärmbild av navigering till arbetsböcker](./media/usage-workbooks/001-workbooks.png)

Då startas ett arbetsboksgalleri med ett antal fördefinierade arbetsböcker som hjälper dig att komma igång.

![Skärmbild av arbetsboksgalleriet](./media/usage-workbooks/002-workbook-gallery.png)

Vi börjar med **standardmallen**, som finns under rubriken **Snabbstart**.

![Skärmbild av arbetsboksgalleriet](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Redigera, ordna om, klona och ta bort arbetsboksavsnitt

Arbetsböcker har två lägen: **redigeringsläge**och **läsläge**. När standardarbetsboken startas öppnas den i **redigeringsläge**. Detta visar allt innehåll i arbetsboken, inklusive alla steg och parametrar som annars är dolda. **Läsläge** visar en förenklad rapportformatvy. Detta gör att du kan abstrakt bort komplexiteten som gick till att skapa en rapport samtidigt som den underliggande mekaniken bara några klick bort när det behövs för ändring.

![Programinsikter Arbetsböcker avsnitt redigering kontroller](./media/usage-workbooks/editing-controls-new.png)

1. När du är klar med redigeringen av ett avsnitt klickar du på **Klar redigering** i det nedre vänstra hörnet av avsnittet.

2. Om du vill skapa en dubblett av ett avsnitt klickar du på ikonen **Klona det här avsnittet.** Att skapa dubblettavsnitt är ett bra sätt att iterera på en fråga utan att förlora tidigare iterationer.

3. Om du vill flytta ett avsnitt i en arbetsbok klickar du på ikonen **Flytta uppåt** eller **Flytta ned.**

4. Om du vill ta bort ett avsnitt permanent klickar du på ikonen **Ta bort.**

## <a name="adding-text-and-markdown-sections"></a>Lägga till text och Markdown-avsnitt

Genom att lägga till rubriker, förklaringar och kommentarer i arbetsböckerna kan du förvandla en uppsättning tabeller och diagram till en berättelse. Textavsnitt i arbetsböcker stöder [syntaxen markdown](https://daringfireball.net/projects/markdown/) för textformatering, till exempel rubriker, fetstil, kursiv stil och punktlistor.

Om du vill lägga till ett textavsnitt i arbetsboken använder du knappen **Lägg till text** längst ned i arbetsboken eller längst ned i ett avsnitt.

## <a name="adding-query-sections"></a>Lägga till frågeavsnitt

![Avsnittet Fråga i Arbetsböcker](./media/usage-workbooks/analytics-section-new.png)

Om du vill lägga till frågeavsnitt i arbetsboken använder du knappen **Lägg till fråga** längst ned i arbetsboken eller längst ned i ett avsnitt.

Frågeavsnitten är mycket flexibla och kan användas för att svara på frågor som:

* Hur många undantag kastade din webbplats under samma tidsperiod som en nedgång i användningen?
* Vad var fördelningen av sidans laddningstider för användare som visar någon sida?
* Hur många användare har tittat på vissa sidor på din webbplats, men inte någon annan uppsättning sidor? Detta kan vara användbart för att förstå om du har kluster av användare som `join` använder `kind=leftanti` olika delmängder av webbplatsens funktionalitet (använd operatorn med modifieraren i [Kusto-frågespråket).](/azure/kusto/query/)

Du är inte heller bara begränsad till att fråga från kontexten för det program som du startade arbetsboken från. Du kan fråga över flera Programinsikter övervakade appar samt Log Analytics-arbetsytor så länge du har åtkomstbehörighet till dessa resurser.

Om du vill fråga från ytterligare externa Application Insights-resurser använder du appidentifieraren. **app**

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Den här frågan kombinerar begäranden från tre olika program. En app med namnet app01, en app med namnet app02 och begäranden från den lokala Application Insights-resursen.

Om du vill hämta data från en extern Log Analytics-arbetsyta använder du **arbetsytans** identifierare.

Mer information om frågor mellan resurser finns i den [officiella vägledningen](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Avancerade analytiska frågeinställningar

Varje avsnitt har sina egna avancerade inställningar, ![som är tillgängliga via inställningsikonen Application Insights Workbooks section editing controls](./media/usage-workbooks/005-settings.png) som finns till höger om knappen Lägg till **parametrar.**

![Programinsikter Arbetsböcker avsnitt redigering kontroller](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Anpassad bredd**    | Ställ in detta så att ett objekt är en godtycklig storlek, så att du kan få plats med många objekt på en enda rad så att du bättre kan ordna diagrammen och tabellerna i omfattande interaktiva rapporter.  |
   | **Villkorligt synlig** | Använd detta för att dölja steg baserat på en parameter i läsläge. |
   | **Exportera en parameter**| På så sätt kan en markerad rad i rutnätet eller diagrammet leda till att senare steg ändrar värden eller blir synliga.  |
   | **Visa fråga när du inte redigerar** | Detta visar frågan ovanför diagrammet eller tabellen även i läsläge.
   | **Visa knappen öppna i analys när du inte redigerar** | Detta lägger till den blå Analytics-ikonen i det högra hörnet av diagrammet för att tillåta åtkomst med ett klick.|

De flesta av dessa inställningar är ganska intuitiva, men för att förstå **Exportera en parameter** är det bättre att undersöka en arbetsbok som använder sig av den här funktionen.

En av de färdiga arbetsböckerna innehåller information om aktiva användare.

Det första avsnittet i arbetsboken baseras på analytiska frågedata:

![Programinsikter Arbetsböcker avsnitt redigering kontroller](./media/usage-workbooks/003-active-users.png)

Det andra avsnittet är också baserat på analytiska frågedata, men om du väljer en rad i den första tabellen uppdateras innehållet i diagrammet interaktivt:

![Programinsikter Arbetsböcker avsnitt redigering kontroller](./media/usage-workbooks/004-active-users-trend.png)

 Detta är möjligt med hjälp av **När ett objekt är markerat exporterar du avancerade parameterinställningar** som är aktiverade i tabellens Analytics-fråga.

![Programinsikter Arbetsböcker avsnitt redigering kontroller](./media/usage-workbooks/007-settings-export.png)

Den andra analysfrågan använder sedan de exporterade värdena när en rad har valts. Om ingen rad är markerad är den som standard den rad som representerar de övergripande värdena. 

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

## <a name="adding-metrics-sections"></a>Lägga till måttavsnitt

Mätavsnitt ger dig fullständig åtkomst till att införliva Azure Monitor-måttdata i dina interaktiva rapporter. Många av de färdiga arbetsböckerna innehåller både analytiska frågedata och måttdata så att du kan dra full nytta av det bästa av båda funktionerna på ett ställe. Du har också möjlighet att hämta metriska data från resurser i någon av de prenumerationer du har åtkomst till.

Här är ett exempel på data från virtuella datorer som hämtas in i en arbetsbok för att tillhandahålla en rutnätsvisualisering av CPU-prestanda:

![Programinsikter Arbetsböcker avsnitt redigering kontroller](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Lägga till parameteravsnitt

Med arbetsboksparametrar kan du ändra värden i arbetsboken utan att behöva redigera frågan eller textavsnitten manuellt.  Detta tar bort kravet på att behöva förstå det underliggande analysfrågespråket och utökar den potentiella målgruppen för arbetsboksbaserad rapportering avsevärt.

Värdena för parametrar ersätts i fråge-, text- eller andra parameteravsnitt genom ``{parameterName}``att parameterns namn sätts in i klammerparenteser, till exempel .  Parameternamn är begränsade till liknande regler som JavaScript-identifierare, i princip alfabetiska tecken eller understreck, följt av alfanumeriska tecken eller understreck. Till exempel tillåts **a1,** men **1a** är inte tillåtet.

Parametrarna är linjära, från början av en arbetsbok och som rinner ned till senare steg.  Parametrar som deklarerats senare i en arbetsbok kan åsidosätta de parametrar som deklarerades längre upp.  På så sätt kan du även använda parametrar som använder frågor för att komma åt värdena från parametrar som definierats längre upp.  Inom själva parameterns steg är parametrarna också linjära, från vänster till höger, där parametrar till höger kan bero på en parameter som deklarerats tidigare i samma steg.
 
Det finns fyra olika typer av parametrar som för närvarande stöds:

  |         |          |
   | ---------------- |:-----|
   | **Text**    | Användaren redigerar en textruta och du kan också ange en fråga för att fylla i standardvärdet. |
   | **Listmeny** | Användaren väljer bland en uppsättning värden. |
   | **Tidsintervallväljare**| Användaren väljer bland en fördefinierad uppsättning tidsintervallvärden eller väljer från ett anpassat tidsintervall.|
   | **Resursväljare** | Användaren väljer bland de resurser som valts för arbetsboken.|

### <a name="using-a-text-parameter"></a>Använda en textparameter

Värdet som en användare skriver i textrutan ersätts direkt i frågan, utan att du kan fly eller citera. Om värdet du behöver är en sträng bör frågan ha citattecken runt parametern (som **{parameter}'**).

På så sätt kan värdet i en textruta användas var som helst. Det kan vara ett tabellnamn, kolumnnamn, funktionsnamn, operator, etc.

Textparametertypen har en inställning **Hämta standardvärde från analytics-frågan**, vilket gör att arbetsboksförfattaren kan använda en fråga för att fylla i standardvärdet för textrutan.

När standardvärdet används från en analysfråga används endast det första värdet i den första raden (rad 0, kolumn 0) som standardvärde. Därför rekommenderas att begränsa frågan så att bara en rad och en kolumn returneras. Alla andra data som returneras av frågan ignoreras. 

Oavsett vilket värde frågereturer kommer att ersättas direkt utan att fly eller citera. Om frågan inte returnerar några rader är resultatet av parametern antingen en tom sträng (om parametern inte krävs) eller odefinierad (om parametern krävs).

### <a name="using-a-dropdown"></a>Använda en listruta

Med parametertypen listruta kan du skapa en listruta som gör det möjligt att välja ett eller flera värden.

Listrutan fylls i av en analysfråga. Om frågan returnerar en kolumn är värdena i den kolumnen både **värdet** och **etiketten** i listrutan. Om frågan returnerar två kolumner är den första kolumnen **värdet**och den andra kolumnen är **den etikett** som visas i listrutan.  Om frågan returnerar tre kolumner används den tredje kolumnen för att ange standardvalet i listrutan.  Den här kolumnen kan vara vilken typ som helst, men det enklaste är att använda bool- eller numeriska typer, där 0 är falskt och 1 är sant.

 Om kolumnen är en strängtyp betraktas null/tom sträng som falsk och alla andra värden anses vara sanna. För listr=Enstaka markeringsgarde används det första värdet med ett sant värde som standardval.  För listr=Flera markeringsgarde används alla värden med ett sant värde som standardmarkerade set. Objekten i listrutan visas i vilken ordning frågan returnerade rader. 

Låt oss titta på parametrarna som finns i rapporten Aktiva användare. Klicka på redigeringssymbolen bredvid **TimeRange**.

![Programinsikter Arbetsböcker avsnitt redigering kontroller](./media/usage-workbooks/009-time-range.png)

Då startas menyalternativet Redigera parameter:

![Programinsikter Arbetsböcker avsnitt redigering kontroller](./media/usage-workbooks/010-time-range-edit.png)

Frågan använder en funktion i analytics frågespråk som kallas ett **databord** som låter dig generera en godtycklig tabell, full av innehåll, ur tomma intet! Följande analysfråga:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Genererar resultatet:

![Programinsikter Arbetsböcker avsnitt redigering kontroller](./media/usage-workbooks/011-data-table.png)

Ett mer användbart exempel är att använda en listruta för att välja från en uppsättning länder/regioner efter namn:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

Frågan visar resultaten på följande sätt:

![Nedrullning av land](./media/usage-workbooks/012-country-dropdown.png)

Rullgardinsmenyer är otroligt kraftfulla verktyg för att anpassa och skapa interaktiva rapporter.

### <a name="time-range-parameters"></a>Parametrar för tidsintervall

Även om du kan skapa en egen anpassad parameter för tidsintervall via parametertypen listruta, kan du också använda parametertypen out-of-box time range om du inte behöver samma flexibilitet. 

Parametertyper för tidsintervall har 15 standardintervall som går från fem minuter till de senaste 90 dagarna. Det finns också ett alternativ för att tillåta anpassad tidsintervallval, vilket gör att rapportoperatören kan välja explicita start- och stoppvärden för tidsintervallet.

### <a name="resource-picker"></a>Resursväljare

Parametertypen resursväljare ger dig möjlighet att begränsa rapporten till vissa typer av resurser. Ett exempel på fördefinierad arbetsbok som utnyttjar resursväljarens typ är arbetsboken **Felinsikter.**

![Nedrullning av land](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Spara och dela arbetsböcker med ditt team

Arbetsböcker sparas i en Application Insights-resurs, antingen i avsnittet **Mina rapporter** som är privat för dig eller i avsnittet **Delade rapporter** som är tillgängligt för alla med åtkomst till application insights-resursen. Om du vill visa alla arbetsböcker i resursen klickar du på knappen **Öppna** i åtgärdsfältet.

Så här delar du en arbetsbok som för närvarande finns i **Mina rapporter:**

1. Klicka på **Öppna** i åtgärdsfältet
2. Klicka på "..." bredvid arbetsboken som du vill dela
3. Klicka på **Flytta till delade rapporter**.

Om du vill dela en arbetsbok med en länk eller via e-post klickar du på **Dela** i åtgärdsfältet. Tänk på att mottagare av länken behöver åtkomst till den här resursen i Azure-portalen för att kunna visa arbetsboken. För att göra ändringar behöver mottagarna minst deltagarbehörighet för resursen.

Så här fäster du en länk till en arbetsbok på en Azure Dashboard:

1. Klicka på **Öppna** i åtgärdsfältet
2. Klicka på "..." bredvid arbetsboken som du vill fästa
3. Klicka på **Fäst vid instrumentpanelen**.

## <a name="contributing-workbook-templates"></a>Bidragande arbetsboksmallar

Har du skapat en fantastisk arbetsboksmall och vill dela den med communityn? Mer information finns i vår [GitHub-repo](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg
- Om du vill aktivera användningsupplevelser börjar du skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du redan har skickat anpassade händelser eller sidvisningar kan du utforska användningsverktygen för att ta reda på hur användarna använder tjänsten.
    - [Användare, sessioner, händelser](../../azure-monitor/app/usage-segmentation.md)
    - [Trattar](../../azure-monitor/app/usage-funnels.md)
    - [Kvarhållning](../../azure-monitor/app/usage-retention.md)
    - [Användarflöden](../../azure-monitor/app/usage-flows.md)
    - [Lägga till användarkontext](../../azure-monitor/app/usage-send-user-context.md)
