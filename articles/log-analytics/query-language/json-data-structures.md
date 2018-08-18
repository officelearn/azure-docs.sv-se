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
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: f027754f26a9063aa5faa548fd01576624811005
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190330"
---
# <a name="working-with-json-and-data-structures-in-log-analytics-queries"></a>Arbeta med JSON och datastrukturer i Log Analytics-frågor

> [!NOTE]
> Bör du genomföra [Kom igång med Analytics-portalen](get-started-analytics-portal.md) och [komma igång med frågor](get-started-queries.md) innan du slutför den här lektionen.


Kapslade objekt är objekt som innehåller andra objekt i en matris eller en karta över nyckel / värde-par. Dessa objekt visas som JSON-strängar. Den här artikeln beskriver hur du använder JSON för att hämta data och analysera kapslade objekt.

## <a name="working-with-json-strings"></a>Arbeta med JSON-strängar
Använd `extractjson` att komma åt ett visst JSON-element i en känd sökväg. Den här funktionen kräver ett sökvägsuttryck som använder följande konventioner.

- _$_ att referera till rotmappen
- Använda beteckningen hakparentes eller punkt för att referera till index och element som illustreras i följande exempel.


Använd hakparenteser för index och punkter för att separera element:

```OQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Det här är samma resultat med hjälp av endast hakparenteser notation:

```OQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Om det finns bara ett element, kan du använda endast punktnotation:

```OQL
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Arbeta med objekt

### <a name="parsejson"></a>parsejson
För att komma åt flera element i json-strukturen är det enklare att komma åt den som en dynamisk objekt. Använd `parsejson` att omvandla textdata till en dynamisk objekt. När ändras till en dynamisk typ, användas ytterligare funktioner för att analysera data.

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Använd `arraylength` att räkna antalet element i en matris:

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Använd `mvexpand` att bryta egenskaperna för ett objekt i separata rader.

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Använd `buildschema` att hämta det schema som ger alla värden i ett objekt:

```OQL
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

```OQL
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