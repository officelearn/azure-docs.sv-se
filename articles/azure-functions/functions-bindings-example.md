---
title: Utlösare och bindningsexempel för Azure Functions
description: Lär dig att konfigurera Azure-funktionsbindningar
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8685c0fe02ad6c68918736e857c2015e2bfb4595
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227245"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Utlösare och bindningsexempel för Azure Functions

Den här artikeln visar hur du konfigurerar en [utlösare och bindningar](./functions-triggers-bindings.md) i en Azure-funktion.

Anta att du vill skriva en ny rad till Azure Table storage när ett nytt meddelande visas i Azure Queue storage. Det här scenariot kan implementeras med hjälp av en Azure Queue Storage-utlösare och en Azure Table Storage-utdatabindning. 

Här är en *function.json-fil* för det här scenariot. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Det första elementet i matrisen `bindings` är kölagringsutlösaren. Egenskaperna `type` `direction` och identifierar utlösaren. Egenskapen `name` identifierar funktionsparametern som tar emot innehållet i kömeddelandet. Namnet på kön som ska `queueName`övervakas finns i och anslutningssträngen finns i appinställningen som identifieras av `connection`.

Det andra elementet i matrisen `bindings` är Azure Table Storage-utdatabindningen. Egenskaperna `type` `direction` och identifierar bindningen. Egenskapen `name` anger hur funktionen tillhandahåller den nya tabellraden, i det här fallet med hjälp av funktionsreturvärdet. Namnet på tabellen finns `tableName`i och anslutningssträngen finns `connection`i appinställningen som identifieras av .

Om du vill visa och redigera innehållet i *function.json* i Azure-portalen klickar du på alternativet **Avancerat redigerare** på fliken **Integrera** i din funktion.

> [!NOTE]
> Värdet `connection` är namnet på en appinställning som innehåller anslutningssträngen, inte själva anslutningssträngen. Bindningar använder anslutningssträngar som lagras i appinställningar för att framtvinga bästa praxis som *function.json* inte innehåller tjänsthemligheter.

## <a name="c-script-example"></a>Exempel på C#-skript

Här är C # skriptkod som fungerar med denna utlösare och bindning. Observera att namnet på parametern som tillhandahåller `order`innehållet i kömeddelandet är ; det här namnet `name` krävs eftersom egenskapsvärdet i *function.json* är`order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>JavaScript-exempel

Samma *function.json-fil* kan användas med en JavaScript-funktion:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Exempel på klassbibliotek

I ett klassbibliotek tillhandahålls samma &mdash; utlösar- och bindningsinformationskö och &mdash; tabellnamn, lagringskonton, funktionsparametrar för indata och utdata av attribut i stället för en function.json-fil. Här är ett exempel:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Du har nu en fungerande funktion som utlöses av en Azure-kö och matar ut data till Azure Table Storage.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bindningsuttrycksmönster för Azure Functions](./functions-bindings-expressions-patterns.md)
