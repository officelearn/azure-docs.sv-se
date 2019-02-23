---
title: Azure Functions-utlösare och bindningen exempel
description: Lär dig att konfigurera Azure Function-bindningar
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 6d5f9b171a4efc5e52d281655de143ac9d40d437
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739741"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Azure Functions-utlösare och bindningen exempel

Den här artikeln visar hur du konfigurerar en [utlösare och bindningar](./functions-triggers-bindings.md) i en Azure-funktion.

Anta att du vill skriva en ny rad till Azure Table storage när ett nytt meddelande visas i Azure Queue storage. Det här scenariot kan implementeras med hjälp av en kö för Azure storage-utlösare och en Azure Table storage-utdatabindning. 

Här är en *function.json* -filen för det här scenariot. 

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

Det första elementet i den `bindings` matrisen är Queue storage-utlösare. Den `type` och `direction` egenskaper identifiera utlösaren. Den `name` egensapen identifierar funktionsparametern som tar emot kö meddelandeinnehåll. Namnet på kön som ska övervaka `queueName`, och anslutningssträngen i appinställningen som identifieras av `connection`.

Det andra elementet i den `bindings` matrisen är Azure Table Storage-utdatabindning. Den `type` och `direction` egenskaper identifiera bindningen. Den `name` egenskap anger hur funktionen ger den nya tabellraden, i det här fallet returnerar värdet med hjälp av funktionen. Namnet på tabellen används i `tableName`, och anslutningssträngen i appinställningen som identifieras av `connection`.

Visa och redigera innehållet i *function.json* i Azure-portalen klickar du på den **Avancerad redigerare** alternativet på den **integrera** i din funktion.

> [!NOTE]
> Värdet för `connection` är namnet på en appinställning som innehåller anslutningssträngen inte anslutningssträngen själva. Bindningar använda anslutningen strängar som lagras i appen inställningar för att tillämpa bäst öva som *function.json* innehåller inte service hemligheter.

## <a name="c-script-example"></a>Exempel på C#-skript

Här är C#-skriptkoden som fungerar med den här utlösaren och bindning. Observera att namnet på den parameter som innehåller meddelandeinnehållet kön är `order`; det här namnet är obligatoriskt eftersom den `name` egenskapsvärdet i *function.json* är `order` 

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

Samma *function.json* filen kan användas med en JavaScript-funktion:

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

## <a name="class-library-example"></a>Class library-exempel

I en klassbiblioteket, samma utlösare och bindningsinformation &mdash; kön och tabellen namn, storage-konton kan fungera parametrar för indata och utdata &mdash; tillhandahålls av attribut i stället för en function.json-fil. Här är ett exempel:

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

Nu har du en fungerande-funktion som utlöses av Azure-tabellagring som matar ut data till en kö.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Azure Functions bindning mönster för uttryck](./functions-bindings-expressions-patterns.md)