
Koden för alla funktioner i en viss funktionsapp finns i rotmappen som innehåller en konfigurationsfil för värden och en eller flera undermappar som innehåller koden för en separat funktion, som i följande exempel:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

Den *host.json* filen innehåller vissa runtime konfiguration och placeras i rotmappen på appen funktion. Mer information om inställningar som är tillgängliga finns [host.json referens](../articles/azure-functions/functions-host-json.md).

Varje funktion har en mapp som innehåller en eller flera kodfiler, function.json-konfiguration och andra beroenden.

