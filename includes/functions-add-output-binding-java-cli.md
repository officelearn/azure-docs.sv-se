---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673260"
---
I ett Java-projekt definieras bindningarna som bindande anteckningar på funktionsmetoden. *Filen function.json* skapas sedan automatiskt baserat på dessa anteckningar.

Bläddra till platsen för din _funktionskod under src/main/java_, öppna *function.java-projektfilen* och lägg till följande parameter i metoddefinitionen: `run`

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Parametern `msg` är [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) en typ som representerar en samling strängar som skrivs som meddelanden till en utdatabindning när funktionen är klar. I det här fallet är utdata en lagringskö med namnet `outqueue`. Anslutningssträngen för lagringskontot `connection` anges med metoden. I stället för själva anslutningssträngen skickar du programinställningen som innehåller anslutningssträngen för lagringskonto.

Metoddefinitionen `run` ska nu se ut som följande exempel:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```