---
title: Använda retur värde från en Azure-funktion
description: Lär dig hantera retur värden för Azure Functions
author: craigshoemaker
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 7ba104e288204dfbf3d24f5783bf69682a286553
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74480571"
---
# <a name="using-the-azure-function-return-value"></a>Använda Azures funktions retur värde

Den här artikeln förklarar hur retur värden fungerar i en funktion.

På språk som har ett retur värde kan du binda en funktions [utgående bindning](./functions-triggers-bindings.md#binding-direction) till returvärdet:

* I ett C#-klass bibliotek använder du attributet utgående bindning till metodens retur värde.
* I Java använder du kommentaren utgående bindning till funktions metoden.
* På andra språk ställer du in `name` egenskapen i *Function. JSON* till `$return`.

Om det finns flera utgående bindningar använder du returvärdet för bara en av dem.

I C#-och C#-skript är `out` alternativa sätt att skicka data till en utgående bindning parametrar och [insamlade objekt](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="c"></a>[C #](#tab/csharp)

Här är C#-koden som använder returvärdet för en utgående bindning, följt av ett asynkront exempel:

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

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Här är utdata-bindningen i *Function. JSON* -filen:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Här är C#-skript koden, följt av ett asynkront exempel:

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

# <a name="f"></a>[F#](#tab/fsharp)

Här är utdata-bindningen i *Function. JSON* -filen:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Här är F #-koden:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Här är utdata-bindningen i *Function. JSON* -filen:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

I Java Script går returvärdet i den andra parametern för `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="python"></a>[Python](#tab/python)

Här är utdata-bindningen i *Function. JSON* -filen:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Här är python-koden:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

Här är Java-koden som använder returvärdet för en utgående bindning:

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
> [Hantera Azure Functions bindnings fel](./functions-bindings-errors.md)
