---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 4db460a5dcefb49de3ad2b594d3957cbcf7445c3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592792"
---
I ett C# klass biblioteks projekt definieras bindningarna som binding-attribut i funktions metoden. Filen *Function. JSON* genereras sedan automatiskt baserat på dessa attribut.

Öppna projekt filen *HttpTrigger.cs* och Lägg till följande `using` -instruktion:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Lägg till följande parameter i `Run` metod definitionen:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

Parametern är en `ICollector<T>` typ som representerar en samling meddelanden som skrivs till en utgående bindning när funktionen slutförs. `msg` I det här fallet är utdata en lagrings kö med `outqueue`namnet. Anslutnings strängen för lagrings kontot anges av `StorageAccountAttribute`. Det här attributet anger den inställning som innehåller lagrings kontots anslutnings sträng och kan tillämpas på klass-, metod-eller parameter nivå. I det här fallet kan du utelämna `StorageAccountAttribute` eftersom du redan använder standard lagrings kontot.

Definitionen av Run-metoden bör nu se ut så här:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```
