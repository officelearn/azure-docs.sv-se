---
title: Standardegenskaper i Azure Monitor Log Analytics-poster | Microsoft Docs
description: Beskriver egenskaper som är gemensamma för olika datatyper i Azure Monitor Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955875"
---
# <a name="standard-properties-in-log-analytics-records"></a>Standardegenskaper i Log Analytics-poster
Data i [Log Analytics](../log-analytics/log-analytics-queries.md) lagras som en uppsättning poster, var och en med en viss datatyp som har en unik uppsättning egenskaper. Många datatyper har standardegenskaper som är gemensamma för flera typer. Den här artikeln beskriver de här egenskaperna och innehåller exempel på hur du kan använda dem i frågor.

Vissa av dessa egenskaper är fortfarande håller på att utvecklas, så du kan se dem i vissa datatyper, men har ännu inte i andra.


## <a name="resourceid"></a>_ResourceId
Den **_ResourceId** egenskapen innehåller en unik identifierare för resursen som som posten är associerad med. Detta ger dig en egenskap som är standard ska använda för att begränsa frågan till endast poster från en viss resurs eller att ansluta till relaterade data från flera tabeller.

För Azure-resurser, värdet för **_ResourceId** är den [Azure-resurs-ID-URL: en](../azure-resource-manager/resource-group-template-functions-resource.md). Egenskapen är för närvarande begränsat till Azure-resurser, men det kommer att gälla till resurser utanför Azure, t.ex lokala datorer. 

### <a name="examples"></a>Exempel
I följande exempel visas en fråga som kopplar prestanda- och händelsedata för varje dator. Den visar alla händelser med ID _101_ och processorbelastning över 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

I följande exempel visas en fråga som kopplar _AzureActivity_ registrerar med _SecurityEvent_ poster. Den visar alla aktivitet åtgärder med användare som har loggat in till dessa datorer.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur [Log Analytics-data lagras](../log-analytics/log-analytics-queries.md).
- Få en lektion på [skriva frågor i Log Analytics](../log-analytics/query-language/get-started-queries.md).
- Få en lektion på [koppla tabeller i Log Analytics-frågor](../log-analytics/query-language/joins.md).