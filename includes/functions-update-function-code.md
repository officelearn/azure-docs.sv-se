---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737302"
---
## <a name="update-the-function"></a>Uppdatera funktionen

Mallen skapar som standard en funktion som kräver en funktionsnyckel då begäranden görs. Om du vill göra det lättare att testa funktionen i Azure, behöver du uppdatera funktionen för att tillåta anonym åtkomst. Hur du gör den här ändringen beror på funktionsprojektspråket.

### <a name="c"></a>C\#

Öppna MyHttpTrigger.cs-kodfilen som är din nya funktion och uppdatera attributet **AuthorizationLevel** i funktionsdefinitionen till värdet `Anonymous` och spara dina ändringar.

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

Öppna filen function.json för den nya funktionen i en textredigerare, uppdatera den **authLevel** -egenskapen i **bindningar** till `anonymous`, och spara dina ändringar.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

Nu kan du anropa funktionen i Azure utan att behöva ange funktionsnyckeln. Funktionsnyckeln krävs aldrig vid lokal körning.
