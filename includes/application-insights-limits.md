Det finns vissa begränsningar för antalet mätvärden och händelser per program (det vill säga per instrumentationsnyckel). Gränserna beror på vilken [prisplan](https://azure.microsoft.com/pricing/details/application-insights/) du väljer.

| **Resurs** | **Standardgräns** | **Obs!**
| --- | --- | --- |
| Totala data per dag | 500 GB | Du kan minska datamängden genom att ange ett tak. Om du behöver mer skickar du ett e-postmeddelande till AIDataCap@microsoft.com.
| Kostnadsfria data per månad<br/> (Basic-prisplan) | 1 GB | Ytterligare data debiteras per gigabyte.
| Begränsning | 16&000; K händelser/sek | Gränser är mätt under en minut.
| Datakvarhållning | 90 dagar | Den här resursen är för [Search](../articles/application-insights/app-insights-diagnostic-search.md), [Analytics](../articles/application-insights/app-insights-analytics.md) och [Metrics Explorer](../articles/application-insights/app-insights-metrics-explorer.md).
| [Flerstegstest för tillgänglighet](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) (kvarhållning av detaljerade resultat) | 90 dagar | Den här resursen innehåller detaljerade resultat för varje steg.
| Namnlängd för egenskaper och mätvärden | 150 |
| Stränglängd för egenskapsvärde | 8&192; |
| Längd för spårnings- och undantagsmeddelande | 10&000; |
| [Tillgänglighetstester](../articles/application-insights/app-insights-monitor-web-app-availability.md) (antal per app)  | 10 |

Mer information finns i [Om priser och kvoter i Application Insights](../articles/application-insights/app-insights-pricing.md).


<!--HONumber=Feb17_HO1-->


