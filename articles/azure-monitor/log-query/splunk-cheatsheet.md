---
title: Splunk till Azure Log Analytics | Microsoft Docs
description: Hjälp för användare som är bekanta med Splunk lära Log Analytics-frågespråket.
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
ms.openlocfilehash: 61f0cff661c79f994a5b3c20646996f617a31b7e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52883542"
---
# <a name="splunk-to-log-analytics"></a>Splunk till Log Analytics

Den här artikeln är avsedd att hjälpa användare som är bekanta med Splunk lära Log Analytics-frågespråket. Direct jämförelser görs mellan de två att förstå viktiga skillnader och likheter där du kan utnyttja dina befintliga kunskaper.

## <a name="structure-and-concepts"></a>Strukturen och begrepp

I följande tabell jämförs begrepp och datastrukturer mellan Splunk och Log Analytics.

 | Begrepp  | Splunk | Log Analytics |  Kommentar
 | --- | --- | --- | ---
 | Distributionsenhet  | kluster |  kluster |  Log Analytics kan godtyckligt mellan kluster frågor. Splunk inte. |
 | Data cacheminnen |  buckets  |  Principer för cachelagring och kvarhållning |  Styr perioden och cachelagring för data. Den här inställningen direkt påverkar prestanda för frågor och kostnaden för distributionen. |
 | Logisk partition av data  |  index  |  databas  |  Tillåter logisk uppdelning av data. Båda implementeringar gör unioner och koppla aktuellt över dessa partitioner. |
 | Strukturerade händelsemetadata | Gäller inte | tabell |  Splunk har inte konceptet exponeras till frågespråket för händelsemetadata. Log Analytics har konceptet med en tabell som har kolumner. Varje händelse-instans är mappad till en rad. |
 | Datapost | händelse | rad |  Terminologi-ändring. |
 | Data-postattribut | Fältet |  Kolumn |  Detta är fördefinierade som en del av tabellstrukturen i Log Analytics. I Splunk har varje händelse en egen uppsättning fält. |
 | Typer | datatyp |  datatyp |  Log Analytics datatyper är mer explicit när de har angetts för kolumner. Båda har möjlighet att arbeta dynamiskt med datatyper och grovt jämföras uppsättning datatyper, inklusive stöd för JSON. |
 | Fråga och sökning  | sök | DocumentDB |  Konceptet är i princip detsamma mellan både Log Analytics och Splunk. |
 | Tidpunkt för händelsen inmatning | Systemtiden | ingestion_time() |  I Splunk hämtar varje händelse en system-tidsstämpel för den tid som händelsen har indexerats. Du kan definiera en princip med namnet ingestion_time som Exponerar en systemkolumn som kan refereras med funktionen ingestion_time() i Log Analytics. |

## <a name="functions"></a>Functions

I följande tabell anger funktioner i Log Analytics som motsvarar Splunk funktioner.

|Splunk | Log Analytics |Kommentar
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|Om     | IFF()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|övre<br>Lägre |toupper()<br>tolower()|(1) |
| Ersätt | replace() | (1)<br> Observera att även `replace()` tar tre parametrar i båda produkterna parametrarna är olika. |
| SUBSTR | substring() | (1)<br>Observera också att Splunk använder ett-baserade index. Log Analytics noterar nollbaserade index. |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| matchning | matchar regex |  (2)  |
| Regex | matchar regex | I Splunk, `regex` en operator. I Log Analytics är det en relationella operator. |
| searchmatch | == | I Splunk, `searchmatch` kan söka efter den exakta strängen.
| slumpmässig | SLUMP()<br>rand(n) | Splunks funktionen returnerar ett tal från noll till 2<sup>31</sup>-1. Log Analytics returnerar en siffra mellan 0,0 och 1,0, eller om en parameter angavs, mellan 0 och n-1.
| nu | now() | (1)
| relative_time | ToTimeSpan() | (1)<br>I Log Analytics är detsamma som Splunk's relative_time (datetimeVal, offsetVal) datetimeVal + totimespan(offsetVal).<br>Till exempel <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> blir <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) i Splunk, funktionen anropas med den `eval` operator. I Log Analytics, det används som en del av `extend` eller `project`.<br>(2) i Splunk, funktionen anropas med den `eval` operator. I Log Analytics, det kan användas med den `where` operator.


