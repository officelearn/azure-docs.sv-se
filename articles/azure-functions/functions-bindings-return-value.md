---
title: Med returvärdet från en Azure-funktion
description: Lär dig att hantera returvärden för Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 4ccfe192eaea94cb9b199bd3c6f0bdacf1685519
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739915"
---
# <a name="using-the-azure-function-return-value"></a>Med hjälp av Azure funktionsreturvärde

Den här artikeln förklarar hur returnera värden work inuti en funktion.

Du kan binda en funktion på språk som har ett returvärde [utdatabindning](./functions-triggers-bindings.md#binding-direction) till det returnera värdet:

* I C# klassbiblioteket, gäller attributet utdata-bindning för returvärdet för metoden.
* I andra språk, ställer du in den `name` -egenskapen i *function.json* till `$return`.

Om det finns flera utdatabindningar kan du använda det returnera värdet för endast en av dem.

I C# och C#-skript, olika sätt att skicka data till en utdatabindning är `out` parametrar och [insamlaren objekt](functions-reference-csharp.md#writing-multiple-output-values).

Se språkspecifika exempel som visar användning av det returnera värdet:

* [C#](#c-example)
* [C#-skript (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>C#-exempel

Här är C#-kod som använder det returnera värdet för en utdatabindning, följt av ett async-exempel:

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

## <a name="c-script-example"></a>Exempel på C#-skript

Här är utdata-bindning i den *function.json* fil:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Här är den C#-skriptkoden, följt av ett async-exempel:

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

## <a name="f-example"></a>F#exempel

Här är utdata-bindning i den *function.json* fil:

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

## <a name="javascript-example"></a>JavaScript-exempel

Här är utdata-bindning i den *function.json* fil:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

I JavaScript, går det returnera värdet i den andra parametern för `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Python-exempel

Här är utdata-bindning i den *function.json* fil:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Här är Python-kod:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Fel i Azure Functions-bindning](./functions-bindings-errors.md)
