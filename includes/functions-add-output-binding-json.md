---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: b118da6b751bc7a1e29ceef10c91dc5e9e3659c2
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474266"
---
Binding-attribut definieras direkt i function. JSON-filen. Beroende på bindnings typen kan ytterligare egenskaper krävas. I [kös Ekö konfigurationen](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) beskrivs de fält som krävs för en Azure Storage Queue-bindning. Tillägget gör det enkelt att lägga till bindningar i function. JSON-filen. 

Om du vill skapa en bindning högerklickar du på (Ctrl + klicka på macOS) `function.json` filen i mappen HttpTrigger och väljer **Lägg till bindning...** . Följ anvisningarna för att definiera följande bindnings egenskaper för den nya bindningen:

| Uppmaning | Värde | Beskrivning |
| -------- | ----- | ----------- |
| **Välj bindnings riktning** | `out` | Bindningen är en utgående bindning. |
| **Välj bindning med riktning...** | `Azure Queue Storage` | Bindningen är en Azure Storage Queue-bindning. |
| **Namnet som används för att identifiera den här bindningen i din kod** | `msg` | Namn som identifierar den bindnings parameter som refereras till i din kod. |
| **Kön som meddelandet ska skickas till** | `outqueue` | Namnet på kön som bindningen skriver till. När *queueName* inte finns skapar bindningen den när den används första gången. |
| **Välj inställning från "lokal. Setting. JSON"** | `AzureWebJobsStorage` | Namnet på en program inställning som innehåller anslutnings strängen för lagrings kontot. Inställningen `AzureWebJobsStorage` innehåller anslutnings strängen för det lagrings konto som du skapade med Function-appen. |

En bindning läggs till i `bindings` matrisen i function. JSON-filen, som nu bör se ut som i följande exempel:

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