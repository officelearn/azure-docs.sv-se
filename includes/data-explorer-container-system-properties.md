---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128652"
---
### <a name="event-system-properties-mapping"></a>Mappning av händelse system egenskaper

> [!Note]
> * System egenskaper stöds för händelser med en post.
> * För `csv` mappning läggs egenskaper till i början av posten. För `json` mappning läggs egenskaper till enligt det namn som visas i den nedrullningsbara listan.

Om du har valt **händelse system egenskaper** i avsnittet **data källa** i tabellen måste du inkludera följande egenskaper i tabell schemat och mappningen.

**Exempel på tabell schema**

Om dina data innehåller tre kolumner (`Timespan`, `Metric`och `Value`) och de egenskaper som du inkluderar är `x-opt-enqueued-time` och `x-opt-offset`, skapar eller ändrar du tabellens schema med hjälp av det här kommandot:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Exempel på CSV-mappning**

Kör följande kommandon för att lägga till data i början av posten. Observera ordnings tal värden.

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

Data läggs till med hjälp av systemets egenskaps namn som de visas i listan **data anslutning** bladet **händelse system egenskaper** . Kör följande kommandon:

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
