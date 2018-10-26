---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0a3f739bae3ec758c8e25d581d3e2b9feb4af5b1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133803"
---
```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till återanrop som meddelandet pump ska starta. Som standard bearbetar funktionskörningen flera meddelanden samtidigt. För att dirigera körning för att bearbeta en enskild kö eller ett ämne meddelande i taget, ange `maxConcurrentCalls` till 1. | 
|prefetchCount|Saknas|Standard PrefetchCount som ska användas av den underliggande MessageReceiver.| 
|autoRenewTimeout|00:05:00|Maximal varaktighet inom vilken meddelandelåset förnyas automatiskt.| 
