---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187137"
---
```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|maxBatchSize|64|Det maximala antalet händelser som tas emot per mottagningsloop.|
|prefetchCount (prefetchCount)|Saknas|StandardprefetchCount som ska användas av den underliggande EventProcessorHost.| 
|batchCheckpointFrequency|1|Antalet händelsebatchar som ska bearbetas innan du skapar en EventHub-markörkontrollpunkt.| 
