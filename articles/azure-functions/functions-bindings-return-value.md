---
title: Använda returvärde från en Azure-funktion
description: Lär dig att hantera returvärden för Azure Functions
author: craigshoemaker
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 7ba104e288204dfbf3d24f5783bf69682a286553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74480571"
---
# <a name="using-the-azure-function-return-value"></a>Med returvärdet för Azure-funktionen

I den här artikeln beskrivs hur returvärden fungerar i en funktion.

På språk som har ett returvärde kan du binda en [funktionsutdatabindning](./functions-triggers-bindings.md#binding-direction) till returvärdet:

* I ett C#-klassbibliotek använder du attributet utdatabindning på metodens returvärde.
* I Java använder du utdatabindningskompeten för funktionsmetoden.
* På andra språk `name` ställer du in egenskapen i *function.json* till `$return`.

Om det finns flera utdatabindningar använder du returvärdet för endast en av dem.

I C# och C#-skript är alternativa sätt `out` att skicka data till en utdatabindning parametrar och [insamlare.](functions-reference-csharp.md#writing-multiple-output-values)

# <a name="c"></a>[C#](#tab/csharp)

Här är C#-kod som använder returvärdet för en utdatabindning, följt av ett asynkrome exempel:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Här är utdatabindningen i *filen function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Här är C# skriptkoden, följt av ett asynkronexempel:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="f"></a>[F #](#tab/fsharp)

Här är utdatabindningen i *filen function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Här är F# koden:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Här är utdatabindningen i *filen function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

I JavaScript går returvärdet i den `context.done`andra parametern för:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="python"></a>[Python](#tab/python)

Här är utdatabindningen i *filen function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Här är Python-koden:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

Här är Java-kod som använder returvärdet för en utdatabindning:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera bindningsfel för Azure Functions](./functions-bindings-errors.md)
