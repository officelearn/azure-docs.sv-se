Det finns vissa begränsningar för antalet mätvärden och händelser per program (det vill säga per instrumentationsnyckel). Gränserna beror på vilken [prisplan](https://azure.microsoft.com/pricing/details/application-insights/) du väljer.

| **Resurs** | **Standardgräns** | **Obs!**
| --- | --- | --- |
| Totala data per dag | 100 GB | Du kan minska datamängden genom att ange ett tak. Om du behöver mer kan du höja gränsen upp till 1 000 GB från portalen. Skicka e-post till kapacitet som är större än 1 000 GB AIDataCap@microsoft.com.
| Kostnadsfria data per månad<br/> (Basic-prisplan) | 1 GB | Ytterligare data debiteras per gigabyte.
| Begränsning | 32 K händelser/sek | Gränser är mätt under en minut.
| Datakvarhållning | 90 dagar | Den här resursen är för [Search](../articles/application-insights/app-insights-diagnostic-search.md), [Analytics](../articles/application-insights/app-insights-analytics.md) och [Metrics Explorer](../articles/application-insights/app-insights-metrics-explorer.md).
| [Flerstegstest för tillgänglighet](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) (kvarhållning av detaljerade resultat) | 90 dagar | Den här resursen innehåller detaljerade resultat för varje steg.
| Maximal storlek | 64 KB | 
| Namnlängd för egenskaper och mätvärden | 150 | Se [skriver scheman](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| Stränglängd för egenskapsvärde | 8 192 | Se [skriver scheman](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| Längd för spårnings- och undantagsmeddelande | 10 k | Se [skriver scheman](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| [Tillgänglighetstester](../articles/application-insights/app-insights-monitor-web-app-availability.md) (antal per app)  | 10 |
| [Profileraren](../articles/application-insights/app-insights-profiler.md) datalagring | fem dagar |
| [Profileraren](../articles/application-insights/app-insights-profiler.md) data som skickats per dag | 10GB |

Mer information finns i [Om priser och kvoter i Application Insights](../articles/application-insights/app-insights-pricing.md).

