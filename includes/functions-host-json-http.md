```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|routePrefix|api|Prefixet för vägen som gäller för alla vägar. Ta bort prefixet som standard med hjälp av en tom sträng. |
|maxOutstandingRequests|-1|Maximalt antal väntande förfrågningar som hålls vid en given tidpunkt. Den här begränsningen inkluderar begäranden som står i kö men har inte startats, samt eventuella i förloppet körningar. Alla inkommande förfrågningar via den här gränsen avvisas med ett 429 ”upptagen” svar. Som kan anropare för tidsbaserade försök strategier och hjälper dig också att styra maximala begäran svarstider. Detta anger endast queuing som sker inom skriptsökvägen värden körning. Andra köer, till exempel ASP.NET begärandekön kommer fortfarande att gälla och påverkas inte av den här inställningen. Standardvärdet är obegränsad.|
|maxConcurrentRequests|-1|Maximalt antal http-funktioner som körs parallellt. Detta låter dig kontrollen samtidighet, som kan hjälpa dig att hantera resursutnyttjande. Du kan till exempel ha en http-funktion som kräver mycket systemresurser (minne / / processorsocket) så att det orsakar problem när samtidighet är för högt. Du kanske har en funktion som gör utgående förfrågningar till en tredje part service och anropen måste vara begränsad hastighet. I dessa fall kan tillämpa en begränsning här hjälpa. Standardvärdet är obegränsad.|
|dynamicThrottlesEnabled|false|När aktiverat, att den här inställningen gör pipelinen för behandling av begäranden att regelbundet kontrollera systemprestanda prestandaräknare som trådar/anslutningar/processer/cpu/minne/etc. och begäranden om något av dessa räknare är över tröskelvärdet för inbyggd hög (80%) avvisade med svaret 429 ”upptagen” förrän counter(s) återgå till normal nivå.|
