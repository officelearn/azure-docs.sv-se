---
title: Splunk för att Azure Monitor logg fråga | Microsoft Docs
description: Hjälp för användare som är bekanta med Splunk i Learning Azure Monitor logg frågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: c59b5646e011afa6b8487e8145a1cb07e6e2a8ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015600"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk för att Azure Monitor logg fråga

Den här artikeln är avsedd att hjälpa användare som är bekanta med Splunk att lära sig Kusto-frågespråket för att skriva logg frågor i Azure Monitor. Direkta jämförelser görs mellan de två för att förstå viktiga skillnader och även likheter där du kan utnyttja din befintliga kunskap.

## <a name="structure-and-concepts"></a>Struktur och koncept

I följande tabell jämförs begrepp och data strukturer mellan Splunk och Azure Monitor loggar.

 | Koncept  | Splunk | Azure Monitor |  Kommentar
 | --- | --- | --- | ---
 | Distributions enhet  | cluster |  cluster |  Azure Monitor tillåter godtyckliga kors kluster frågor. Splunk har inte det. |
 | Cachelagrade data |  buckets  |  Principer för cachelagring och bevarande |  Styr period-och lagrings nivå för data. Den här inställningen påverkar direkt prestanda för frågorna och kostnaden för distributionen. |
 | Logisk partition med data  |  index  |  databas  |  Tillåter logisk avgränsning av data. Båda implementeringarna tillåter unioner och anslutning mellan dessa partitioner. |
 | Metadata för strukturerad händelse | E.t. | tabell |  Splunk har inte det koncept som exponeras för sökspråket för händelsens metadata. Azure Monitor loggar har en tabell som har kolumner. Varje händelse instans mappas till en rad. |
 | Data post | händelse | rad |  Endast terminologi ändring. |
 | Datapost-attribut | fält |  kolumn |  I Azure Monitor är detta fördefinierat som en del av tabell strukturen. I Splunk har varje händelse en egen uppsättning fält. |
 | Typer | datatype |  datatype |  Azure Monitor data typer är mer explicita eftersom de är inställda på kolumnerna. Båda har möjlighet att arbeta dynamiskt med data typer och ungefär samma uppsättning data typer, inklusive JSON-stöd. |
 | Fråga och Sök  | sök | DocumentDB |  Begreppen är i stort sett samma mellan Azure Monitor och Splunk. |
 | Tid för händelse hämtning | System tid | ingestion_time() |  I Splunk hämtar varje händelse en tidsstämpel för systemet för den tidpunkt då händelsen indexerades. I Azure Monitor kan du definiera en princip som kallas ingestion_time som visar en system kolumn som kan refereras via funktionen ingestion_time (). |

## <a name="functions"></a>Funktioner

I följande tabell anges funktioner i Azure Monitor som motsvarar Splunk-funktioner.

|Splunk | Azure Monitor |Kommentar
|---|---|---
|strcat | strcat()| 81.1 |
|split  | split() | 81.1 |
|om     | iff()   | 81.1 |
|tonumber | todouble()<br>tolong()<br>toint() | 81.1 |
|produktion<br>sämre |toupper()<br>tolower()|81.1 |
| bytt | replace() | 81.1<br> Observera också att när `replace()` tar tre parametrar i båda produkterna är parametrarna olika. |
| substr | substring() | 81.1<br>Observera också att Splunk använder sig av ett-baserat index. Azure Monitor noterar noll-baserade index. |
| tolower |  tolower() | 81.1 |
| toupper | toupper() | 81.1 |
| villkoren | matchar regex |  (2)  |
| verifiering | matchar regex | I Splunk `regex` är en operatör. I Azure Monitor är det en Relations operator. |
| searchmatch | == | I Splunk `searchmatch` kan du söka efter den exakta strängen.
| slumpmässig | rand()<br>rand (n) | Splunk-funktionen returnerar ett tal från noll till 2<sup>31</sup>-1. Azure Monitor returnerar ett tal mellan 0,0 och 1,0, eller om en parameter har angetts, mellan 0 och n-1.
| nu | now() | 81.1
| relative_time | totimespan() | 81.1<br>I Azure Monitor är Splunk motsvarigheten till relative_time (datetimeVal, offsetVal) datetimeVal + ToTimeSpan (offsetVal).<br>Till exempel <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> blir <code>...  &#124; extend myTime = now() - totimespan("1d")</code> .

(1) i Splunk anropas funktionen med `eval` operatorn. I Azure Monitor används den som en del av `extend` eller `project` .<br>(2) i Splunk anropas funktionen med `eval` operatorn. I Azure Monitor kan den användas med- `where` operatorn.