## <a name="operators"></a>Operatorer

I följande avsnitt innehåller exempel på hur du använder olika operatorer mellan Splunk och Log Analytics.

> [!NOTE]
> I följande exempel fältet Splunk _regeln_ mappas till en tabell i Azure Log Analytics och Splunks standard tidsstämpel mappar till det loggar Analytics _ingestion_time()_ kolumn.

### <a name="search"></a>Search
I Splunk, kan du utelämna den `search` nyckelord och ange en ociterade sträng. I Azure Log Analytics måste du starta varje sökning med `find`en ociterade sträng är ett kolumnnamn och lookup-värdet måste vara en sträng inom citattecken. 

| |  | |
|:---|:---|:---|
| Splunk | **Sök** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Log Analytics | **hitta** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filter
Azure Log Analytics-frågor start från en tabular resultatmängd där filtret. I Splunk är filtrering standardåtgärden på det aktuella indexet. Du kan också använda `where` operator i Splunk, men det rekommenderas inte.

| |  | |
|:---|:---|:---|
| Splunk | **Sök** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Log Analytics | **där** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Hämta n händelser/rader för granskning 
Azure Log Analytics stöder också `take` som ett alias till `limit`. I Splunk, om resultaten ordnas `head` returnerar de första n resultaten. I Azure Log Analytics gränsen är inte sorterade men returnerar de första n raderna som påträffas.

| |  | |
|:---|:---|:---|
| Splunk | **HEAD** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Log Analytics | **Gränsen** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Hämtar de första n händelser/raderna ordnade efter ett fält/kolumn
Längst ned resultatet i Splunk använder `tail`. I Azure Log Analytics kan du ange skrivordning riktning med `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **HEAD** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Log Analytics | **längst upp** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Utöka resultatet anges med nya fält/kolumner
Splunk har också en `eval` som inte ska jämföras med den `eval` operator. Både den `eval` operator i Splunk och `extend` operator i Azure Log Analytics stöder endast skalära funktioner och aritmetiska operatorer.

| |  | |
|:---|:---|:---|
| Splunk | **Utvärdering** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Log Analytics | **Utöka** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Byt namn 
Azure Log Analytics använder samma operatorn att byta namn på och för att skapa ett nytt fält. Splunk har två separata operatorer `eval` och `rename`.

| |  | |
|:---|:---|:---|
| Splunk | **Byt namn på** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Log Analytics | **Utöka** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Formatet resultat/projektion
Splunk verkar inte ha en operatör som liknar `project-away`. Du kan använda Gränssnittet för att filtrera bort fält.

| |  | |
|:---|:---|:---|
| Splunk | **Tabell** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Log Analytics | **Projekt**<br>**projekt-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Sammansättning
Se den [aggregeringar i Log Analytics-frågor](aggregations.md) för olika sammansättningsfunktioner.

| |  | |
|:---|:---|:---|
| Splunk | **Statistik** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Log Analytics | **Sammanfatta** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Slå ihop
Koppling i Splunk har betydande begränsningar. Underfrågan har en gräns på 10000 resultat (anges i distributionskonfigurationsfilen) och det ett begränsat antal join varianter.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias | ansluta till Client.Id max = 0 [Sök tidigaste = - 24h Event.Rule="150310.0” Data.Hresult= 2147221040]</code> |
| Log Analytics | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Sortera
I Splunk att sortera i stigande ordning måste du använda den `reverse` operator. Azure Log Analytics stöder också definiera var du vill placera null-värden, i början eller slutet.

| |  | |
|:---|:---|:---|
| Splunk | **Sortera** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Log Analytics | **order by-** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Flervärden är expandera
Det här är en liknande operator i både Splunk och Log Analytics.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Log Analytics | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Resultaten fasetter, intressant fält
Endast den första kolumnen visas i Log Analytics-portalen. Alla kolumner är tillgängliga via API: et.

| |  | |
|:---|:---|:---|
| Splunk | **Fält** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Log Analytics | **fasetter** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Ta bort duplicerad
Du kan använda `summarize arg_min()` i stället att vända på ordningen som post hämtar valt.

| |  | |
|:---|:---|:---|
| Splunk | **dedupliceringen** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Log Analytics | **Sammanfatta arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Nästa steg

- Gå igenom en lektion den [skriva frågor i Log Analytics](get-started-queries.md).