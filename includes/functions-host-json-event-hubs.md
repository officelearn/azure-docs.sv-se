---
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104465"
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

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|maxBatchSize|64|Det maximala antal mottagna händelser per receive-loop.|
|prefetchCount|Saknas|Standard PrefetchCount som ska användas av den underliggande EventProcessorHost.| 
|batchCheckpointFrequency|1|Antal händelsebatchar att bearbeta innan du skapar en kontrollpunkt för EventHub-markör.| 

