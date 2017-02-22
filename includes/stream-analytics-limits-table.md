---
| Gräns för identifierare | Gräns | Kommentarer |
| --- | --- | --- |
| Maximalt antal enheter för strömning per prenumeration per region |50 |Du kan göra en begäran för att öka strömningsenheterna för din prenumeration till över 50 genom att kontakta [Microsoft Support](https://support.microsoft.com/en-us). |
| Maximalt dataflöde för en strömningsenhet |1 MB/s * |Maximalt dataflöde per SU beror på scenariot. Det faktiska dataflödet kan vara lägre och beror på frågekomplexitet och partitionering. Mer information finns i artikeln [Scale Azure Stream Analytics jobs to increase throughput](../articles/stream-analytics/stream-analytics-scale-jobs.md) (Skala Azure Stream Analytics-jobb för att öka dataflödet). |
| Maximalt antal indata per jobb |60 |Det finns en hård gräns på 60 indata per Stream Analytics-jobb. |
| Maximalt antal utdata per jobb |60 |Det finns en hård gräns på 60 utdata per Stream Analytics-jobb. |
| Maximalt antal funktioner per jobb |60 |Det finns en hård gräns på 60 funktioner per Stream Analytics-jobb. |
| Maximalt antal jobb per region |1500 |Varje prenumeration kan ha upp till 1500 jobb per geografisk region. |
| Referensdatablob MB | 100 | Referensdatablobar får inte överskrida 100 MB var. |



<!--HONumber=Feb17_HO2-->


