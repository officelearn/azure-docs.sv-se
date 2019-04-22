---
title: Skapa interaktiva rapporter med Azure Monitor arbetsböcker | Microsoft Docs
description: Förenkla komplexa rapportering med fördefinierade och anpassade parametriserade arbetsböcker för Azure Monitor för virtuella datorer.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2019
ms.author: magoedte
ms.openlocfilehash: 90c236347380bb5d5e51db56d0f431d2659a7258
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288718"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Skapa interaktiva rapporter med Azure Monitor-arbetsböcker

Arbetsböcker kombinera text, [logga frågor](../log-query/query-language.md), mått och parametrar i interaktiva rapporter. Arbetsböcker kan redigeras av andra gruppmedlemmar som har åtkomst till samma Azure-resurser.

Arbetsböcker är användbart för scenarier som:

* Utforska användningen av den virtuella datorn när du inte vet mätvärden i förväg: CPU-användning, diskutrymme, minne, nätverksberoenden osv. Till skillnad från andra analysverktyg för användning kan arbetsböcker du kombinera flera typer av visualiseringar och analyser, vilket gör dem bra för den här typen av egen utforskning.
* Förklarar hur en nyligen etablerade virtuell dator fungerar, genom att visa mått för viktiga räknare och andra händelser till ditt team.
* Dela resultatet av ett experiment som storleksändring av den virtuella datorn med andra medlemmar i ditt team. Du kan förklara mål för experiment med text och sedan visa varje användning mått- och analysfrågor används för att utvärdera experiment, tillsammans med tydliga pratbubblor för om varje mått var ovan - eller nedan-mål.
* Rapporterar effekten av ett avbrott på användningen av den virtuella datorn, om du kombinerar data, text förklaring och en beskrivning av nästa steg att förhindra avbrott i framtiden.

Azure Monitor för virtuella datorer innehåller flera arbetsböcker för att komma igång och i följande tabell sammanfattas de.

| Arbetsbok | Beskrivning | Scope |
|----------|-------------|-------|
| Prestanda | Innehåller en anpassningsbar version av våra Top N-listan och visa diagram i en enda arbetsbok som utnyttjar alla av Log Analytics-prestandaräknare som du har aktiverat.| I stor skala |
| Prestandaräknare | En Top N diagramvy över en bred uppsättning prestandaräknare. | I stor skala |
| Anslutningar | Anslutningar tillhandahåller en detaljerad vy av inkommande och utgående anslutningar från dina övervakade virtuella datorer. | I stor skala |
| Aktiva portar | Innehåller en lista över de processer som har bundits till portarna på de övervakade virtuella datorerna och deras aktiviteter i den valda tidsramen. | I stor skala |
| Öppna portar | Innehåller antalet portar öppna på dina övervakade virtuella datorer och information om de öppnar portar. | I stor skala |
| Misslyckade anslutningar | Visa antalet misslyckade anslutningar på dina övervakade virtuella datorer, fel-utvecklingen, och om procentandelen fel ökar med tiden. | I stor skala |
| Säkerhet och granskning | En analys av din TCP/IP-trafik som rapporterar om övergripande anslutningar, skadliga anslutningar, där IP-adresslutpunkter finns globalt.  Om du vill aktivera alla funktioner behöver du aktivera Hotidentifiering. | I stor skala |
| TCP-trafik | En rankad rapport för dina övervakade virtuella datorer och deras skickade mottagna och Totalt antal nätverk trafik i ett rutnät och visas som en linje. | I stor skala |
| Jämförelse av trafik | Den här arbetsböcker kan du jämföra trender för nätverket trafik för en enskild dator eller en grupp datorer. | I stor skala |
| Prestanda | Innehåller en anpassningsbar version av våra prestandavy som utnyttjar alla av Log Analytics-prestandaräknare som du har aktiverat. | Enstaka virtuell dator | 
| Anslutningar | Anslutningar tillhandahåller en detaljerad vy av inkommande och utgående anslutningar från den virtuella datorn. | Enstaka virtuell dator |
 
## <a name="starting-with-a-template-or-saved-workbook"></a>Börja med en mall eller sparat arbetsboken

En arbetsbok består av avsnitt som består av oberoende redigerbara diagram, tabeller, text, och ange kontroller. För att bättre förstå arbetsböcker, låt oss börja med att öppna en mall och hjälper att skapa en anpassad arbetsbok. 

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **virtuella datorer**.

3. Välj en virtuell dator i listan.

4. På sidan virtuell dator i den **övervakning** väljer **Insights (förhandsversion)**.

