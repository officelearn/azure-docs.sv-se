---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881337"
---
#### <a name="built-in-log-streaming"></a>Inbyggd logg strömning

`logstream` Använd alternativet för att börja ta emot strömmande loggar för en speciell Function-app som körs i Azure, som i följande exempel:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Du kan också visa [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) för din Function-app i ett nytt webbläsarfönster genom att `--browser` inkludera alternativet, som i följande exempel:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
