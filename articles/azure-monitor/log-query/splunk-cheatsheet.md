---
title: Splunk till Azure Monitor loggfråga | Microsoft-dokument
description: Hjälp för användare som är bekanta med Splunk i att lära sig Azure Monitor-loggfrågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 6346055f1169bfa533d5dbfe441ecf27fb0d78a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397752"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk till Azure Monitor loggfråga

Den här artikeln är avsedd att hjälpa användare som är bekanta med Splunk att lära sig Kusto-frågespråket för att skriva loggfrågor i Azure Monitor. Direkta jämförelser görs mellan de två för att förstå viktiga skillnader och även likheter där du kan utnyttja din befintliga kunskap.

## <a name="structure-and-concepts"></a>Struktur och begrepp

I följande tabell jämförs begrepp och datastrukturer mellan Splunk- och Azure Monitor-loggar.

 | Begrepp  | Splunk | Azure Monitor |  Kommentar
 | --- | --- | --- | ---
 | Driftsättningsenhet  | cluster |  cluster |  Azure Monitor tillåter godtyckliga korsklusterfrågor. Splunk gör det inte. |
 | Datacachen |  Hinkar  |  Cachelagrings- och bevarandeprinciper |  Styr perioden och cachelagringsnivån för data. Den här inställningen påverkar direkt prestanda för frågor och kostnaden för distributionen. |
 | Logisk partition av data  |  Index  |  databas  |  Tillåter logisk separation av data. Båda implementeringarna tillåter fackföreningar och sammanfogning över dessa partitioner. |
 | Metadata för strukturerade händelser | Ej tillämpligt | tabell |  Splunk har inte konceptet exponerat för sökspråket för händelsemetadata. Azure Monitor-loggar har begreppet en tabell, som har kolumner. Varje händelseinstans mappas till en rad. |
 | Datapost | händelse | Rad |  Terminologi ändras bara. |
 | Attributet Datapost | fält |  kolumn |  I Azure Monitor är detta fördefinierat som en del av tabellstrukturen. I Splunk har varje händelse sin egen uppsättning fält. |
 | Typer | Datatyp |  Datatyp |  Azure Monitor-datatyper är tydligare eftersom de anges i kolumnerna. Båda har möjlighet att arbeta dynamiskt med datatyper och ungefär motsvarande uppsättning datatyper inklusive JSON-stöd. |
 | Fråga och sök  | sök | DocumentDB |  Begreppen är i stort sett desamma mellan både Azure Monitor och Splunk. |
 | Tid för intag av händelser | Systemtid | ingestion_time() |  I Splunk får varje händelse en systemtidsstämpel för den tidpunkt då händelsen indexerades. I Azure Monitor kan du definiera en princip som kallas ingestion_time som exponerar en systemkolumn som kan refereras via funktionen ingestion_time(). |

## <a name="functions"></a>Funktioner

Följande tabell anger funktioner i Azure Monitor som motsvarar Splunk-funktioner.

|Splunk | Azure Monitor |Kommentar
|---|---|---
|strcat (strcat) | strcat()| (1) |
|split  | split() | (1) |
|om     | iff()   | (1) |
|tonumber (tonumber) | todouble()<br>tolong()<br>toint() | (1) |
|Övre<br>Lägre |toupper()<br>tolower()|(1) |
| Ersätta | ersätt() | (1)<br> Observera också `replace()` att medan tar tre parametrar i båda produkterna, parametrarna är olika. |
| Substr | delsträng() | (1)<br>Observera också att Splunk använder enbaserade index. Azure Monitor noterar nollbaserade index. |
| tolower (tolower) |  tolower() | (1) |
| toupper (toupper) | toupper() | (1) |
| Matcha | matchar regex |  (2)  |
| Regex | matchar regex | I Splunk, `regex` är en operatör. I Azure Monitor är det en relationsoperator. |
| searchmatch (sökmatch) | == | I Splunk, `searchmatch` tillåter att söka efter den exakta strängen.
| slumpmässig | rand()<br>rand(n) | Splunks funktion returnerar ett tal från noll till 2<sup>31</sup>-1. Azure Monitor' returnerar ett tal mellan 0,0 och 1,0, eller om en parameter anges, mellan 0 och n-1.
| nu | now() | (1)
| relative_time | totimespan() | (1)<br>I Azure Monitor är Splunks motsvarighet till relative_time(datetimeVal, offsetVal) datetimeVal + totimespan(offsetVal).<br>Till exempel <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> <code>...  &#124; extend myTime = now() - totimespan("1d")</code>blir .

