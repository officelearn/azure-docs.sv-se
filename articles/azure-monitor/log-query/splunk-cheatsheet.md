---
title: Splunk till Azure Monitor log-fråga | Microsoft Docs
description: Hjälp för användare som är bekanta med Splunk lära Azure Monitor log-frågor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: dafafa8ff5d721034b3b10bdeb1a2fc09cd32835
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267588"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk till Azure Monitor log-fråga

Den här artikeln är avsedd att hjälpa användare som är bekanta med Splunk mer Kusto-frågespråk för att skriva loggfrågor i Azure Monitor. Direct jämförelser görs mellan de två att förstå viktiga skillnader och likheter där du kan utnyttja dina befintliga kunskaper.

## <a name="structure-and-concepts"></a>Strukturen och begrepp

I följande tabell jämförs begrepp och datastrukturer mellan Splunk och Azure Monitor-loggar.

 | Begrepp  | Splunk | Azure Monitor |  Kommentar
 | --- | --- | --- | ---
 | Distributionsenhet  | kluster |  kluster |  Azure Monitor kan godtyckligt mellan kluster frågor. Splunk inte. |
 | Data cacheminnen |  buckets  |  Principer för cachelagring och kvarhållning |  Styr perioden och cachelagring för data. Den här inställningen direkt påverkar prestanda för frågor och kostnaden för distributionen. |
 | Logisk partition av data  |  index  |  databas  |  Tillåter logisk uppdelning av data. Båda implementeringar gör unioner och koppla aktuellt över dessa partitioner. |
 | Strukturerade händelsemetadata | Gäller inte | tabell |  Splunk har inte konceptet exponeras till frågespråket för händelsemetadata. Azure Monitor-loggar har konceptet med en tabell som har kolumner. Varje händelse-instans är mappad till en rad. |
 | Data record | händelse | rad |  Terminologi-ändring. |
 | Data-postattribut | Fältet |  Kolumn |  Detta är fördefinierade som en del av tabellstrukturen i Azure Monitor. I Splunk har varje händelse en egen uppsättning fält. |
 | Typer | datatyp |  datatyp |  Azure Monitor-datatyper är mer explicit när de har angetts för kolumner. Båda har möjlighet att arbeta dynamiskt med datatyper och grovt jämföras uppsättning datatyper, inklusive stöd för JSON. |
 | Fråga och sökning  | sök | DocumentDB |  Konceptet är i princip detsamma mellan både Azure Monitor och Splunk. |
 | Tidpunkt för händelsen inmatning | Systemtiden | ingestion_time() |  I Splunk hämtar varje händelse en system-tidsstämpel för den tid som händelsen har indexerats. I Azure Monitor kan definiera du en princip med namnet ingestion_time som Exponerar en systemkolumn som kan refereras med funktionen ingestion_time(). |

## <a name="functions"></a>Functions

I följande tabell anger funktioner i Azure Monitor som motsvarar Splunk funktioner.

|Splunk | Azure Monitor |Kommentar
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|övre<br>Lägre |toupper()<br>tolower()|(1) |
| Ersätt | replace() | (1)<br> Observera att även `replace()` tar tre parametrar i båda produkterna parametrarna är olika. |
| SUBSTR | substring() | (1)<br>Observera också att Splunk använder ett-baserade index. Azure Monitor noterar nollbaserade index. |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| matchning | matchar regex |  (2)  |
| regex | matchar regex | I Splunk, `regex` en operator. I Azure Monitor är det en relationella operator. |
| searchmatch | == | I Splunk, `searchmatch` kan söka efter den exakta strängen.
| slumpmässig | SLUMP()<br>rand(n) | Splunks funktionen returnerar ett tal från noll till 2<sup>31</sup>-1. Azure Monitor ”returnerar en siffra mellan 0,0 och 1,0, eller om en parameter angavs, mellan 0 och n-1.
| nu | now() | (1)
| relative_time | totimespan() | (1)<br>I Azure Monitor är detsamma som Splunk's relative_time (datetimeVal, offsetVal) datetimeVal + totimespan(offsetVal).<br>Till exempel <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> blir <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) i Splunk, funktionen anropas med den `eval` operator. I Azure Monitor kan den användas som en del av `extend` eller `project`.<br>(2) i Splunk, funktionen anropas med den `eval` operator. I Azure Monitor, den kan användas med den `where` operator.


