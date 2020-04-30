---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791662"
---
### <a name="functions-2x-and-higher"></a>Funktioner 2. x och högre

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

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------|
|maxBatchSize|10|Maximalt antal händelser som tas emot per Receive-slinga.|
|prefetchCount|300|Standard antalet för hämtningar som används av den underliggande `EventProcessorHost`.|
|batchCheckpointFrequency|1|Antalet händelse grupper som ska bearbetas innan en kontroll punkt för EventHub-markören skapas.|

> [!NOTE]
> En referens för Host. json i Azure Functions 2. x och senare finns i [Host. JSON-referens för Azure Functions](../articles/azure-functions/functions-host-json.md).

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

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------| 
|maxBatchSize|64|Maximalt antal händelser som tas emot per Receive-slinga.|
|prefetchCount|saknas|Standard för hämtning som ska användas av den underliggande `EventProcessorHost`.| 
|batchCheckpointFrequency|1|Antalet händelse grupper som ska bearbetas innan en kontroll punkt för EventHub-markören skapas.| 

> [!NOTE]
> En referens för Host. json i Azure Functions 1. x finns i [Host. JSON-referens för Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).

