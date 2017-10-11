Samling av anpassade mått. Använd den här samlingen i rapporten med namnet mått som är associerade med objektet telemetri. Vanliga användningsområden är:
- storleken på Beroendetelemetri nyttolast
- Antal objekt i kö bearbetas av begäran telemetri
- tid kunden tog för att slutföra steg i guiden steg slutförande händelse telemetri.

Du kan fråga [anpassade mått](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) i Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Anpassade mått är kopplade till objektet telemetri som de tillhör. De regleras provtagning telemetri-objektet som innehåller dessa mått. Om du vill spåra ett mått som har ett värde som är oberoende av andra typer av telemetri använda [mått telemetri](../articles/application-insights/app-insights-api-custom-events-metrics.md).

Maximal nyckellängd: 150