## <a name="operators"></a>Operatorer

I följande avsnitt innehåller exempel på hur du använder olika operatorer mellan Splunk och Azure Monitor.

> [!NOTE]
> I följande exempel fältet Splunk _regeln_ mappas till en tabell i Azure Monitor och Splunks standard tidsstämpel mappar till det loggar Analytics _ingestion_time()_ kolumn.

### <a name="search"></a>Search
I Splunk, kan du utelämna den `search` nyckelord och ange en ociterade sträng. Du måste starta varje fråga med i Azure Monitor `find`en ociterade sträng är ett kolumnnamn och lookup-värdet måste vara en sträng inom citattecken. 

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **hitta** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filter
Azure Monitor log-frågor start från ett tabellformat anger var filtret. I Splunk är filtrering standardåtgärden på det aktuella indexet. Du kan också använda `where` operator i Splunk, men det rekommenderas inte.

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **där** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Hämta n händelser/rader för granskning 
Azure Monitor log frågar också stöd för `take` som ett alias till `limit`. I Splunk, om resultaten ordnas `head` returnerar de första n resultaten. I Azure Monitor gränsen är inte sorterade men returnerar de första n raderna som påträffas.

| |  | |
|:---|:---|:---|
| Splunk | **HEAD** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Hämtar de första n händelser/raderna ordnade efter ett fält/kolumn
Längst ned resultatet i Splunk använder `tail`. I Azure Monitor kan du ange skrivordning riktning med `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **HEAD** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **längst upp** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Utöka resultatet anges med nya fält/kolumner
Splunk har också en `eval` som inte ska jämföras med den `eval` operator. Både den `eval` operator i Splunk och `extend` operator i Azure Monitor endast stöd för skalära funktioner och aritmetiska operatorer.

| |  | |
|:---|:---|:---|
| Splunk | **Utvärdering** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **Utöka** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Byt namn 
Azure Monitor använder samma operatorn att byta namn på och för att skapa ett nytt fält. Splunk har två separata operatorer `eval` och `rename`.

| |  | |
|:---|:---|:---|
| Splunk | **Byt namn på** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **Utöka** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Formatet resultat/projektion
Splunk verkar inte ha en operatör som liknar `project-away`. Du kan använda Gränssnittet för att filtrera bort fält.

| |  | |
|:---|:---|:---|
| Splunk | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Sammansättning
Se den [aggregeringar i Azure Monitor logga frågor](aggregations.md) för olika sammansättningsfunktioner.

| |  | |
|:---|:---|:---|
| Splunk | **Statistik** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **Sammanfatta** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Slå ihop
Koppling i Splunk har betydande begränsningar. Underfrågan har en gräns på 10000 resultat (anges i distributionskonfigurationsfilen) och det ett begränsat antal join varianter.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias | ansluta till Client.Id max = 0 [Sök tidigaste = - 24h Event.Rule="150310.0” Data.Hresult= 2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Sortera
I Splunk att sortera i stigande ordning måste du använda den `reverse` operator. Azure övervakar också stöder definierar var du vill placera null-värden, i början eller slutet.

| |  | |
|:---|:---|:---|
| Splunk | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **order by-** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Flervärden är expandera
Det här är en liknande operator i både Splunk och Azure Monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Resultaten fasetter, intressant fält
I Log Analytics i Azure-portalen visas endast den första kolumnen. Alla kolumner är tillgängliga via API: et.

| |  | |
|:---|:---|:---|
| Splunk | **Fält** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **fasetter** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Ta bort duplicerad
Du kan använda `summarize arg_min()` i stället att vända på ordningen som post hämtar valt.

| |  | |
|:---|:---|:---|
| Splunk | **dedupliceringen** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **Sammanfatta arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Nästa steg

- Gå igenom en lektion den [skriva loggfrågor i Azure Monitor](get-started-queries.md).