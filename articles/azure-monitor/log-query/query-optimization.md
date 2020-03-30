---
title: Optimera loggfrågor i Azure Monitor
description: Metodtips för att optimera loggfrågor i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/28/2019
ms.openlocfilehash: c32731ce2de2b0f886a1e21ee8ccad3996e395eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480274"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Optimera loggfrågor i Azure Monitor
Azure Monitor Logs använder [Azure Data Explorer (ADX)](/azure/data-explorer/) för att lagra loggdata och köra frågor för att analysera dessa data. Det skapar, hanterar och underhåller ADX-kluster för dig och optimerar dem för din logganalysarbetsbelastning. När du kör en fråga optimeras den och dirigeras till lämpligt ADX-kluster som lagrar arbetsytans data. Både Azure Monitor Logs och Azure Data Explorer använder många automatiska frågeoptimeringsmekanismer. Även om automatiska optimeringar ger betydande skjuts, är de i vissa fall där du dramatiskt kan förbättra frågeprestanda. I den här artikeln beskrivs prestandaöverväganden och flera tekniker för att åtgärda dem.

De flesta tekniker är vanliga för frågor som körs direkt på Azure Data Explorer och Azure Monitor Logs, men det finns flera unika Azure Monitor Logs överväganden som diskuteras här. Fler optimeringstips för Azure Data Explorer finns i [Metodtips för frågetips](/azure/kusto/query/best-practices).

Optimerade frågor kommer att:

- Kör snabbare, minska den totala varaktigheten för frågekörningen.
- Har mindre chans att bli strypt eller avvisad.

Du bör ägna särskild uppmärksamhet åt frågor som används för återkommande och bursty användning såsom instrumentpaneler, aviseringar, Logic Apps och Power BI. Effekten av en ineffektiv fråga i dessa fall är betydande.

## <a name="query-performance-pane"></a>Prestandafönstret för frågor
När du har kört en fråga i Log Analytics klickar du på nedpilen ovanför frågeresultaten för att visa frågeresultatfönstret som visar resultatet av flera prestandaindikatorer för frågan. Dessa resultatindikatorer beskrivs var och en i följande avsnitt.

