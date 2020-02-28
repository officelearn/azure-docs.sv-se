---
title: Skapa interaktiva rapporter med Azure Monitor arbets böcker | Microsoft-dokument
description: Förenkla komplex rapportering med förbyggda och anpassade parameterstyrda arbets böcker
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 15543f7f761c707e8eff8e0cc0a0e4532475ddf8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671009"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Skapa interaktiva rapporter med Azure Monitor arbets böcker

Arbets böcker kombinerar text, [analys frågor](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), Azure-mått och parametrar till omfattande interaktiva rapporter. Arbets böcker kan redige ras av andra team medlemmar som har åtkomst till samma Azure-resurser.

Arbets böcker är användbara för scenarier som:

* Utforska användningen av din app när du inte känner till mått på intresse i förväg: antal användare, Kvarhållning, konverterings priser osv. Till skillnad från andra verktyg för användnings analys kan du med arbets böcker kombinera flera olika typer av visualiseringar och analyser, vilket gör dem fantastiska för den här typen av kostnads fri utforskning.
* Vi förklarar ditt team hur en nyligen utgiven funktion utförs genom att visa antalet användare för viktiga interaktioner och andra mått.
* Dela resultatet av ett A/B-experiment i din app med andra medlemmar i din grupp. Du kan förklara målen för experimentet med text, och sedan Visa alla användnings mått och analys frågor som används för att utvärdera experimentet, tillsammans med tydliga anrop för anrop för om varje mått var över eller under mål.
* Rapporterar effekten av ett avbrott i användningen av din app, kombinera data, text förklaring och en beskrivning av nästa steg för att förhindra avbrott i framtiden.

## <a name="starting-with-a-template-or-saved-workbook"></a>Starta med en mall eller Sparad arbets bok

En arbets bok består av avsnitt som består av oberoende redigerbara diagram, tabeller, text och inmatade kontroller. För att bättre förstå arbets böcker är det bäst att öppna en. 

Välj **arbets böcker** i den vänstra menyn inifrån Application Insights upplevelsen för din app.

![Skärm bild av navigering till arbets böcker](./media/usage-workbooks/001-workbooks.png)

Detta startar ett arbets boks galleri med ett antal färdiga arbets böcker som hjälper dig att komma igång.

![Skärm bild av arbets boks Galleri](./media/usage-workbooks/002-workbook-gallery.png)

Vi börjar med **standard mal len**, som finns under rubriken **snabb start**.

