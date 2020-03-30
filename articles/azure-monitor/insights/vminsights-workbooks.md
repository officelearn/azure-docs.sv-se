---
title: Skapa interaktiva rapporter i Azure Monitor for VMs med arbetsböcker
description: Förenkla komplex rapportering med fördefinierade och anpassade parameteriserade arbetsböcker för Azure Monitor för virtuella datorer.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: a6ab126c3a5b0d2a82b17fac42dcc9e20f6aba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480461"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Skapa interaktiva rapporter i Azure Monitor for VMs med arbetsböcker

Arbetsböcker kombinerar text, [loggfrågor,](../log-query/query-language.md)mått och parametrar i avancerade interaktiva rapporter. Arbetsböcker kan redigeras av andra teammedlemmar som har åtkomst till samma Azure-resurser.

Arbetsböcker är användbara för scenarier som:

* Utforska användningen av din virtuella dator när du inte vet de mått av intresse i förväg: CPU-användning, diskutrymme, minne, nätverksberoenden, etc. Till skillnad från andra verktyg för användningsanalys kan du med arbetsböcker kombinera flera typer av visualiseringar och analyser, vilket gör dem bra för den här typen av frihandsutforskning.
* Förklara för ditt team hur en nyligen etablerad virtuell dator presterar genom att visa mått för nyckelräknare och andra logghändelser.
* Dela resultaten av ett storleksändringsexperiment av din virtuella dator med andra medlemmar i ditt team. Du kan förklara målen för experimentet med text och sedan visa varje användningsmått och analysfrågor som används för att utvärdera experimentet, tillsammans med tydliga länktexter för om varje mått var över- eller undermål.
* Rapportera effekten av ett avbrott på användningen av den virtuella datorn, kombinera data, textförklaring och en diskussion om nästa steg för att förhindra avbrott i framtiden.

I följande tabell sammanfattas de arbetsböcker som Azure Monitor för virtuella datorer innehåller för att komma igång.

| Arbetsbok | Beskrivning | Omfång |
|----------|-------------|-------|
| Prestanda | Innehåller en anpassningsbar version av vår topp-N-lista och diagramvyn i en enda arbetsbok som utnyttjar alla prestandaräknare för Log Analytics som du har aktiverat.| I stor skala |
| Prestandaräknare | En topp N-diagramvy över en bred uppsättning prestandaräknare. | I stor skala |
| Anslutningar | Anslutningar ger en djupgående vy över inkommande och utgående anslutningar från dina övervakade virtuella datorer. | I stor skala |
| Aktiva portar | Innehåller en lista över de processer som har bundits till portarna på de övervakade virtuella datorerna och deras aktivitet inom den valda tidsramen. | I stor skala |
| Öppna portar | Anger antalet portar som är öppna på dina övervakade virtuella datorer och information om dessa öppna portar. | I stor skala |
| Misslyckade anslutningar | Visa antalet misslyckade anslutningar på dina övervakade virtuella datorer, feltrenden och om procentandelen fel ökar med tiden. | I stor skala |
| Säkerhet och granskning | En analys av din TCP/IP-trafik som rapporterar om övergripande anslutningar, skadliga anslutningar, där IP-slutpunkterna finns globalt.  Om du vill aktivera alla funktioner måste du aktivera säkerhetsidentifiering. | I stor skala |
| TCP-trafik | En rankad rapport för dina övervakade virtuella datorer och deras skickade, mottagna och totala nätverkstrafik i ett rutnät och visas som en trendlinje. | I stor skala |
| Jämförelse av trafik | Med de här arbetsböckerna kan du jämföra nätverkstrafiktrender för en enda dator eller en grupp datorer. | I stor skala |
| Prestanda | Innehåller en anpassningsbar version av vår prestandavy som utnyttjar alla prestandaräknare för Log Analytics som du har aktiverat. | Enstaka virtuell dator | 
| Anslutningar | Anslutningar ger en djupgående vy över inkommande och utgående anslutningar från den virtuella datorn. | Enstaka virtuell dator |
 
## <a name="creating-a-new-workbook"></a>Skapa en ny arbetsbok

En arbetsbok består av avsnitt som består av oberoende redigerbara diagram, tabeller, text och inmatningskontroller. Om du vill bättre förstå arbetsböcker ska vi börja med att öppna en mall och gå igenom att skapa en anpassad arbetsbok. 

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **virtuella datorer**.

3. Välj en virtuell dator i listan.

4. Välj Insikter i avsnittet **Övervakning** på sidan Virtuell **dator**.