5. På sidan VM insikter väljer **prestanda** eller **Maps** fliken och välj sedan **visa arbetsböcker** från länken på sidan. 

    ![Skärmbild av navigering till arbetsböcker](media/vminsights-workbooks/workbook-option-01.png)

6. Från listrutan, väljer **gå till galleriet** längst ned i listan.

    ![Skärmbild av arbetsboken nedrullningsbar listruta](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Startar arbetsboken galleriet med ett antal fördefinierade arbetsböcker hjälper dig att komma igång.

7. Vi börjar med den **standardmall**, som finns under rubriken **Snabbstart**.

    ![Skärmbild av arbetsboken galleriet](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Redigera arbetsboken avsnitt

Arbetsböcker har två lägen: **redigeringsläge**, och **läsläge**. När standard mall arbetsboken startas första gången, öppnas den i **redigeringsläge**. Den visar allt innehåll i arbetsboken, inklusive alla steg och parametrar som annars är dolda. **Läsläge** anger formatet för förenklad rapportvyn. Läsläge möjligt att abstrahera bort komplexiteten som skickades i att skapa en rapport medan du fortfarande har underliggande säkerhetsnivån endast några få klick vid behov ändras.

![Azure Monitor för virtuella datorer arbetsböcker redigera kontroller](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. När du är klar redigerar ett avsnitt, klickar du på **klar redigera** i det nedre vänstra hörnet i avsnittet.

2. Om du vill skapa en dubblett av ett avsnitt, klickar du på den **klona det här avsnittet** ikon. Det är en bra sätt att iterera på en fråga utan att förlora tidigare iterationer för att skapa dubbla avsnitt.

3. Om du vill flytta upp ett avsnitt i en arbetsbok, klickar du på den **Flytta upp** eller **Flytta ned** ikon.

4. Om du vill ta bort ett avsnitt permanent, klickar du på den **ta bort** ikon.

## <a name="adding-text-and-markdown-sections"></a>Att lägga till text- och Markdown-avsnitt

Att lägga till rubriker, förklaringar och kommentarer till dina arbetsböcker omvandla hjälper dig att en uppsättning tabeller och diagram till en berättelse. Delar av texten i stödet för arbetsböcker på [markdownsyntax](https://daringfireball.net/projects/markdown/) för textformatering som rubriker, fet stil, kursiv stil och punktlistor.

Lägg till ett textavsnitt i din arbetsbok, använda den **Lägg till text** knappen längst ned i arbetsboken eller längst ned i ett avsnitt.

## <a name="adding-query-sections"></a>Att lägga till frågan avsnitt

![Frågeavsnitt i arbetsböcker](media/vminsights-workbooks/005-workbook-query-section.png)

Lägg till frågeavsnitt i din arbetsbok, använda den **Lägg till fråga** knappen längst ned i arbetsboken eller längst ned i ett avsnitt.

Fråga avsnitt är mycket flexibel och kan användas för att besvara frågor som:

* Hur var CPU-användning under samma tidsperiod som en ökning av nätverkstrafiken?
* Vad var du trenden i tillgängligt diskutrymme under den senaste månaden?
* Hur många nätverksfel den virtuella datorn får de senaste två veckorna? 

Du också är inte endast begränsad till frågor från kontexten för den virtuella datorn startas från arbetsboken. Du kan fråga över flera virtuella datorer, samt Log Analytics-arbetsytor, så länge som du har åtkomstbehörighet till dessa resurser.

Att ta med data från andra Log Analytics-arbetsytor eller från ett specifikt Application Insights med hjälp av den **arbetsytan** identifierare. Mer information om frågor mellan resurser i den [officiella vägledning](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Avancerade inställningar för analytisk fråga

Varje avsnitt har sin egen avancerade inställningar är tillgängliga via inställningarna ![arbetsböcker avsnittet redigeringskontroller](media/vminsights-workbooks/006-settings.png) ikonen finns till höger om den **lägga till parametrar** knappen.

![Azure Monitor för virtuella datorer arbetsböcker redigera kontroller](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Anpassad storlek**    | Gör ett objekt en godtycklig storlek, så att du kan anpassa många objekt på en enda rad så att du kan organisera dina diagram och tabeller i interaktiva rapporter.  |
| **Villkorligt synligt** | Ange om du vill dölja stegen utifrån en parameter i läsläge. |
| **Exportera en parameter**| Tillåt en vald rad i rutnätet eller diagrammet kan orsaka senare steg för att ändra värden eller bli synliga.  |
| **Visa fråga när du inte redigerar** | Visar frågan ovanför diagrammet eller tabellen även när du i läsläge.
| **Visa öppna i analytics knappen när du inte redigerar** | Lägger till ikonen Analytics i det högra hörnet i diagrammet för att tillåta åtkomst med ett klick.|

De flesta av de här inställningarna är ganska intuitiva, men att förstå **exportera en parameter** är det bättre att undersöka en arbetsbok som använder den här funktionen.

En av de fördefinierade arbetsböckerna - **TCP-trafik**, innehåller information om anslutningsmått från en virtuell dator.

Den första delen av arbetsboken baseras på loggdata för frågan. Det andra avsnittet bygger också på loggdata för frågan, men att välja en rad i den första tabellen interaktivt uppdaterar innehållet i diagrammen:

![Azure Monitor för virtuella datorer arbetsböcker redigera kontroller](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Beteendet är möjligt med hjälp av den **när ett objekt är markerat kan du exportera en parameter** avancerade inställningar som är aktiverade i tabellfråga log.

![Azure Monitor för virtuella datorer arbetsböcker redigera kontroller](media/vminsights-workbooks/009-settings-export.png)

Den andra frågan log använder sedan de exporterade värdena när en rad har valts för att skapa en uppsättning värden som används sedan av avsnittsrubriken och diagram. Om ingen rad väljs, döljer avsnittsrubriken och diagram. 

Parametern dolda i det andra avsnittet använder exempelvis följande referens från den rad som markerats i rutnätet:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Att lägga till mått-avsnitt

Mått avsnitt får du fullständig åtkomst att införliva Azure Monitor metrics data i dina interaktiva rapporter. I Azure Monitor för virtuella datorer innehåller fördefinierade arbetsböcker vanligtvis analytisk fråga data i stället för måttdata.  Du kan välja att skapa arbetsböcker med mätvärden, så att du kan dra full nytta av bäst av båda funktionerna på samma plats. Du har också möjlighet att dra in måttdata från resurser i någon av de prenumerationer som du har åtkomst till.

Här är ett exempel på VM-data att hämtas i en arbetsbok att tillhandahålla en grid visualisering av CPU-prestanda:

![Azure Monitor för virtuella datorer arbetsböcker redigera kontroller](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Att lägga till parametern-avsnitt

Arbetsboksparametrar kan du ändra värden i arbetsboken utan att behöva manuellt redigera avsnitten fråga eller text. Detta undanröjer behovet av behöver förstå underliggande analytics-frågespråket och utökar avsevärt potentiella målgrupp arbetsboken-baserade rapporter.

Värdena för parametrarna ersätts i fråga, text eller andra avsnitt i parametern genom att ange namnet på parametern inom klammerparenteser, till exempel ``{parameterName}``. Parameternamn är begränsade till liknande regler som JavaScript-identifierare, alfabetiskt tecken eller understreck, följt av alfanumeriska tecken eller understreck. Till exempel **a1** tillåts, men **1a** tillåts inte.

Parametrar är linjär börjar högst upp på en arbetsbok och flödar ned senare steg.  Parametrarna som har deklarerats senare i en arbetsbok kan åsidosätta parametrar deklarerades tidigare. Detta kan också parametrar som använder frågor för att komma åt värden från parametrar som definierats tidigare. Inom en parameter steget själva är parametrar heller linjär, vänster till höger, där parametrar till höger kan lita på en parameter som deklarerats tidigare i det samma steget.
 
Det finns fyra olika typer av parametrar som stöds för närvarande:

|                  |      |
| ---------------- |:-----|
| **Text**    | Tillåter användare att redigera en textruta och du kan också ange en fråga för att fylla i standardvärdet. |
| **Nedrullningsbar listruta** | Gör att användaren kan välja mellan en uppsättning värden. |
| **Tidsintervallsväljare**| Gör att användaren kan välja från en fördefinierad uppsättning tidsvärden för intervallet, eller välj från ett anpassat tidsintervall.|
| **Resursväljare** | Gör att användaren kan välja bland de resurser som valts för arbetsboken.|

### <a name="using-a-text-parameter"></a>Med hjälp av en textparameter

Det värde som en användare skriver i textrutan ersätts direkt i frågan, utan undantagstecken eller citera. Om värdet som du behöver är en sträng är frågan bör ha citattecken runt parametern (t.ex. **'{parameter}'**).

Textparametern att värdet i en textruta som ska användas var som helst. Det kan vara ett tabellnamn, kolumnnamn, funktionsnamn, operatorn osv.  Parametertypen text har en inställning **hämta standardvärdet från analysfrågan**, vilket gör att arbetsboksförfattare att använda en fråga för att fylla i standardvärdet för textrutan.

När du använder standardvärdet från en loggfråga, används bara det första värdet i den första raden (rad 0, kolumn 0) som standardvärde. Därför rekommenderas det att begränsa din fråga för att returnera bara en rad och en kolumn. Andra data som returneras av frågan ignoreras. 

Det värde frågan returnerar ersätts direkt med ingen undantagstecken eller citera. Om frågan returnerar inga rader, resultatet av parametern är en tom sträng (om parametern inte är obligatoriskt) eller odefinierad (om parametern krävs).

### <a name="using-a-drop-down"></a>Med hjälp av en listruta

Parametertypen listrutan kan du skapa en kontroll med nedrullningsbar listruta, vilket gör att valet av en eller flera värden.

Listrutan innehåller en loggfråga eller JSON. Om frågan returnerar en kolumn, är värdena i kolumnen både värdet och etiketten i listrutan kontrollen. Om frågan returnerar två kolumner, den första kolumnen är värdet och den andra kolumnen är den etikett som visas i listrutan. Om frågan returnerar tre kolumner, fungerar som den tredje kolumnen visar standardvalet i den nedrullningsbara. Den här kolumnen kan vara valfri, men det enklaste är att använda bool eller numeriska typer, där 0 är FALSKT och 1 är sant.

Om kolumnen är en strängtyp, null/tomt sträng anses FALSKT och andra värden anses vara sant. Det första värdet med värdet SANT används för enstaka markering listrutorna som standardvalet.  Alla värden med värdet SANT används för flera val av listrutorna som standarduppsättningen valt. Objekten i listrutan visas i ordningen frågan returnerade rader. 

Låt oss titta på parametrarna som finns i översikt över rapporten. Klicka på Redigera symbolen bredvid **riktning**.

![Azure Monitor för virtuella datorer arbetsböcker redigera kontroller](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Detta startar den **redigera parametern** menyalternativ.

![Azure Monitor för virtuella datorer arbetsböcker redigera kontroller](media/vminsights-workbooks/012-workbook-edit-parameter.png)

JSON kan du skapa en godtycklig tabell som fylls i med innehåll. Till exempel genererar följande JSON två värden i listrutan:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

En lämpligare exempel är att använda en listruta för att välja från en uppsättning prestandaräknare efter namn:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

Frågan visar resultat på följande sätt:

![Listrutan för Perf-räknare](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Listrutor är otroligt kraftfulla verktyg för att anpassa och skapa interaktiva rapporter.

### <a name="time-range-parameters"></a>Intervallet körningsparametrar

Medan du kan göra dina egna anpassade tid intervallet parametern via parametertypen listrutan, kan du också använda parametertypen för out-of-box tid intervall om du inte behöver samma grad av flexibilitet. 

Parametertyper intervall för tid har 15 intervall av förvalda som går mellan fem minuter till de senaste 90 dagarna. Det finns också ett alternativ för att tillåta val av anpassat tidsintervall, vilket gör att operatorn i rapporten att välja explicit starta och stoppa värden för tidsintervall.

### <a name="resource-picker"></a>Resursväljare

Resurstypen väljare parametern ger dig möjlighet att definiera omfattningen av rapporten för att vissa typer av resurser. Ett exempel på en fördefinierade arbetsbok som utnyttjar väljare resurstypen är den **prestanda** arbetsboken.

![Arbetsytor listrutan](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Spara och dela arbetsböcker med ditt team

Arbetsböcker sparas i en Log Analytics-arbetsyta eller en virtuell datorresurs, beroende på hur du kommer åt arbetsböcker-galleriet. Arbetsboken kan sparas på den **Mina rapporter** avsnitt som är privata för dig eller i den **delade rapporter** avsnitt som är tillgängliga för alla med åtkomst till resursen. Om du vill visa alla arbetsböcker i resursen, klickar du på den **öppna** knappen i åtgärdsfältet.

Dela en arbetsbok som för närvarande finns i **Mina rapporter**:

1. Klicka på **öppna** i Åtgärdsfältet
2. Klicka på knappen ”...” bredvid arbetsboken som du vill dela
3. Klicka på **flytta till delade rapporter**.

Om du vill dela en arbetsbok med en länk eller via e-post, klickar du på **dela** i åtgärdsfältet. Tänk på att mottagarna av länken behöver åtkomst till den här resursen i Azure portal för att visa arbetsboken. När du vill redigera mottagarna behöver minst deltagarbehörighet för resursen.

Att fästa en länk till en arbetsbok till en Azure-instrumentpanelen:

1. Klicka på **öppna** i Åtgärdsfältet
2. Klicka på knappen ”...” bredvid arbetsboken som du vill fästa
3. Klicka på **Fäst vid instrumentpanelen**.

## <a name="next-steps"></a>Nästa steg
Läs hur du använder funktionen hälsotillstånd i [visa Azure VM-hälsa](vminsights-health.md), eller om du vill visa identifierade programberoenden, se [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md). 