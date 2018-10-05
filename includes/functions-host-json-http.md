```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|routePrefix|api|Prefix för vägen som gäller för alla vägar. Ta bort standardprefixet med hjälp av en tom sträng. |
|maxOutstandingRequests|200 *|Det maximala antalet väntande förfrågningar som ligger vid en given tidpunkt. Den här gränsen inkluderar begäranden som köas men har inte startats, samt eventuella i förloppet körningar. Alla inkommande begäranden under den här gränsen avvisas med en ”upptagen” 429-svaret. Som tillåter anropare ska kunna använda tidsbaserade återförsöksstrategier och hjälper dig också att styra tillåtna svarstider. Detta styr endast queuing som sker inom skriptsökvägen för körning av värden. Andra köer, till exempel ASP.NET kön kommer fortfarande att tillämpas och påverkas inte av den här inställningen. * Standardvärdet för version 1.x är obegränsade. Standardvärdet för version 2.x i en användningsplan är 200. Standardvärdet för version 2.x i en dedikerad plan är obegränsade.|
|maxConcurrentRequests|100 *|Det maximala antalet http-funktioner som körs parallellt. Detta kan du kontrollen samtidighet, vilket hjälper dig att hantera resursutnyttjande. Du kan till exempel ha en http-funktion som kräver mycket systemresurser (minne/cpu/sockets) så att det orsakar problem när konkurrensen är för hög. Du kanske har en funktion som gör utgående begäranden till en tredjepartstjänst och dessa anrop måste vara frekvensbegränsat. I dessa fall kan tillämpa en begränsning här. * Standardvärdet för version 1.x är obegränsade. Standardvärdet för version 2.x i en användningsplan är 100. Standardvärdet för version 2.x i en dedikerad plan är obegränsade.|
|dynamicThrottlesEnabled|SANT *|När aktiverat att den här inställningen gör pipelinen att regelbundet kontrollera systemprestanda prestandaräknare som trådar/anslutningar/processer/cpu/minne/etc. och om något av dessa räknare är över tröskelvärdet för inbyggd hög (80%), begär avvisade med svar 429 ”upptagen” tills räknare kommer tillbaka till normalnivåerna. * Standardvärdet för version 1.x är FALSKT. Standardvärdet för version 2.x i en användningsplan är true. Standardvärdet för version 2.x i en dedikerad plan är FALSKT.|
