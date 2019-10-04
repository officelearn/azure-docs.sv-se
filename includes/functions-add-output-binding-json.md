---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 64a1062a8b73768a334277eafb663a7d2d5dd59a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838973"
---
Binding-attribut definieras direkt i function. JSON-filen. Beroende på bindnings typen kan ytterligare egenskaper krävas. I [kös Ekö konfigurationen](../articles/azure-functions/functions-bindings-storage-queue.md#output---configuration) beskrivs de fält som krävs för en Azure Storage Queue-bindning. Tillägget gör det enkelt att lägga till bindningar i function. JSON-filen. 

Om du vill skapa en bindning högerklickar du på (Ctrl + klicka på macOS) `function.json`-filen i mappen HttpTrigger och väljer **Lägg till bindning...** . Följ anvisningarna för att definiera följande bindnings egenskaper för den nya bindningen:

| Uppmaning | Value | Beskrivning |
| -------- | ----- | ----------- |
| **Välj bindnings riktning** | `out` | Bindningen är en utgående bindning. |
| **Välj bindning med riktning...** | `Azure Queue Storage` | Bindningen är en Azure Storage Queue-bindning. |
| **Namnet som används för att identifiera den här bindningen i din kod** | `msg` | Namn som identifierar den bindnings parameter som refereras till i din kod. |
| **Kön som meddelandet ska skickas till** | `outqueue` | Namnet på kön som bindningen skriver till. När *queueName* inte finns skapar bindningen den när den används första gången. |
| **Välj inställning från "lokal. Setting. JSON"** | `AzureWebJobsStorage` | Namnet på en program inställning som innehåller anslutnings strängen för lagrings kontot. Inställningen `AzureWebJobsStorage` innehåller anslutnings strängen för det lagrings konto som du skapade med Function-appen. |

En bindning läggs till i `bindings`-matrisen i function. JSON-filen, som nu bör se ut som i följande exempel:

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```