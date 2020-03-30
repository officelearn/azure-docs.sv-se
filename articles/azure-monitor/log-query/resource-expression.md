---
title: resurs() uttryck i Azure Monitor-loggfrågan | Microsoft-dokument
description: Resursuttrycket används i en resurscentrerad Azure Monitor-loggfråga för att hämta data från flera resurser.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665707"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>resurs() uttryck i Azure Monitor-loggfrågan

Uttrycket `resource` används i en Azure Monitor-fråga [som är begränsad till en resurs](scope.md#query-scope) för att hämta data från andra resurser. 


## <a name="syntax"></a>Syntax

`resource(`*Identifierare*`)`

## <a name="arguments"></a>Argument

- *Identifierare*: Resurs-ID för en resurs.

| Identifierare | Beskrivning | Exempel
|:---|:---|:---|
| Resurs | Innehåller data för resursen. | resurs("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| Resursgrupp eller prenumeration | Innehåller data för resursen och alla resurser som den innehåller.  | resurs("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Anteckningar

* Du måste ha läsbehörighet till resursen.


## <a name="examples"></a>Exempel

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Nästa steg

- Mer information om ett frågeomfattning finns [i Loggomfånget och tidsintervallet i Azure Monitor Log Analytics.](scope.md)
- Få tillgång till fullständig dokumentation för [Kusto-frågespråket](/azure/kusto/query/).