(1) I Splunk anropas funktionen `eval` med operatören. I Azure Monitor används den `extend` som `project`en del av eller .<br>(2) I Splunk anropas funktionen `eval` med operatören. I Azure Monitor kan den `where` användas med operatören.


## <a name="operators"></a>Operatorer

Följande avsnitt ger exempel på hur du använder olika operatörer mellan Splunk och Azure Monitor.

> [!NOTE]
> I följande exempel mappar splunk-fältregeln till en tabell i Azure Monitor och Splunks standardtidsstämpel mappar till kolumnen Loggar Analytics _ingestion_time()._ _rule_

### <a name="search"></a>Search
I Splunk kan du `search` utelämna nyckelordet och ange en ocierad sträng. I Azure Monitor måste du `find`starta varje fråga med , en ocierad sträng är ett kolumnnamn och uppslagsvärdet måste vara en citerad sträng. 

| |  | |
|:---|:---|:---|
| Splunk | **Sök** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **hitta** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filter
Azure Monitor-loggfrågor startar från en tabellresultatuppsättning där filtret. I Splunk är filtrering standardåtgärden för det aktuella indexet. Du kan `where` också använda operatorn i Splunk, men det rekommenderas inte.

| |  | |
|:---|:---|:---|
| Splunk | **Sök** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **Där** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Få n händelser/rader för inspektion 
Azure Monitor-loggfrågor `take` stöder också `limit`som ett alias till . I Splunk, om resultaten `head` beställs, returnerar de första n resultaten. I Azure Monitor beställs inte gränsen utan returnerar de första n-raderna som hittas.

| |  | |
|:---|:---|:---|
| Splunk | **Huvud** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **Gräns** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Hämta de första n-händelser/raderna som beställts av ett fält/en kolumn
För bottenresultat, i Splunk du använder `tail`. I Azure Monitor kan du ange `asc`orderriktningen med .

| |  | |
|:---|:---|:---|
| Splunk | **Huvud** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **Topp** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Utöka resultatuppsättningen med nya fält/kolumner
Splunk har `eval` också en funktion, som inte `eval` ska vara jämförbar med operatören. Både `eval` operatorn i Splunk och operatorn `extend` i Azure Monitor stöder endast skalärfunktioner och aritmetiska operatorer.

| |  | |
|:---|:---|:---|
| Splunk | **Eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **Utöka** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Byt namn 
Azure Monitor `project-rename` använder operatorn för att byta namn på ett fält. `project-rename`gör att frågan kan dra nytta av alla index som är förbyggda för ett fält. Splunk har `rename` en operatör att göra detsamma.

| |  | |
|:---|:---|:---|
| Splunk | **Byt namn** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **projekt-byta namn** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Formatera resultat/projektion
Splunk verkar inte ha en `project-away`operatör som liknar . Du kan använda användargränssnittet för att filtrera bort fält.

| |  | |
|:---|:---|:---|
| Splunk | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **Projekt**<br>**projekt-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Sammansättning
Se [aggregeringarna i Azure Monitor-loggfrågor](aggregations.md) för de olika aggregeringsfunktionerna.

| |  | |
|:---|:---|:---|
| Splunk | **Statistik** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **Sammanfatta** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Slå ihop
Gå med i Splunk har betydande begränsningar. Underfrågan har en gräns på 10000 resultat (som anges i distributionskonfigurationsfilen) och det finns ett begränsat antal kopplingssmaker.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Sortera
I Splunk måste du använda operatorn `reverse` för att sortera i stigande ordning. Azure Monitor stöder också att definiera var nulls ska anges, i början eller i slutet.

| |  | |
|:---|:---|:---|
| Splunk | **Sortera** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **ordning efter** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Flervärde expandera
Detta är en liknande operatör i både Splunk och Azure Monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand (mvexpand)** |  `mvexpand foo` |
| Azure Monitor | **mvexpand (mvexpand)** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Resultat aspekter, intressanta områden
I Logganalys i Azure-portalen visas endast den första kolumnen. Alla kolumner är tillgängliga via API:et.

| |  | |
|:---|:---|:---|
| Splunk | **Fält** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **Aspekter** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Avdubblett
Du kan `summarize arg_min()` i stället använda för att ändra ordningen på vilken post som väljs.

| |  | |
|:---|:---|:---|
| Splunk | **uppdup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **sammanfatta arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Nästa steg

- Gå igenom en lektion om [skrivloggfrågorna i Azure Monitor](get-started-queries.md).
