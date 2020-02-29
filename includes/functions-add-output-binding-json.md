---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191077"
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

En bindning läggs till i `bindings` matrisen i function. JSON, vilket bör se ut så här:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::