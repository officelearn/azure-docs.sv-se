---
title: Arbeta med strängar i Azure Monitor-loggfrågor | Microsoft-dokument
description: Den här artikeln innehåller en självstudiekurs för att använda Azure Monitor Log Analytics i Azure-portalen för att fråga och analysera loggdata i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8be4f318149590ff08b73fda719e99a17220ec2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670159"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Arbeta med JSON och datastrukturer i Azure Monitor-loggfrågor

> [!NOTE]
> Du bör slutföra [Komma igång med Azure Monitor Log Analytics](get-started-portal.md) och Komma igång med Azure [Monitor-loggfrågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Kapslade objekt är objekt som innehåller andra objekt i en matris eller en karta över nyckelvärdespar. Dessa objekt representeras som JSON-strängar. I den här artikeln beskrivs hur JSON används för att hämta data och analysera kapslade objekt.

## <a name="working-with-json-strings"></a>Arbeta med JSON-strängar
Används `extractjson` för att komma åt ett specifikt JSON-element i en känd sökväg. Den här funktionen kräver ett banuttryck som använder följande konventioner.

- _$_ för att referera till rotmappen
- Använd hakparentesen eller punkt notationen för att referera till index och element som illustreras i följande exempel.


Använd hakparenteser för index och punkter för att separera element:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Detta är samma resultat med endast gafflings notation:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Om det bara finns ett element kan du bara använda punktateringen:

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Arbeta med objekt

### <a name="parsejson"></a>parsejson (tolk)
Om du vill komma åt flera element i json-strukturen är det enklare att komma åt den som ett dynamiskt objekt. Används `parsejson` för att casta textdata till ett dynamiskt objekt. När du har konverterat till en dynamisk typ kan ytterligare funktioner användas för att analysera data.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylängd
Används `arraylength` för att räkna antalet element i en matris:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand (mvexpand)
Används `mvexpand` för att dela upp ett objekts egenskaper i separata rader.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand (mvexpand)](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Används `buildschema` för att hämta schemat som medger alla värden för ett objekt:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Utdata är ett schema i JSON-format:
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
I det här utdata beskrivs namnen på objektfälten och deras matchande datatyper. 

Kapslade objekt kan ha olika scheman, till exempel i följande exempel:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Skapa schema](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda loggfrågor i Azure Monitor:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [Avancerad frågeskrivning](advanced-query-writing.md)
- [Går](joins.md)
- [Diagram](charts.md)
