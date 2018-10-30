---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/16/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d264477693458ff4132c1f69704a480eed2756e0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988023"
---
## <a name="update-the-function"></a>Uppdatera funktionen

Mallen skapar som standard en funktion som kräver en funktionsnyckel då begäranden görs. Om du vill göra det lättare att testa funktionen i Azure, behöver du uppdatera funktionen för att tillåta anonym åtkomst. Hur du gör den här ändringen beror på funktionsprojektspråket.

### <a name="c"></a>C\#

Öppna MyHttpTrigger.cs-kodfilen som är din nya funktion och uppdatera attributet **AuthorizationLevel** i funktionsdefinitionen till värdet `anonymous` och spara dina ändringar.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Öppna .json-funktionsfilen för den nya funktionen i en textredigerare och uppdatera egenskapen **authLevel** i **bindings.httpTrigger** till `anonymous` och spara dina ändringar.

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
