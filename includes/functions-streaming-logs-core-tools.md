---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164880"
---
#### <a name="built-in-log-streaming"></a>Inbyggd logg strömning

Använd `logstream` alternativet för att börja ta emot strömmande loggar för en speciell Function-app som körs i Azure, som i följande exempel:

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>Inbyggd logg strömning har ännu inte Aktiver ATS i kärn verktyg för Function-appar som körs på Linux i en förbruknings plan. För dessa värd planer måste du i stället använda Live Metrics Stream för att visa loggarna i nära real tid.

#### <a name="live-metrics-stream"></a>Live-ström med mätvärden

Du kan visa [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) för din Function-app i ett nytt webbläsarfönster genom att inkludera `--browser` alternativet, som i följande exempel:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
