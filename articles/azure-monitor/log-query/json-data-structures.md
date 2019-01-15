---
title: Arbeta med strängar i Azure Log Analytics-frågor | Microsoft Docs
description: Den här artikeln innehåller en självstudie för att skriva frågor i Log Analytics Analytics-portalen.
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
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 24f22d659ccfb6923ad2a038e12454716b2c5445
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263891"
---
# <a name="working-with-json-and-data-structures-in-log-analytics-queries"></a>Arbeta med JSON och datastrukturer i Log Analytics-frågor

> [!NOTE]
> Bör du genomföra [Kom igång med Analytics-portalen](get-started-portal.md) och [komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Kapslade objekt är objekt som innehåller andra objekt i en matris eller en karta över nyckel / värde-par. Dessa objekt visas som JSON-strängar. Den här artikeln beskriver hur du använder JSON för att hämta data och analysera kapslade objekt.

## <a name="working-with-json-strings"></a>Arbeta med JSON-strängar
Använd `extractjson` att komma åt ett visst JSON-element i en känd sökväg. Den här funktionen kräver ett sökvägsuttryck som använder följande konventioner.

- _$_ att referera till rotmappen
- Använda beteckningen hakparentes eller punkt för att referera till index och element som illustreras i följande exempel.


Använd hakparenteser för index och punkter för att separera element:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Det här är samma resultat med hjälp av endast hakparenteser notation:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Om det finns bara ett element, kan du använda endast punktnotation:

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Arbeta med objekt

### <a name="parsejson"></a>parsejson
För att komma åt flera element i json-strukturen är det enklare att komma åt den som en dynamisk objekt. Använd `parsejson` att omvandla textdata till en dynamisk objekt. När ändras till en dynamisk typ, användas ytterligare funktioner för att analysera data.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Använd `arraylength` att räkna antalet element i en matris:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Använd `mvexpand` att bryta egenskaperna för ett objekt i separata rader.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Använd `buildschema` att hämta det schema som ger alla värden i ett objekt:

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
Den här utdatan beskriver namnen på fälten objekt och deras matchande datatyper. 

Kapslade objekt kan ha olika scheman som i följande exempel:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Skapa schema](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att använda Log Analytics-frågespråket:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [Avancerad fråga skrivning](advanced-query-writing.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)