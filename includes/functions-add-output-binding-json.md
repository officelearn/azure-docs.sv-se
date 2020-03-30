---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191077"
---
Bindningsattribut definieras direkt i filen function.json. Beroende på bindningstypen kan ytterligare egenskaper krävas. [Köutdatakonfigurationen](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) beskriver de fält som krävs för en Azure Storage-köbindning. Tillägget gör det enkelt att lägga till bindningar i filen function.json. 

Om du vill skapa en bindning högerklickar du `function.json` (Ctrl+klicka på macOS) filen i mappen HttpTrigger och väljer **Lägg till bindning...**. Följ anvisningarna för att definiera följande bindningsegenskaper för den nya bindningen:

| Uppmaning | Värde | Beskrivning |
| -------- | ----- | ----------- |
| **Välj bindningsriktning** | `out` | Bindningen är en utdatabindning. |
| **Välj bindning med riktning...** | `Azure Queue Storage` | Bindningen är en Azure Storage-köbindning. |
| **Namnet som används för att identifiera den här bindningen i koden** | `msg` | Namn som identifierar bindningsparametern som refereras i koden. |
| **Kön som meddelandet ska skickas till** | `outqueue` | Namnet på kön som bindningen skriver till. När *könamnet* inte finns skapar bindningen den vid första användningen. |
| **Välj inställning från "local.setting.json"** | `AzureWebJobsStorage` | Namnet på en programinställning som innehåller anslutningssträngen för lagringskontot. Inställningen `AzureWebJobsStorage` innehåller anslutningssträngen för det lagringskonto som du skapade med funktionsappen. |

En bindning läggs `bindings` till i matrisen i din function.json, som ska se ut så här:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::