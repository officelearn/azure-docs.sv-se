---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 8f3a58d3a7470867ab23249bbd645289e010ad89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997328"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x och senare

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Egenskap  |Standardvärde | Description |
|---------|---------|---------|
|maxBatchSize|10|Maximalt antal händelser som tas emot per Receive-slinga.|
|prefetchCount|300|Standard antalet för hämtningar som används av den underliggande `EventProcessorHost` . Det minsta tillåtna värdet är 10.|
|batchCheckpointFrequency|1|Antalet händelse grupper som ska bearbetas innan en kontroll punkt för EventHub-markören skapas.|

> [!NOTE]
> En referens för host.jspå i Azure Functions 2. x och senare finns i [host.jsför referens för Azure Functions](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Egenskap  |Standardvärde | Description |
|---------|---------|---------| 
|maxBatchSize|64|Maximalt antal händelser som tas emot per Receive-slinga.|
|prefetchCount|saknas|Standard för hämtning som ska användas av den underliggande `EventProcessorHost` .| 
|batchCheckpointFrequency|1|Antalet händelse grupper som ska bearbetas innan en kontroll punkt för EventHub-markören skapas.| 

> [!NOTE]
> En referens för host.jspå i Azure Functions 1. x finns i [host.jsför referens för Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).
