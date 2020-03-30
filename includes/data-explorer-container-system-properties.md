---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79128652"
---
### <a name="event-system-properties-mapping"></a>Mappning av egenskaper för händelsesystem

> [!Note]
> * Systemegenskaper stöds för enskilda posthändelser.
> * För `csv` mappning läggs egenskaper till i början av posten. För `json` mappning läggs egenskaper till enligt namnet som visas i listrutan.

Om du valde **egenskaper för händelsesystem** i avsnittet **Datakälla** i tabellen måste du inkludera följande egenskaper i tabellschemat och mappningen.

**Exempel på tabellschema**

Om dina data innehåller`Timespan` `Metric`tre `Value`kolumner ( , och `x-opt-enqueued-time` `x-opt-offset`) och de egenskaper du inkluderar är och skapar eller ändrar tabellschemat med det här kommandot:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Exempel på CSV-mappning**

Kör följande kommandon för att lägga till data i början av posten. Notera ordningsvärden.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Exempel på JSON-mappning**

Data läggs till med hjälp av systemegenskapernas namn som de visas i listan Egenskaper för **händelsesystem för** **dataanslutningsblad.** Kör dessa kommandon:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
