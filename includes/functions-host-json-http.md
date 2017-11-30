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
|maxOutstandingRequests|-1|Maximalt antal väntande förfrågningar som hålls kvar vid en given tidpunkt (-1 innebär unbounded). Gränsen inkluderar begäranden som står i kö men har inte startats, samt alla pågående körningar. Alla inkommande förfrågningar via den här gränsen avvisas med ett 429 ”upptagen” svar. Anropare kan använda det svaret för att använda strategier för tidsbaserade försök igen. Kontrollerar den här inställningen bara queuing som sker inom jobbet värden körning av sökväg. Andra köer, till exempel begärandekön ASP.NET påverkas inte av den här inställningen. |
|maxConcurrentRequests|-1|Maximalt antal HTTP-funktioner som körs parallellt (-1 innebär unbounded). Du kan till exempel ange en gräns om HTTP-funktioner använder för mycket systemresurser när samtidighet är hög. Eller om dina funktioner gör utgående förfrågningar till en tjänst från tredje part, anropen behöva vara begränsad hastighet.|
|dynamicThrottlesEnabled|false|Gör en begäran om bearbetning pipeline att regelbundet kontrollera prestandaräknare för system. Räknare som inkluderar anslutningar, trådar, processer, minne och cpu. Om någon av räknarna som är över en inbyggd tröskelvärdet (80%) avvisas förfrågningar med svaret 429 ”upptagen” förrän counter(s) återgå till normal nivå.|