5. På sidan Vm-statistik väljer du fliken **Prestanda** eller **Kartor** och väljer sedan **Visa arbetsböcker** från länken på sidan. Välj **Gå till galleri**i listrutan .

    ![Skärmbild av listrutan arbetsbok](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Då startas arbetsboksgalleriet med ett antal fördefinierade arbetsböcker som hjälper dig att komma igång.

7. Skapa en ny arbetsbok genom att välja **Ny**.

    ![Skärmbild av arbetsboksgalleriet](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Redigera avsnitt i arbetsbok

Arbetsböcker har två lägen: **redigeringsläge**och **läsläge**. När en ny arbetsbok startas öppnas den i **redigeringsläge**. Den visar allt innehåll i arbetsboken, inklusive alla steg och parametrar som annars är dolda. **Läsläge** visar en förenklad rapportformatvy. Läsläge kan du abstrakt bort komplexiteten som gick till att skapa en rapport samtidigt som den underliggande mekaniken bara några klick bort när det behövs för ändring.

![Azure Monitor för virtuella datorer Arbetsböcker avsnitt redigering kontroller](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. När du är klar med redigeringen av ett avsnitt klickar du på **Klar redigering** i det nedre vänstra hörnet av avsnittet.

2. Om du vill skapa en dubblett av ett avsnitt klickar du på ikonen **Klona det här avsnittet.** Att skapa dubblettavsnitt är ett bra sätt att iterera på en fråga utan att förlora tidigare iterationer.

3. Om du vill flytta ett avsnitt i en arbetsbok klickar du på ikonen **Flytta uppåt** eller **Flytta ned.**

4. Om du vill ta bort ett avsnitt permanent klickar du på ikonen **Ta bort.**

## <a name="adding-text-and-markdown-sections"></a>Lägga till text och Markdown-avsnitt

Genom att lägga till rubriker, förklaringar och kommentarer i arbetsböckerna kan du förvandla en uppsättning tabeller och diagram till en berättelse. Textavsnitt i arbetsböcker stöder [syntaxen markdown](https://daringfireball.net/projects/markdown/) för textformatering, till exempel rubriker, fetstil, kursiv stil och punktlistor.

Om du vill lägga till ett textavsnitt i arbetsboken använder du knappen **Lägg till text** längst ned i arbetsboken eller längst ned i ett avsnitt.

## <a name="adding-query-sections"></a>Lägga till frågeavsnitt

![Avsnittet Fråga i Arbetsböcker](media/vminsights-workbooks/005-workbook-query-section.png)

Om du vill lägga till frågeavsnitt i arbetsboken använder du knappen **Lägg till fråga** längst ned i arbetsboken eller längst ned i ett avsnitt.

Frågeavsnitten är mycket flexibla och kan användas för att svara på frågor som:

* Hur var min CPU-användning under samma tidsperiod som en ökning av nätverkstrafiken?
* Vad var trenden i tillgängligt diskutrymme under den senaste månaden?
* Hur många nätverksanslutningsfel har min virtuella dator under de senaste två veckorna? 

Du är inte heller bara begränsad till att fråga från kontexten för den virtuella datorn som du startade arbetsboken från. Du kan fråga över flera virtuella datorer samt Log Analytics-arbetsytor, så länge du har åtkomstbehörighet till dessa resurser.

Om du vill inkludera data från andra Log Analytics-arbetsytor eller från en specifik Application Insights-app med **arbetsyteidentifieraren.** Mer information om frågor mellan resurser finns i den [officiella vägledningen](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Avancerade analytiska frågeinställningar

Varje avsnitt har sina egna avancerade inställningar, ![som är tillgängliga](media/vminsights-workbooks/006-settings.png) via ikonen för redigering av inställningar arbetsböcker avsnittsredigeringskontroller som finns till höger om knappen **Lägg till parametrar.**

![Azure Monitor för virtuella datorer Arbetsböcker avsnitt redigering kontroller](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Anpassad bredd**    | Gör ett objekt till en godtycklig storlek, så att du kan få plats med många objekt på en enda rad så att du bättre kan ordna dina diagram och tabeller i omfattande interaktiva rapporter.  |
| **Villkorligt synlig** | Ange att dölja steg baserat på en parameter i läsläge. |
| **Exportera en parameter**| Tillåt att en markerad rad i rutnätet eller diagrammet gör att senare steg ändrar värden eller blir synliga.  |
| **Visa fråga när du inte redigerar** | Visar frågan ovanför diagrammet eller tabellen även i läsläge.
| **Visa knappen öppna i analys när du inte redigerar** | Lägger till den blå Analytics-ikonen i diagrammets högra hörn för att tillåta åtkomst med ett klick.|

De flesta av dessa inställningar är ganska intuitiva, men för att förstå **Exportera en parameter** är det bättre att undersöka en arbetsbok som använder sig av den här funktionen.

En av de fördefinierade arbetsböckerna - **TCP Traffic**, innehåller information om anslutningsmått från en virtuell dator.

Det första avsnittet i arbetsboken baseras på loggfrågedata. Det andra avsnittet baseras också på loggfrågedata, men om du väljer en rad i den första tabellen uppdateras innehållet i diagrammen interaktivt:

![Azure Monitor för virtuella datorer Arbetsböcker avsnitt redigering kontroller](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Beteendet är möjligt med hjälp av **när ett objekt är markerat exporterar du avancerade parameterinställningar** som är aktiverade i tabellens loggfråga.

![Azure Monitor för virtuella datorer Arbetsböcker avsnitt redigering kontroller](media/vminsights-workbooks/009-settings-export.png)

Den andra loggfrågan använder sedan de exporterade värdena när en rad väljs för att skapa en uppsättning värden som sedan används av avsnittsrubriken och diagrammen. Om ingen rad är markerad döljs avsnittsrubriken och diagrammen. 

Den dolda parametern i det andra avsnittet använder till exempel följande referens från raden som valts i rutnätet:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Lägga till måttavsnitt

Mätavsnitt ger dig fullständig åtkomst till att införliva Azure Monitor-måttdata i dina interaktiva rapporter. I Azure Monitor för virtuella datorer innehåller de fördefinierade arbetsböckerna vanligtvis analytiska frågedata i stället för måttdata.  Du kan välja att skapa arbetsböcker med måttdata, så att du kan dra full nytta av det bästa av båda funktionerna på ett ställe. Du har också möjlighet att hämta metriska data från resurser i någon av de prenumerationer du har åtkomst till.

Här är ett exempel på data från virtuella datorer som hämtas in i en arbetsbok för att tillhandahålla en rutnätsvisualisering av CPU-prestanda:

![Azure Monitor för virtuella datorer Arbetsböcker avsnitt redigering kontroller](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Lägga till parameteravsnitt

Med arbetsboksparametrar kan du ändra värden i arbetsboken utan att behöva redigera frågan eller textavsnitten manuellt. Detta tar bort kravet på att behöva förstå det underliggande analysfrågespråket och utökar den potentiella målgruppen för arbetsboksbaserad rapportering avsevärt.

Värdena för parametrar ersätts i fråge-, text- eller andra parameteravsnitt genom ``{parameterName}``att parameterns namn sätts in i klammerparenteser, till exempel . Parameternamn är begränsade till liknande regler som JavaScript-identifierare, alfabetiska tecken eller understreck, följt av alfanumeriska tecken eller understreck. Till exempel tillåts **a1,** men **1a** är inte tillåtet.

Parametrarna är linjära, från början av en arbetsbok och som rinner ned till senare steg.  Parametrar som deklarerats senare i en arbetsbok kan åsidosätta parametrar som deklarerats tidigare. På så sätt kan du även använda parametrar som använder frågor för att komma åt värdena från parametrar som definierats tidigare. Inom själva parameterns steg är parametrarna också linjära, från vänster till höger, där parametrar till höger kan bero på en parameter som deklarerats tidigare i samma steg.
 
Det finns fyra olika typer av parametrar som för närvarande stöds:

|                  |      |
| ---------------- |:-----|
| **Text**    | Tillåter användaren att redigera en textruta och du kan också ange en fråga för att fylla i standardvärdet. |
| **Listmeny** | Tillåter användaren att välja mellan en uppsättning värden. |
| **Tidsintervallväljare**| Tillåter användaren att välja från en fördefinierad uppsättning tidsintervallvärden eller välja från ett anpassat tidsintervall.|
| **Resursväljare** | Gör att användaren kan välja bland de resurser som valts för arbetsboken.|

### <a name="using-a-text-parameter"></a>Använda en textparameter

Värdet som en användare skriver i textrutan ersätts direkt i frågan, utan att du kan fly eller citera. Om värdet du behöver är en sträng bör frågan ha citattecken runt parametern (som **{parameter}'**).

Med textparametern kan värdet i en textruta användas var som helst. Det kan vara ett tabellnamn, kolumnnamn, funktionsnamn, operator, etc.  Textparametertypen har en inställning **Hämta standardvärde från analytics-frågan**, vilket gör att arbetsboksförfattaren kan använda en fråga för att fylla i standardvärdet för den textrutan.

När standardvärdet används från en loggfråga används endast det första värdet i den första raden (rad 0, kolumn 0) som standardvärde. Därför rekommenderas att begränsa frågan så att bara en rad och en kolumn returneras. Alla andra data som returneras av frågan ignoreras. 

Oavsett vilket värde frågereturer kommer att ersättas direkt utan att fly eller citera. Om frågan inte returnerar några rader är resultatet av parametern antingen en tom sträng (om parametern inte krävs) eller odefinierad (om parametern krävs).

### <a name="using-a-drop-down"></a>Använda en rullgardinsmenyn

Med parametertypen listruta kan du skapa en listruta som gör det möjligt att välja ett eller flera värden.

Listrutan fylls i av en loggfråga eller JSON. Om frågan returnerar en kolumn är värdena i den kolumnen både värdet och etiketten i listrutan. Om frågan returnerar två kolumner är den första kolumnen värdet och den andra kolumnen är den etikett som visas i listrutan. Om frågan returnerar tre kolumner används den tredje kolumnen för att ange standardmarkeringen i listrutan. Den här kolumnen kan vara vilken typ som helst, men det enklaste är att använda bool- eller numeriska typer, där 0 är falskt och 1 är sant.

Om kolumnen är en strängtyp betraktas null/tom sträng som falsk och alla andra värden anses vara sanna. För listr=Enstaka markeringar används det första värdet med ett sant värde som standardval.  För listr=Flera markeringsgarde används alla värden med ett sant värde som standardmarkerade set. Objekten i listrutan visas i vilken ordning frågan returnerade rader. 

Låt oss titta på parametrarna som finns i rapporten Anslutningar översikt. Klicka på redigeringssymbolen **bredvid Riktning**.

![Azure Monitor för virtuella datorer Arbetsböcker avsnitt redigering kontroller](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Då startas menyalternativet **Redigera parameter.**

![Azure Monitor för virtuella datorer Arbetsböcker avsnitt redigering kontroller](media/vminsights-workbooks/012-workbook-edit-parameter.png)

Med JSON kan du skapa en godtycklig tabell som fylls med innehåll. Följande JSON genererar till exempel två värden i listrutan:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Ett mer användbart exempel är att använda en listruta för att välja från en uppsättning prestandaräknare efter namn:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

Frågan visar resultaten på följande sätt:

![Nedrullningsning av Perf-räknare](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Rullgardinsmenyn är otroligt kraftfulla verktyg för att anpassa och skapa interaktiva rapporter.

### <a name="time-range-parameters"></a>Parametrar för tidsintervall

Även om du kan skapa en egen anpassad parameter för tidsintervall via parametertypen listruta, kan du också använda parametertypen out-of-box time range om du inte behöver samma flexibilitet. 

Parametertyper för tidsintervall har 15 standardintervall som går från fem minuter till de senaste 90 dagarna. Det finns också ett alternativ för att tillåta anpassad tidsintervallval, vilket gör att rapportoperatören kan välja explicita start- och stoppvärden för tidsintervallet.

### <a name="resource-picker"></a>Resursväljare

Parametertypen resursväljare ger dig möjlighet att begränsa rapporten till vissa typer av resurser. Ett exempel på en fördefinierad arbetsbok som utnyttjar resursväljarens typ är **prestandaarbetsboken.**

![Listruta arbetsytor](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Spara och dela arbetsböcker med ditt team

Arbetsböcker sparas i en Log Analytics-arbetsyta eller en resurs för virtuella datorer, beroende på hur du kommer åt arbetsboksgalleriet. Arbetsboken kan sparas i avsnittet **Mina rapporter** som är privat för dig eller i avsnittet **Delade rapporter** som är tillgängligt för alla med åtkomst till resursen. Om du vill visa alla arbetsböcker i resursen klickar du på knappen **Öppna** i åtgärdsfältet.

Så här delar du en arbetsbok som för närvarande finns i **Mina rapporter:**

1. Klicka på **Öppna** i åtgärdsfältet
2. Klicka på "..." bredvid arbetsboken som du vill dela
3. Klicka på **Flytta till delade rapporter**.

Om du vill dela en arbetsbok med en länk eller via e-post klickar du på **Dela** i åtgärdsfältet. Tänk på att mottagare av länken behöver åtkomst till den här resursen i Azure-portalen för att kunna visa arbetsboken. För att göra ändringar behöver mottagarna minst deltagarbehörighet för resursen.

Så här fäster du en länk till en arbetsbok på en Azure Dashboard:

1. Klicka på **Öppna** i åtgärdsfältet
2. Klicka på "..." bredvid arbetsboken som du vill fästa
3. Klicka på **Fäst vid instrumentpanelen**.

## <a name="next-steps"></a>Nästa steg

- Information om hur du identifierar begränsningar och övergripande vm-prestanda finns i [Visa Azure VM-prestanda](vminsights-performance.md).

- Mer information om identifierade programberoenden finns i [Visa Azure Monitor för virtuella datorer Karta](vminsights-maps.md).