![Prestandafönstret för frågor](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Frågeprestandaindikatorer

Följande frågeprestandaindikatorer är tillgängliga för varje fråga som körs:

- [Total CPU:](#total-cpu)Total beräkning som används för att bearbeta frågan över alla beräkningsnoder. Den representerar tid som används för databehandling, tolkning och datahämtning. 

- [Data som används för bearbetad fråga](#data-used-for-processed-query): Övergripande data som användes för att bearbeta frågan. Influerad av måltabellens storlek, använt tidsintervall, använda filter och antalet refererade kolumner.

- [Tidsintervall för den bearbetade frågan:](#time-span-of-the-processed-query)Gapet mellan de nyaste och de äldsta data som användes för att bearbeta frågan. Influerad av det explicita tidsintervall som angetts för frågan.

- [Ålder för bearbetade data](#age-of-processed-data): Gapet mellan nu och de äldsta data som användes för att bearbeta frågan. Det påverkar i hög grad effektiviteten i datahämtning.

- [Antal arbetsytor](#number-of-workspaces): Hur många arbetsytor har åtkomst till under frågebearbetningen på grund av implicit eller explicit markering.

- [Antal regioner](#number-of-regions): Hur många regioner har åtkomst till under frågebearbetningen baserat på grund av implicit eller explicit val av arbetsytor. Flerregionfrågor är mycket mindre effektiva och prestandaindikatorer ger partiell täckning.

- [Parallellism](#parallelism): Anger hur mycket systemet kunde köra den här frågan på flera noder. Relevant endast för frågor som har hög CPU-förbrukning. Påverkas av användning av specifika funktioner och operatörer.


## <a name="total-cpu"></a>Totalt CPU
Den faktiska beräknings-PROCESSORn som har investerats för att bearbeta den här frågan över alla frågebearbetningsnoder. Eftersom de flesta frågor körs på ett stort antal noder, är detta vanligtvis mycket större än den varaktighet som frågan faktiskt tog att köra. 

Frågebearbetningstiden spenderas på:
- Datahämtning – hämtning av gamla data förbrukar mer tid än hämtning av de senaste data.
- Databehandling – logik och utvärdering av data. 

Förutom tid i frågebearbetningsnoderna finns det ytterligare tid som spenderas av Azure Monitor Logs för att: autentisera användaren och verifiera att de har rätt att komma åt dessa data, hitta datalagret, tolka frågan och allokera frågebearbetningen Noder. Den här gången ingår inte i frågans totala CPU-tid.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Tidig filtrering av poster innan du använder höga CPU-funktioner

Vissa av frågekommandona och funktionerna är tunga i sin CPU-förbrukning. Detta gäller särskilt för kommandon som tolkar JSON och XML eller extraherar komplexa reguljära uttryck. Sådan tolkning kan ske uttryckligen via [parse_json()](/azure/kusto/query/parsejsonfunction) eller [parse_xml()](/azure/kusto/query/parse-xmlfunction) funktioner eller implicit när du refererar till dynamiska kolumner.

Dessa funktioner förbrukar CPU i proportion till antalet rader som de bearbetar. Den mest effektiva optimeringen är att lägga till var villkor tidigt i frågan som kan filtrera bort så många poster som möjligt innan cpu-intensiva funktionen körs.

Följande frågor ger till exempel exakt samma resultat, men den andra är den överlägset mest effektiva som [det där](/azure/kusto/query/whereoperator) villkoret före tolkning utesluter många poster:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Undvik att använda utvärderade var satser

Frågor som innehåller [där](/azure/kusto/query/whereoperator) satser i en utvärderad kolumn i stället för på kolumner som finns fysiskt i datauppsättningen förlorar effektivitet. Filtrering på utvärderade kolumner förhindrar vissa systemoptimeringar när stora datamängder hanteras.
Följande frågor ger till exempel exakt samma resultat, men den andra är effektivare som [det där](/azure/kusto/query/whereoperator) villkoret refererar till inbyggd kolumn

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

### <a name="use-effective-aggregation-commands-and-dimmentions-in-summarize-and-join"></a>Använd effektiva aggregeringskommandon och dimmentioner för att sammanfatta och gå med

Medan vissa aggregeringskommandon som [max()](/azure/kusto/query/max-aggfunction), [sum()](/azure/kusto/query/sum-aggfunction), [count()](/azure/kusto/query/count-aggfunction)och [avg()](/azure/kusto/query/avg-aggfunction) har låg CPU-påverkan på grund av sin logik, är andra mer komplexa och inkluderar heuristik och uppskattningar som gör att de kan utföras effektivt. [Dcount()](/azure/kusto/query/dcount-aggfunction) använder till exempel HyperLogLog-algoritmen för att ge nära uppskattning till distinkt antal stora datauppsättningar utan att faktiskt räkna varje värde. percentilfunktionerna gör liknande approximationer med hjälp av närmaste rang percentilalgoritm. Flera av kommandona innehåller valfria parametrar för att minska deras påverkan. [Funktionen makeset()](/azure/kusto/query/makeset-aggfunction) har till exempel en valfri parameter för att definiera den maximala inställda storleken, vilket avsevärt påverkar processorn och minnet.

[Gå med](/azure/kusto/query/joinoperator?pivots=azuremonitor) och [sammanfatta](/azure/kusto/query/summarizeoperator) kommandon kan orsaka hög CPU-användning när de bearbetar en stor uppsättning data. Deras komplexitet är direkt relaterad till antalet möjliga värden, så kallade *kardinalitet*, `by` för de kolumner som använder som sammanfattande eller som kopplingsattribut. För förklaring och optimering av gå och sammanfatta, se deras dokumentation artiklar och optimering tips.

Följande frågor ger till exempel exakt samma resultat eftersom **CounterPath** alltid är en-till-en mappad till **CounterName** och **ObjectName**. Den andra är effektivare eftersom aggregeringsdimensionen är mindre:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

CPU-förbrukning kan också påverkas av var villkor eller utökade kolumner som kräver intensiv databehandling. Alla triviala strängjämnningar som [lika ==](/azure/kusto/query/datatypes-string-operators) och [börjar med](/azure/kusto/query/datatypes-string-operators) har ungefär samma CPU-effekt medan avancerade textmatchningar har större inverkan. Specifikt har [operatören](/azure/kusto/query/datatypes-string-operators) är effektivare än den [innehåller](/azure/kusto/query/datatypes-string-operators) operatören. På grund av stränghanteringstekniker är det mer effektivt att leta efter strängar som är längre än fyra tecken än korta strängar.

Följande frågor ger till exempel liknande resultat, beroende på datornamnprincip, men den andra är effektivare:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Den här indikatorn visar endast CPU från det omedelbara klustret. I frågan med flera regioner skulle den bara representera en av regionerna. I frågan om flera arbetsytor kanske den inte innehåller alla arbetsytor.


## <a name="data-used-for-processed-query"></a>Data som används för bearbetad fråga

En kritisk faktor i bearbetningen av frågan är mängden data som genomsöks och används för frågebearbetningen. Azure Data Explorer använder aggressiva optimeringar som dramatiskt minskar datavolymen jämfört med andra dataplattformar. Det finns fortfarande kritiska faktorer i frågan som kan påverka datavolymen som används.

I Azure Monitor Logs används kolumnen **TimeGenerated** som ett sätt att indexera data. Om du begränsar **TimeGenerated-värdena** till ett så smalt intervall som möjligt förbättras frågeprestanda avsevärt genom att avsevärt begränsa mängden data som måste bearbetas.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Undvik onödig användning av sök- och fackliga operatörer

En annan faktor som ökar de data som bearbetas är användningen av ett stort antal tabeller. Detta händer `search *` vanligtvis `union *` när och kommandon används. Dessa kommandon tvingar systemet att utvärdera och skanna data från alla tabeller på arbetsytan. I vissa fall kan det finnas hundratals tabeller på arbetsytan. Försök att undvika så mycket som möjligt med hjälp av "sök *" eller någon sökning utan att undersöka den till en viss tabell.

Följande frågor ger till exempel exakt samma resultat, men den sista är den överlägset mest effektiva:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Lägga till tidiga filter i frågan

En annan metod för att minska datavolymen är att ha [där](/azure/kusto/query/whereoperator) villkor tidigt i frågan. Azure Data Explorer-plattformen innehåller en cache som gör det möjligt att veta vilka partitioner som innehåller data som är relevanta för ett visst varvillkor. Om en fråga till `where EventID == 4624` exempel innehåller distribuerar den frågan endast till noder som hanterar partitioner med matchande händelser.

Följande exempelfrågor ger exakt samma resultat men det andra är effektivare:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Minska antalet kolumner som hämtas

Eftersom Azure Data Explorer är ett columnar-datalager är hämtning av varje kolumn oberoende av de andra. Antalet kolumner som hämtas direkt påverkar den totala datavolymen. Du bör bara inkludera kolumnerna i utdata som behövs genom [att summera](/azure/kusto/query/summarizeoperator) resultaten eller [projicera](/azure/kusto/query/projectoperator) de specifika kolumnerna. Azure Data Explorer har flera optimeringar för att minska antalet hämtade kolumner. Om det fastställs att en kolumn inte behövs, till exempel om den inte refereras i [sammanfatta kommandot,](/azure/kusto/query/summarizeoperator) kommer den inte att hämta den.

Den andra frågan kan till exempel bearbetas tre gånger mer data eftersom den inte behöver hämta en kolumn utan tre:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Tidsintervall för den bearbetade frågan

Alla loggar i Azure Monitor Logs partitioneras enligt kolumnen **TimeGenerated.** Antalet partitioner som används är direkt relaterade till tidsspannet. Att minska tidsintervallet är det effektivaste sättet att säkerställa en snabb frågekörning.

Tidsintervallet kan ställas in med hjälp av tidsintervallväljaren på skärmen Logganalys enligt beskrivningen i [Loggfrågeomfattning och tidsintervall i Azure Monitor Log Analytics](scope.md#time-range). Detta är den rekommenderade metoden när det valda tidsintervallet skickas till backend med hjälp av frågemetadata. 

En alternativ metod är att uttryckligen inkludera ett [varvillkor](/azure/kusto/query/whereoperator) på **TimeGenerated** i frågan. Du bör använda den här metoden eftersom den försäkrar att tidsperioden är fast, även när frågan används från ett annat gränssnitt.
Du bör se till att alla delar av frågan har **TimeGenerated-filter.** När en fråga har underfrågor som hämtar data från olika tabeller eller samma tabell, måste var och en inkludera sitt eget [vars](/azure/kusto/query/whereoperator) villkor.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Kontrollera att alla underfrågor har timegenererat filter

I följande fråga, medan **Perf-tabellen** bara genomsöks under den sista dagen, genomsöks tabellen **Heartbeat** efter all historik, vilket kan vara upp till två år:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Ett vanligt fall där ett sådant misstag inträffar är när [arg_max()](/azure/kusto/query/arg-max-aggfunction) används för att hitta den senaste förekomsten. Ett exempel:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Detta kan enkelt korrigeras genom att lägga till ett tidsfilter i den inre frågan:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Ett annat exempel på det här felet är när du utför tidsomfattningsfiltrering strax efter en [union](/azure/kusto/query/unionoperator?pivots=azuremonitor) över flera tabeller. När du utför unionen ska varje underfråga begränsas. Du kan [let](/azure/kusto/query/letstatement) använda let-sats för att säkerställa omfångskonsekvens.

Följande fråga söker till exempel igenom alla data i tabellerna *Pulsslag* och *Perf,* inte bara den sista 1 dagen:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Den här frågan bör åtgärdas på följande sätt:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Begränsningar för tidsspannvidering

Mätningen är alltid större än den angivna faktiska tiden. Om filtret i frågan till exempel är 7 dagar kan systemet skanna 7,5 eller 8,1 dagar. Detta beror på att systemet partitionerar data i segment i variabel storlek. För att säkerställa att alla relevanta poster genomsöks genomsöker den hela partitionen som kan omfatta flera timmar och till och med mer än en dag.

Det finns flera fall där systemet inte kan ge en korrekt mätning av tidsintervallet. Detta händer i de flesta fall där frågans intervall är mindre än en dag eller i frågor om flera arbetsytor.


> [!IMPORTANT]
> Den här indikatorn visar endast data som bearbetas i det omedelbara klustret. I frågan med flera regioner skulle den bara representera en av regionerna. I frågan om flera arbetsytor kanske den inte innehåller alla arbetsytor.

## <a name="age-of-processed-data"></a>Ålder på bearbetade data
Azure Data Explorer använder flera lagringsnivåer: i minnet, lokala SSD-diskar och mycket långsammare Azure Blobbar. Ju nyare data, desto högre är chansen att den lagras i en mer högpresterande nivå med mindre svarstid, vilket minskar frågevaraktigheten och processorn. Förutom själva data har systemet också en cache för metadata. Ju äldre data är, desto mindre chans är metadata i cacheminnet.

Vissa frågor kräver användning av gamla data, men det finns fall där gamla data används av misstag. Detta händer när frågor körs utan att ange tidsintervall i sina metadata och inte alla tabellreferenser innehåller filter i kolumnen **TimeGenerated.** I dessa fall kommer systemet att skanna alla data som lagras i den tabellen. När datalagringen är lång kan den täcka långa tidsintervall och därmed data som är lika gamla som datalagringsperioden.

Sådana fall kan till exempel vara:

- Inte ange tidsintervallet i Log Analytics med en underfråga som inte är begränsad. Se exempel ovan.
- Använda API utan valfria parametrar för tidsintervallet.
- Använda en klient som inte tvingar fram ett tidsintervall, till exempel Power BI-kontakten.

Se exempel och anteckningar i det pervious avsnittet eftersom de också är relevanta i detta fall.

## <a name="number-of-regions"></a>Antal regioner
Det finns flera situationer där en enskild fråga kan köras över olika regioner:

- När flera arbetsytor uttryckligen visas och de finns i olika regioner.
- När en resursomfattig fråga hämtar data och data lagras i flera arbetsytor som finns i olika regioner.

Körning mellan regioner kräver att systemet serialiserar och överför stora bitar av mellanliggande data som vanligtvis är mycket större än frågeslutresultaten i det bakre slutet av problemet. Det begränsar också systemets förmåga att utföra optimeringar, heuristik, och använda cachar.
Om det inte finns någon verklig anledning att skanna alla dessa regioner bör du justera omfattningen så att den täcker färre regioner. Om resursomfattningen minimeras men fortfarande många regioner används kan det hända på grund av felkonfiguration. Granskningsloggar och diagnostikinställningar skickas till exempel till olika arbetsytor i olika regioner eller så finns det flera konfigurationer för diagnostikinställningar. 

> [!IMPORTANT]
> När en fråga körs över flera regioner är processorn och datamätningarna inte korrekta och representerar mätningen endast i en av regionerna.

## <a name="number-of-workspaces"></a>Antal arbetsytor
Arbetsytor är logiska behållare som används för att segregera och administrera loggar data. Serverdelen optimerar placeringar på fysiska kluster inom den valda regionen.

Användning av flera arbetsytor kan bero på: 

- Där flera arbetsytor uttryckligen visas.
- När en resursomfattig fråga hämtar data och data lagras i flera arbetsytor.
 
Cross-region och cross-cluster körning av frågor kräver att systemet serialisera och överföra i serverdel stora bitar av mellanliggande data som vanligtvis är mycket större än frågan slutliga resultat. Det begränsar också systemets förmåga att utföra optimeringar, heuristik och använda cachar.

> [!IMPORTANT]
> I vissa scenarier med flera arbetsytor är processor- och datamätningarna inte korrekta och representerar mätningen endast för ett fåtal arbetsytor.

## <a name="parallelism"></a>Parallellitet
Azure Monitor Logs använder stora kluster av Azure Data Explorer för att köra frågor, och dessa kluster varierar i skala, vilket kan få upp till dussintals beräkningsnoder. Systemet skalar automatiskt kluster enligt arbetsyteplaceringslogik och kapacitet.

För att effektivt köra en fråga partitioneras den och distribueras för att beräkna noder baserat på de data som krävs för dess bearbetning. Det finns vissa situationer där systemet inte kan göra detta effektivt. Detta kan leda till en lång varaktighet av frågan. 

Frågebeteenden som kan minska parallellismen omfattar:

- Användning av serialiserings- och fönsterfunktioner som [serialiseringsoperatorn](/azure/kusto/query/serializeoperator), [next()](/azure/kusto/query/nextfunction), [prev()](/azure/kusto/query/prevfunction)och [radfunktionerna.](/azure/kusto/query/rowcumsumfunction) Tidsserier och användaranalysfunktioner kan användas i vissa av dessa fall. Ineffektiv serialisering kan också ske om följande operatorer inte används i slutet av frågan: [intervall](/azure/kusto/query/rangeoperator), [sortera](/azure/kusto/query/sortoperator), [ordning](/azure/kusto/query/orderoperator), [topp](/azure/kusto/query/topoperator), [top-hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator).
-    Användning av [dcount()](/azure/kusto/query/dcount-aggfunction) aggregering funktion tvinga systemet att ha en central kopia av de distinkta värdena. När dataskalan är hög bör du överväga att använda valfria parametrar för dcount-funktionen för att minska noggrannheten.
-    I många fall [join](/azure/kusto/query/joinoperator?pivots=azuremonitor) sänker kopplingsoperatören den totala parallellismen. Undersök shuffle join som ett alternativ när prestanda är problematiskt.
-    I resursomfattningsfrågor kan RBAC-kontroller före körningen dröja kvar i situationer där det finns ett mycket stort antal RBAC-tilldelningar. Detta kan leda till längre kontroller som skulle leda till lägre parallellitet. En fråga körs till exempel på en prenumeration där det finns tusentals resurser och varje resurs har många rolltilldelningar på resursnivå, inte i prenumerationen eller resursgruppen.
-    Om en fråga bearbetar små bitar av data, kommer dess parallellism vara låg eftersom systemet inte kommer att sprida det över många beräkningsnoder.



## <a name="next-steps"></a>Nästa steg

- [Referensdokumentation för Kusto-frågespråket](/azure/kusto/query/).