## <a name="operators"></a>Operatorer

I följande avsnitt får du exempel på hur du kan använda olika operatorer mellan Splunk och Azure Monitor.

> [!NOTE]
> I följande _exempel mappas_ Splunk-till en tabell i Azure Monitor och Splunk standard-tidsstämpeln mappas till kolumnen loggar Analytics _ingestion_time ()_ .

### <a name="search"></a>Sök
I Splunk kan du utelämna `search` nyckelordet och ange en sträng med citat tecken. I Azure Monitor måste du starta varje fråga med `find` , en icke-Citerad sträng är ett kolumn namn och uppslags värdet måste vara en sträng i citat tecken. 

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **Sök** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| **Azure Monitor** | **reda** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |


### <a name="filter"></a>Filtrera
Azure Monitor logg frågor börjar från en tabell resultat uppsättning där filtret. I Splunk är filtrering standard åtgärden för det aktuella indexet. Du kan också använda `where` operatorn i Splunk, men det rekommenderas inte.

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **Sök** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| **Azure Monitor** | **vilken** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |

### <a name="getting-n-eventsrows-for-inspection"></a>Hämtar n händelser/rader för granskning 
Azure Monitor logg frågor stöder också `take` ett alias till `limit` . I Splunk `head` returnerar resultaten de första n resultaten om resultatet sorteras. I Azure Monitor beställs inte gränsen, men returnerar de första n raderna som hittas.

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **företagets** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| **Azure Monitor** | **gränserna** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |

### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Hämta de första n händelserna/raderna som sorteras efter ett fält/en kolumn
För nedersta resultat, i Splunk som du använder `tail` . I Azure Monitor kan du ange ordnings riktningen för ordningen `asc` .

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **företagets** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| **Azure Monitor** | **top** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |

### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Utöka resultat uppsättningen med nya fält/kolumner
Splunk har också en `eval` funktion som inte ska vara jämförbar med `eval` operatorn. Både `eval` operatorn i Splunk och `extend` operatorn i Azure Monitor stöder bara skalära funktioner och aritmetiska operatorer.

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **utvärdera** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| **Azure Monitor** | **batteri** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |

### <a name="rename"></a>Byt namn 
Azure Monitor använder `project-rename` operatorn för att byta namn på ett fält. `project-rename` tillåter att frågan kan dra nytta av alla index som skapats i förväg för ett fält. Splunk har en `rename` operator att göra samma.

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **ta** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| **Azure Monitor** | **projekt-Byt namn** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |

### <a name="format-resultsprojection"></a>Format resultat/projektion
Splunk verkar inte ha en operator som liknar `project-away` . Du kan använda användar gränssnittet för att filtrera bort fält.

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| **Azure Monitor** | **projektfilerna**<br>**projekt bort** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |

### <a name="aggregation"></a>Aggregering
Se [agg regeringar i Azure Monitor logg frågor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations) för de olika agg regerings funktionerna.

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| **Azure Monitor** | **sammanfatta** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |


### <a name="join"></a>Slå ihop
Join i Splunk har avsevärda begränsningar. Under frågan har en gräns på 10000 resultat (anges i distributions konfigurations filen) och det finns ett begränsat antal Join-varianter.

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **ansluta** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| **Azure Monitor** | **ansluta** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |

### <a name="sort"></a>Sortera
I Splunk måste du använda operatorn för att sortera i stigande ordning `reverse` . Azure Monitor också stöd för att definiera var nullvärden ska användas, i början eller i slutet.

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **ordning** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| **Azure Monitor** | **Sortera efter** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |

### <a name="multivalue-expand"></a>Expandera multivärde
Det här är en liknande operator i både Splunk och Azure Monitor.

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **mvexpand** |  `mvexpand foo` |
| **Azure Monitor** | **mvexpand** | `mvexpand foo` |

### <a name="results-facets-interesting-fields"></a>Resultat ansikte, intressanta fält
I Log Analytics i Azure Portal visas bara den första kolumnen. Alla kolumner är tillgängliga via API: et.

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **fält** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| **Azure Monitor** | **facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |

### <a name="de-duplicate"></a>De är dubbletter
Du kan använda `summarize arg_min()` i stället för att ändra ordningen på posten som väljs.

| | Operator | Exempel |
|:---|:---|:---|
| **Splunk** | **duplicering** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| **Azure Monitor** | **sammanfatta arg_max ()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |

## <a name="next-steps"></a>Nästa steg

- Gå igenom en lektion om att [skriva logg frågor i Azure Monitor](get-started-queries.md).
