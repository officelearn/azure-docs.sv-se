---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881337"
---
#### <a name="built-in-log-streaming"></a>Inbyggd logg strömning

Använd `logstream` alternativet för att börja ta emot strömmande loggar för en speciell Function-app som körs i Azure, som i följande exempel:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live-ström med mätvärden

Du kan också visa [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) för din Function-app i ett nytt webbläsarfönster genom att `--browser` inkludera alternativet, som i följande exempel:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
