---
title: resurs () uttryck i Azure Monitor logg fråga | Microsoft Docs
description: Resurs uttrycket används i en resurs-inriktad Azure Monitor logg fråga för att hämta data från flera resurser.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: deca6e7ef1c231a82a73067971d86a6e9cdd0599
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817415"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>resurs () uttryck i Azure Monitor logg fråga

Uttrycket `resource` används i en Azure Monitor fråga som är [begränsad till en resurs](scope.md#query-scope) för att hämta data från andra resurser. 


## <a name="syntax"></a>Syntax

`resource(`*identifierare*`)`

## <a name="arguments"></a>Argument

- *Identifierare*: Resurs-ID för en resurs.

| identifierare | Beskrivning | Exempel
|:---|:---|:---|
| Resource | Innehåller data för resursen. | resurs ("/Subscriptions/xxxxxxx-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup/providers/Microsoft.Compute/virtualmachines/myvm") |
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
