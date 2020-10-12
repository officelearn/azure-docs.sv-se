---
title: Arbeta med strängar i Azure Monitor logg frågor | Microsoft Docs
description: Den här artikeln innehåller en själv studie kurs om hur du använder Azure Monitor Log Analytics i Azure Portal för att fråga efter och analysera loggdata i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 5021b815da1419052ceb4d6114fbb2bc676fe8d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088407"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Arbeta med JSON och data strukturer i Azure Monitor logg frågor

> [!NOTE]
> Du bör slutföra [Kom igång med Azure Monitor Log Analytics](get-started-portal.md) och [komma igång med Azure Monitor logg frågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Kapslade objekt är objekt som innehåller andra objekt i en matris eller en karta med nyckel/värde-par. Dessa objekt representeras som JSON-strängar. Den här artikeln beskriver hur JSON används för att hämta data och analysera kapslade objekt.

## <a name="working-with-json-strings"></a>Arbeta med JSON-strängar
Använd `extractjson` för att få åtkomst till ett visst JSON-element i en känd sökväg. Den här funktionen kräver ett Sök vägs uttryck som använder följande konventioner.

- _$_ för att referera till rotmappen
- Använd hakparentesen eller punkt notationen för att referera till index och element enligt följande exempel.


Använd hakparenteser för index och punkter för att avgränsa element:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Det här är samma resultat som bara använder hakparenteser:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Om det bara finns ett-element kan du bara använda punkt notation:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Arbeta med objekt

### <a name="parsejson"></a>parsejson
För att komma åt flera element i JSON-strukturen är det enklare att komma åt det som ett dynamiskt objekt. Används `parsejson` för att omvandla text data till ett dynamiskt objekt. När du har konverterat till en dynamisk typ kan ytterligare funktioner användas för att analysera data.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Används `arraylength` för att räkna antalet element i en matris:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Använd `mvexpand` för att bryta egenskaperna för ett objekt till separata rader.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![Skärm bild som visar hosts_0 med värden för plats, status och hastighet.](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Använd `buildschema` för att hämta schemat som admits alla värden för ett objekt:

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
Dessa utdata beskriver namnen på objekt fälten och deras matchande data typer. 

Kapslade objekt kan ha olika scheman, som i följande exempel:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Bygg schema](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda logg frågor i Azure Monitor:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [Avancerad frågeskrivning](advanced-query-writing.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)
