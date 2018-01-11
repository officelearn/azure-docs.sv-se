
Koden för alla funktioner i en specifik funktionsapp finns i rotmappen som innehåller en konfigurationsfil för värden och en eller flera undermappar. Varje undermapp innehåller koden för en separat funktion, som i följande exempel:

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

Filen host.json innehåller vissa runtime-specifika konfigurationer och placeras i rotmappen på appen funktion. Information om inställningar som är tillgängliga finns i [host.json referens](../articles/azure-functions/functions-host-json.md).

Varje funktion har en mapp som innehåller en eller flera kodfiler, function.json konfiguration och andra beroenden.