![Skärm bild av arbets boks Galleri](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Avsnitt om att redigera, ordna om, klona och ta bort arbets böcker

Arbets böcker har två lägen: **redigerings läge**och **läsläge**. När standard arbets boken först startas öppnas den i **redigerings läge**. Detta visar allt innehåll i arbets boken, inklusive alla steg och parametrar som annars är dolda. **Läsläge** visar en förenklad vy för rapport format. På så sätt kan du sammanställa komplexiteten som ingick i att skapa en rapport samtidigt som de underliggande Mechanics bara har några klickningar borta när de behöver ändras.

![Avsnittet redigerings kontroller för Application Insights-arbetsböcker](./media/usage-workbooks/editing-controls-new.png)

1. När du är klar med redigeringen av ett avsnitt klickar du på **klar redigering** i det nedre vänstra hörnet av avsnittet.

2. Om du vill skapa en dubblett av ett avsnitt klickar du på ikonen **klona det här avsnittet** . Att skapa duplicerade avsnitt är ett bra sätt att iterera på en fråga utan att förlora tidigare iterationer.

3. Om du vill flytta upp ett avsnitt i en arbets bok klickar du på ikonen **Flytta upp** eller **Flytta ned** .

4. Om du vill ta bort ett avsnitt permanent klickar du på ikonen **ta bort** .

## <a name="adding-text-and-markdown-sections"></a>Lägga till text-och markdown-avsnitt

Genom att lägga till rubriker, förklaringar och kommentarer i dina arbets böcker kan du omvandla en uppsättning tabeller och diagram till en rad olika. Text avsnitt i arbets böcker stöder [markdown-syntaxen](https://daringfireball.net/projects/markdown/) för textformatering, t. ex. rubrik, fet, kursiv stil och punkt listor.

Om du vill lägga till ett text avsnitt i din arbets bok använder du knappen **Lägg till text** längst ned i arbets boken eller längst ned i ett avsnitt.

## <a name="adding-query-sections"></a>Lägga till fråge avsnitt

![Avsnittet fråga i arbets böcker](./media/usage-workbooks/analytics-section-new.png)

Om du vill lägga till en fråga i din arbets bok använder du knappen **Lägg till fråga** längst ned i arbets boken eller längst ned i ett avsnitt.

Fråge avsnitt är mycket flexibla och kan användas för att besvara frågor som:

* Hur många undantag utlöste din webbplats under samma tids period när användningen skulle nekas?
* Vad är distributionen av sid inläsnings tider för användare som visar någon sida?
* Hur många användare visade en uppsättning sidor på din webbplats, men inte någon annan uppsättning sidor? Detta kan vara användbart för att förstå om du har kluster av användare som använder olika del mängder av platsens funktioner (Använd `join`-operatorn med `kind=leftanti` modifieraren i Kusto- [frågespråket](/azure/kusto/query/)).

Du är inte bara begränsad till att fråga från kontexten för det program som du startade arbets boken från. Du kan fråga över flera Application Insights övervakade appar samt Log Analytics arbets ytor så länge du har åtkomst behörighet till dessa resurser.

Om du vill fråga från ytterligare externa Application Insights resurser använder du **app** -ID.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Den här frågan kombinerar begär Anden från tre olika program. En app med namnet app01, en app med namnet app02 och begär Anden från den lokala Application Insights resursen.

Använd **arbetsyte** -ID för att hämta data från en extern Log Analytics arbets yta.

Mer information om frågor över flera resurser hittar du i den [officiella vägledningen](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Avancerade inställningar för analys fråga

Varje avsnitt har sina egna avancerade inställningar, som du kan komma åt via inställnings ikonen ![Application Insights arbetsböcker-avsnittet redigerings kontroller](./media/usage-workbooks/005-settings.png) finns till höger om knappen **Lägg till parametrar** .

![Avsnittet redigerings kontroller för Application Insights-arbetsböcker](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Anpassad bredd**    | Ange det här alternativet om du vill göra ett objekt till en godtycklig storlek, så att du kan passa många objekt på en enda rad så att du bättre kan organisera dina diagram och tabeller i interaktiva interaktiva rapporter.  |
   | **Villkorligt synlig** | Använd den här för att dölja steg baserat på en parameter i läsläge. |
   | **Exportera en parameter**| Detta gör att en vald rad i rutnätet eller diagrammet kan orsaka senare steg att ändra värden eller bli synlig.  |
   | **Visa fråga när du inte redigerar** | Detta visar frågan ovanför diagrammet eller tabellen även i läsläge.
   | **Visa knappen Öppna i Analytics när du inte redigerar** | Detta lägger till den blå analys ikonen i det högra hörnet i diagrammet för att tillåta åtkomst till ett klick.|

De flesta av dessa inställningar är ganska intuitiva, men för att förstå **export av en parameter** är det bättre att granska en arbets bok som använder den här funktionen.

En av de färdiga arbets böckerna innehåller information om aktiva användare.

Det första avsnittet i arbets boken baseras på analys frågans data:

![Avsnittet redigerings kontroller för Application Insights-arbetsböcker](./media/usage-workbooks/003-active-users.png)

Det andra avsnittet baseras också på analys frågans data, men om du markerar en rad i den första tabellen uppdateras innehållet i diagrammet interaktivt:

![Avsnittet redigerings kontroller för Application Insights-arbetsböcker](./media/usage-workbooks/004-active-users-trend.png)

 Detta är möjligt genom att använda **när ett objekt är markerat, exportera en parameter** avancerade inställningar som är aktiverade i tabellens Analytics-fråga.

![Avsnittet redigerings kontroller för Application Insights-arbetsböcker](./media/usage-workbooks/007-settings-export.png)

Den andra analys frågan använder sedan de exporterade värdena när en rad är markerad. Om ingen rad är markerad är den som standard den rad som representerar de övergripande värdena. 

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

## <a name="adding-metrics-sections"></a>Lägga till mått avsnitt

Mått avsnitt ger dig fullständig åtkomst till att införliva Azure Monitor Mät data i dina interaktiva rapporter. Många av de färdiga arbets böckerna kommer att innehålla både analytiska frågedata och mät data så att du kan dra full nytta av det bästa av båda funktionerna på ett och samma ställe. Du kan också hämta mått data från resurser i någon av de prenumerationer som du har åtkomst till.

Här är ett exempel på data för virtuella datorer som hämtas till en arbets bok för att tillhandahålla en rutnäts visualisering av processor prestanda:

![Avsnittet redigerings kontroller för Application Insights-arbetsböcker](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Lägga till parameter avsnitt

Med arbets boks parametrar kan du ändra värden i arbets boken utan att behöva redigera fråge-eller text avsnitten manuellt.  Detta tar bort behovet av att förstå det underliggande Analytics-frågespråket och utökar den potentiella mål gruppen för arbets boksbaserade rapporter.

Värdena för parametrarna ersätts i frågor, text eller andra parameter avsnitt genom att namnet på parametern anges i klamrar, t. ex. ``{parameterName}``.  Parameter namn är begränsade till liknande regler som JavaScript-identifierare, i princip alfabetiska tecken eller under streck, följt av alfanumeriska tecken eller under streck. Till exempel tillåts **a1** , men **1a** tillåts inte.

Parametrar är linjära, med början från början av en arbets bok och flödar ned till senare steg.  Parametrar som deklarerats senare i en arbets bok kan åsidosätta de som har deklarerats ytterligare.  Detta gör det också möjligt att använda parametrar som använder frågor för att få åtkomst till värdena från parametrarna som definierats ytterligare.  I själva steget för en parameter är parametrarna också linjära, vänster till höger, där parametrar till höger kan vara beroende av en parameter som har deklarerats tidigare i samma steg.
 
Det finns fyra olika typer av parametrar som stöds för närvarande:

  |         |          |
   | ---------------- |:-----|
   | **Text**    | användaren redigerar en text ruta och du kan också ange en fråga för att fylla i standardvärdet. |
   | **Listruta** | Användaren kommer att välja från en uppsättning värden. |
   | **Intervall väljare**| Användaren väljer från en fördefinierad uppsättning tidsintervalls värden eller väljer från ett anpassat tidsintervall.|
   | **Resurs väljare** | Användaren väljer bland de resurser som valts för arbets boken.|

### <a name="using-a-text-parameter"></a>Använda en text parameter

Värdet som en användar typ i text rutan ersätts direkt i frågan, utan några undantag eller citat tecken. Om värdet du behöver är en sträng ska frågan ha citat tecken runt parametern (t. ex. **{parameter}** ).

Detta gör att värdet i en text ruta kan användas var som helst. Det kan vara ett tabell namn, ett kolumn namn, ett funktions namn, en operator osv.

Text parameter typen har inställningen **Hämta standardvärde från Analytics-frågan**, vilket gör att arbets bokens författare kan använda en fråga för att fylla i standardvärdet för text rutan.

När du använder standardvärdet från en Analytics-fråga används endast det första värdet på den första raden (rad 0, kolumn 0) som standardvärdet. Vi rekommenderar därför att du begränsar frågan så att den returnerar bara en rad och en kolumn. Andra data som returneras av frågan ignoreras. 

Vilket värde som frågan returnerar ersätts direkt utan att det visas några undantag eller citat tecken. Om frågan inte returnerar några rader är resultatet av parametern antingen en tom sträng (om parametern inte krävs) eller odefinierad (om parametern krävs).

### <a name="using-a-dropdown"></a>Använda en listruta

Med den nedrullningsbara parameter typen kan du skapa en List Rute kontroll, så att du kan välja ett eller flera värden.

List rutan fylls i med en analys fråga. Om frågan returnerar en kolumn, är värdena i den kolumnen både **värdet** och **etiketten** i list rutan. Om frågan returnerar två kolumner är den första kolumnen **värdet**och den andra kolumnen är den **etikett** som visas i list rutan.  Om frågan returnerar tre kolumner används den tredje kolumnen för att ange standard valet i list rutan.  Den här kolumnen kan vara vilken typ som helst, men det enklaste är att använda bool-eller numeric-typer, där 0 är falskt och 1 är sant.

 Om kolumnen är en sträng typ anses null/tom sträng vara falskt och andra värden betraktas som sanna. För enstaka markerings List rutor används det första värdet med ett sant värde som standard val.  För List rutor med flera markeringar används alla värden med ett sant värde som standard vald uppsättning. Objekten i list rutan visas i den ordning frågan returnerade rader. 

Nu ska vi titta på de parametrar som finns i rapporten aktiva användare. Klicka på Redigera-symbolen bredvid **TimeRange**.

![Avsnittet redigerings kontroller för Application Insights-arbetsböcker](./media/usage-workbooks/009-time-range.png)

Då startas meny alternativet Redigera parameter:

![Avsnittet redigerings kontroller för Application Insights-arbetsböcker](./media/usage-workbooks/010-time-range-edit.png)

Frågan använder en funktion i Analytics-frågespråket som kallas för en **DataTable** som gör att du kan generera en godtycklig tabell, fullständig innehåll, slut på tunn luft! Till exempel följande analys fråga:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Genererar resultatet:

![Avsnittet redigerings kontroller för Application Insights-arbetsböcker](./media/usage-workbooks/011-data-table.png)

Ett mer tillämpligt exempel är att använda en listruta för att välja från en uppsättning länder/regioner efter namn:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

Frågan visar resultat enligt följande:

![Listruta för land](./media/usage-workbooks/012-country-dropdown.png)

List rutor är otroligt kraftfulla verktyg för att anpassa och skapa interaktiva rapporter.

### <a name="time-range-parameters"></a>Intervall parametrar

Även om du kan skapa en egen anpassad tids intervall parameter via den nedrullningsbara parameter typen, kan du också använda parameter typen out-of-box för tidsintervallen om du inte behöver samma grad av flexibilitet. 

Parameter typer för tidsintervall har 15 standard intervall som går från fem minuter till de senaste 90 dagarna. Det finns också ett alternativ för att tillåta val av anpassat tidsintervall, vilket gör att rapportens operatör kan välja explicita start-och stopp värden för tidsintervallet.

### <a name="resource-picker"></a>Resurs väljare

Parameter typen resurs väljare ger dig möjlighet att begränsa rapporten till vissa typer av resurser. Ett exempel på en fördefinierad arbets bok som använder resurs väljar typen är arbets boken med **felaktiga insikter** .

![Listruta för land](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Spara och dela arbets böcker med ditt team

Arbets böcker sparas i en Application Insights resurs, antingen i avsnittet **Mina rapporter** som är privat för dig eller i avsnittet **delade rapporter** som är tillgängliga för alla som har åtkomst till Application Insights resursen. Om du vill visa alla arbets böcker i resursen klickar du på knappen **Öppna** i åtgärds fältet.

Så här delar du en arbets bok som för närvarande finns i **Mina rapporter**:

1. Klicka på **Öppna** i åtgärds fältet
2. Klicka på "..." bredvid den arbets bok som du vill dela
3. Klicka på **Flytta till delade rapporter**.

Om du vill dela en arbets bok med en länk eller via e-post, klickar du på **dela** i åtgärds fältet. Tänk på att mottagare av länken behöver åtkomst till den här resursen i Azure Portal för att kunna visa arbets boken. För att göra redigeringar behöver mottagarna minst deltagar behörigheter för resursen.

Fästa en länk till en arbets bok på en Azure-instrumentpanel:

1. Klicka på **Öppna** i åtgärds fältet
2. Klicka på "..." bredvid den arbets bok som du vill fästa
3. Klicka på **Fäst vid instrumentpanelen**.

## <a name="contributing-workbook-templates"></a>Bidra till mallar för arbets böcker

Har du skapat en fantastisk arbetsboksmall och vill dela den med communityn? Mer information finns på vår [GitHub-lagrings platsen](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg
- Börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sid visningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)om du vill aktivera användnings upplevelser.
- Om du redan skickar anpassade händelser eller sid visningar, utforska användnings verktygen för att lära dig hur användarna använder tjänsten.
    - [Användare, sessioner, händelser](../../azure-monitor/app/usage-segmentation.md)
    - [Trattar](../../azure-monitor/app/usage-funnels.md)
    - [Kvarhållning](../../azure-monitor/app/usage-retention.md)
    - [Användarflöden](../../azure-monitor/app/usage-flows.md)
    - [Lägg till användar kontext](../../azure-monitor/app/usage-send-user-context.md)
