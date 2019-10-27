---
title: resurs () uttryck i Azure Monitor logg fråga | Microsoft Docs
description: Resurs uttrycket används i en resurs-inriktad Azure Monitor logg fråga för att hämta data från flera resurser.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 370b2547c9e726ab4f5ebc4dd732cc0bfa17f760
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933018"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>resurs () uttryck i Azure Monitor logg fråga

`resource`-uttrycket används i en Azure Monitor fråga [som är begränsad till en resurs](scope.md#query-scope) för att hämta data från andra resurser. 


## <a name="syntax"></a>Syntax

`resource(`*identifierare*`)`

## <a name="arguments"></a>Argument

- *Identifierare*: resurs-ID för en resurs.

| Beteckning | Beskrivning | Exempel
|:---|:---|:---|
| Resurs | Innehåller data för resursen. | resurs ("/Subscriptions/xxxxxxx-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup/providers/Microsoft.Compute/virtualmachines/myvm") |
| Resurs grupp eller prenumeration | Innehåller data för resursen och alla resurser som den innehåller.  | resurs ("/Subscriptions/xxxxxxx-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Anteckningar

* Du måste ha Läs behörighet till resursen.


## <a name="examples"></a>Exempel

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Nästa steg

- Mer information om ett fråge omfång finns [i omfånget och tidsintervallet för logg frågor i Azure Monitor Log Analytics](scope.md) .
- Få fullständig dokumentation för [Kusto-frågespråket](/azure/kusto/query/).
