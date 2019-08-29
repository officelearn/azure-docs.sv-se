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
ms.openlocfilehash: 1ea7ec0444ba80d3494afba77ad9d7fdabd5f982
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086410"
---
# <a name="using-the-azure-function-return-value"></a>Använda Azures funktions retur värde

Den här artikeln förklarar hur retur värden fungerar i en funktion.

På språk som har ett retur värde kan du binda en funktions [utgående bindning](./functions-triggers-bindings.md#binding-direction) till returvärdet:

* I ett C# klass bibliotek använder du attributet utgående bindning till metodens retur värde.
* På andra språk ställer du in `name` egenskapen i *Function. JSON* till `$return`.

Om det finns flera utgående bindningar använder du returvärdet för bara en av dem.

I C# och C# skript är `out` alternativa sätt att skicka data till en utgående bindning parametrar och insamlade [objekt](functions-reference-csharp.md#writing-multiple-output-values).

Se det språkspecifika exemplet som visar användning av returvärdet:

* [C#](#c-example)
* [C#-skript (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>C#exempel

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

## <a name="c-script-example"></a>C#skript exempel

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

## <a name="f-example"></a>F#exempel

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

## <a name="javascript-example"></a>JavaScript-exempel

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

## <a name="python-example"></a>Python-exempel

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

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera Azure Functions bindnings fel](./functions-bindings-errors.md)
