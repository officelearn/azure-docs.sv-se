---
title: Använda retur värde från en Azure-funktion
description: Lär dig hantera retur värden för Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 8dd5a4d9d869c879ed402c5450690f0a691e1d2c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074406"
---
# <a name="using-the-azure-function-return-value"></a>Använda Azures funktions retur värde

Den här artikeln förklarar hur retur värden fungerar i en funktion.

På språk som har ett retur värde kan du binda en funktions [utgående bindning](./functions-triggers-bindings.md#binding-direction) till returvärdet:

* I ett C# klass bibliotek använder du attributet utgående bindning till metodens retur värde.
* På andra språk ställer du in egenskapen `name` i *Function. JSON* för att `$return`.

Om det finns flera utgående bindningar använder du returvärdet för bara en av dem.

I C# och C# skript är alternativa sätt att skicka data till en utgående bindning `out` parametrar och [insamlade objekt](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Här är C# koden som använder returvärdet för en utgående bindning, följt av ett asynkront exempel:

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

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Här är utdata-bindningen i *Function. JSON* -filen:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Här är C# skript koden, följt av ett asynkront exempel:

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

# <a name="ftabfsharp"></a>[F#](#tab/fsharp)

Här är utdata-bindningen i *Function. JSON* -filen:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Här är den F# kod:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

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

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera Azure Functions bindnings fel](./functions-bindings-errors.md)
