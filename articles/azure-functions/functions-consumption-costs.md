---
title: Uppskatta kostnader för förbrukningsplan i Azure-funktioner
description: Lär dig hur du bättre kan uppskatta de kostnader som du kan ådra dig när du kör din funktionsapp i en förbrukningsplan i Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963996"
---
# <a name="estimating-consumption-plan-costs"></a>Uppskatta kostnader för förbrukningsplan

Det finns för närvarande tre typer av värdplaner för en app som körs i Azure Functions, där varje plan har en egen prismodell: 

| Planera | Beskrivning |
| ---- | ----------- |
| [**Förbrukning**](functions-scale.md#consumption-plan) | Du debiteras bara för den tid som funktionsappen körs. Den här planen innehåller en kostnadsfri[prissida] för [bidrag]per prenumeration.|
| [**Premium**](functions-scale.md#premium-plan) | Ger dig samma funktioner och skalningsmekanism som förbrukningsplanen, men med förbättrad prestanda och VNET-åtkomst. Kostnaden baseras på din valda prisnivå. Mer information finns i [Azure Functions Premium-abonnemanget](functions-premium-plan.md). |
| [**Dedikerad (apptjänst)**](functions-scale.md#app-service-plan) <br/>(basnivå eller högre) | När du behöver köra i dedikerade virtuella datorer eller isolerat kan du använda anpassade avbildningar eller vill använda din kapacitet för appserviceabonnemanget. Använder [vanlig fakturering för appserviceabonnemang](https://azure.microsoft.com/pricing/details/app-service/). Kostnaden baseras på din valda prisnivå.|

Du valde den plan som bäst stöder din funktion prestanda och kostnadskrav. Mer information finns i [Azure Functions skala och vara värd .](functions-scale.md)

Den här artikeln behandlar endast förbrukningsplanen, eftersom den här planen resulterar i rörliga kostnader. Den här artikeln ersätter vanliga frågor och svar [om förbrukningsplan kostnadsfakturor.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

Varaktiga funktioner kan också köras i en förbrukningsplan. Mer information om kostnadsöverväganden när du använder varaktiga funktioner finns i [fakturering av varaktiga funktioner](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Kostnader för förbrukningsplan

*Körningskostnaden för* en enda funktionskörning mäts i *GB-sekunder*. Körningskostnaden beräknas genom att kombinera dess minnesanvändning med dess körningstid. En funktion som körs för längre kostnader mer, liksom en funktion som förbrukar mer minne. 

Tänk dig ett fall där mängden minne som används av funktionen förblir konstant. I det här fallet är beräkning av kostnaden enkel multiplikation. Anta till exempel att din funktion förbrukas 0,5 GB i 3 sekunder. Då är `0.5GB * 3s = 1.5 GB-seconds`körningskostnaden . 

Eftersom minnesanvändningen ändras med tiden är beräkningen i huvudsak den integrerade minnesanvändningen över tid.  Systemet gör denna beräkning genom att sampling minnesanvändningen av processen (tillsammans med underordnade processer) med jämna mellanrum. Som nämnts på [prissidan]avrundas minnesanvändningen uppåt till närmaste bucket 128 MB. När din process använder 160 MB debiteras du för 256 MB. Beräkningen tar hänsyn till samtidighet, vilket är flera samtidiga funktionskörningar i samma process.

> [!NOTE]
> Cpu-användning beaktas inte direkt i körningskostnaden, men det kan påverka kostnaden när den påverkar funktionens körningstid.

## <a name="other-related-costs"></a>Övriga relaterade kostnader

När du beräknar den totala kostnaden för att köra dina funktioner i alla planer, kom ihåg att funktionskörningen använder flera andra Azure-tjänster, som faktureras separat. Vid beräkning av priser för funktionsappar kräver alla utlösare och bindningar som du har som integrerar med andra Azure-tjänster att du skapar och betalar för dessa ytterligare tjänster. 

För funktioner som körs i en förbrukningsplan är den totala kostnaden körningskostnaden för dina funktioner, plus kostnaden för bandbredd och tilläggstjänster. 

När du beräknar de totala kostnaderna för din funktionsapp och relaterade tjänster använder du [Azure-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Relaterad kostnad | Beskrivning |
| ------------ | ----------- |
| **Lagringskonto** | Varje funktionsapp kräver att du har ett associerat [Azure Storage-konto](../storage/common/storage-introduction.md#types-of-storage-accounts)för allmänt ändamål , som [faktureras separat](https://azure.microsoft.com/pricing/details/storage/). Det här kontot används internt av funktionskörningen, men du kan också använda det för lagringsutlösare och bindningar. Om du inte har ett lagringskonto skapas ett för dig när funktionsappen skapas. Mer information finns i [Krav på lagringskonto](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Funktioner är beroende av [Application Insights](../azure-monitor/app/app-insights-overview.md) för att ge en högpresterande övervakningsupplevelse för dina funktionsappar. Även om det inte krävs, bör du [aktivera Application Insights integration](functions-monitoring.md#enable-application-insights-integration). Ett fritt beviljande av telemetridata ingår varje månad. Mer information finns [på prissidan för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). |
| **Nätverksbandbredd** | Du betalar inte för dataöverföring mellan Azure-tjänster i samma region. Du kan dock ådra dig kostnader för utgående dataöverföringar till en annan region eller utanför Azure. Mer information finns i [Information om bandbreddspriser](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Beteenden som påverkar körningstiden

Följande funktioners beteenden kan påverka körningstiden:

+ **Utlösare och bindningar**: Den tid det tar att läsa indata från och skriva utdata till [dina funktionsbindningar](functions-triggers-bindings.md) räknas som körningstid. När din funktion till exempel använder en utdatabindning för att skriva ett meddelande till en Azure-lagringskö, innehåller körningstiden den tid det tar att skriva meddelandet till kön, vilket ingår i beräkningen av funktionskostnaden. 

+ **Asynkron körning**: Den tid som din funktion väntar på`await` resultatet av en asynkron begäran (i C#) räknas som körningstid. Beräkningen av GB-sekund baseras på funktionens start- och sluttid och minnesanvändningen under den perioden. Vad som händer under den tiden när det gäller CPU-aktivitet räknas inte in i beräkningen. Du kanske kan minska kostnaderna under asynkrona åtgärder med hjälp av [varaktiga funktioner](durable/durable-functions-overview.md). Du faktureras inte för tid som spenderas på väntar i orchestrator funktioner.

## <a name="view-execution-data"></a>Visa körningsdata

I [fakturan](/azure/billing/billing-download-azure-invoice)kan du visa kostnadsrelaterade data **för totala körningar - funktioner** och **körningstid - funktioner**, tillsammans med de faktiska fakturerade kostnaderna. Dessa fakturadata är dock en månatlig mängd för en tidigare fakturaperiod. 

För att bättre förstå kostnadseffekten av dina funktioner kan du använda Azure Monitor för att visa kostnadsrelaterade mått som för närvarande genereras av dina funktionsappar. Du kan använda antingen [Azure Monitor-måttutforskare i](../azure-monitor/platform/metrics-getting-started.md) [Azure-portalen] eller REST-API:er för att hämta dessa data.

### <a name="monitor-metrics-explorer"></a>Utforska statistikutforskaren övervaka mått

Använd [Azure Monitor-statistikutforskaren](../azure-monitor/platform/metrics-getting-started.md) för att visa kostnadsrelaterade data för funktionsapparna för förbrukningsplan i ett grafiskt format. 

1. Högst upp i [Azure-portalen] i **Sök tjänster söker resurser och dokument** efter `monitor` och väljer **Övervaka** under **Tjänster**.

1. Till vänster väljer du Mått**Välj en resurs**och använder sedan inställningarna under bilden för att välja **funktionsapp.** > 

    ![Välj funktionsappresurs](media/functions-consumption-costing/select-a-resource.png)

      
    |Inställning  |Föreslaget värde  |Beskrivning  |
    |---------|---------|---------|
    | Prenumeration    |  Din prenumeration  | Prenumerationen med din funktionsapp.  |
    | Resursgrupp     | Din resursgrupp  | Resursgruppen som innehåller din funktionsapp.   |
    | Resurstyp     |  App Services | Funktionsappar visas som App Services-instanser i Monitor. |
    | Resurs     |  Din funktionsapp  | Funktionsappen som ska övervakas.        |

1. Välj **Använd** om du vill välja funktionsappen som den resurs som ska övervakas.

1. Från **Mått**väljer du **Antal funktionskörning** och **Summa** för **aggregering**. Detta lägger till summan av körningsantalet under vald period i diagrammet.

    ![Definiera ett funktionsappmått som ska läggas till i diagrammet](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Välj **Lägg till mått** och upprepa steg 2-4 om du vill lägga till **funktionsutförandeenheter** i diagrammet. 

Det resulterande diagrammet innehåller summorna för båda körningsmåtten i det valda tidsintervallet, som i det här fallet är två timmar.

![Diagram över funktionskörningsantal och körningsenheter](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Eftersom antalet körningsenheter är så mycket större än antalet körningar visar diagrammet bara körningsenheter.

Diagrammet visar totalt 1,11 `Function Execution Units` miljarder som förbrukats under en tvåtimmarsperiod, mätt i MB-millisekunder. Om du vill konvertera till GB-sekunder dividerar du med 1024000. I det här exemplet `1110000000 / 1024000 = 1083.98` förbrukas GB-sekunder i funktionsappen. Du kan ta det här värdet och multiplicera med det aktuella priset för körningstid på[prissidan] [Funktioner,]vilket ger dig kostnaden för dessa två timmar, förutsatt att du redan har använt några kostnadsfria beviljande av exekveringstid. 

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/) har kommandon för att hämta mått. Du kan använda CLI från en lokal kommandomiljö eller direkt från portalen med [Azure Cloud Shell](../cloud-shell/overview.md). Följande kommando [för az-övervakarestatistik returnerar](/cli/azure/monitor/metrics#az-monitor-metrics-list) till exempel timdata under samma tidsperiod som användes före.

Se till `<AZURE_SUBSCRIPTON_ID>` att ersätta med ditt Azure-prenumerations-ID som kör kommandot.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Det här kommandot returnerar en JSON-nyttolast som ser ut som följande exempel:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Det här svaret `2019-09-11T21:46` visar `2019-09-11T23:18`att appen från till , förbrukade 11100000000 MB millisekunder (1083,98 GB sekunder).

## <a name="determine-memory-usage"></a>Ta reda på minnesanvändning

Funktionskörningsenheter är en kombination av körningstid och minnesanvändning, vilket gör det till ett svårt mått för att förstå minnesanvändning. Minnesdata är inte ett mått som för närvarande är tillgängligt via Azure Monitor. Men om du vill optimera minnesanvändningen för din app kan du använda prestandaräknaredata som samlas in av Application Insights.  

Om du inte redan har gjort det [aktiverar du Application Insights i funktionsappen](functions-monitoring.md#enable-application-insights-integration). När den här integreringen är aktiverad kan du [fråga om telemetridata i portalen](functions-monitoring.md#query-telemetry-data).  

Under **Övervakning**väljer du **Loggar (Analytics)** och kopierar sedan följande telemetrifråga och klistrar in den i frågefönstret och väljer **Kör**. Den här frågan returnerar den totala minnesanvändningen vid varje samplingstillfälle.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Resultaten ser ut som följande exempel:

| tidsstämpel \[UTC\]          | namn          | värde       |
|----------------------------|---------------|-------------|
| 2019-09-12, 01:05:14\.947 | Privata byte | 209,932,288 |
| 2019-09-12, 01:06:14\.994 | Privata byte | 212,189,184 |
| 2019-09-12, 01:06:30\.010 | Privata byte | 231,714,816 |
| 2019-09-12, 01:07:15\.040 | Privata byte | 210,591,744 |
| 2019-09-12, 01:12:16\.285 | Privata byte | 216,285,184 |
| 2019-09-12, 01:12:31\.376 | Privata byte | 235,806,720 |

## <a name="function-level-metrics"></a>Mått på funktionsnivå

Azure Monitor spårar mått på resursnivå, som för Funktioner är funktionsappen. Application Insights-integrering avger mått per funktion. Här är en exempelanalysfråga för att få den genomsnittliga varaktigheten för en funktion:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| namn                       | genomsnittligaDurationMilliseconds |
|----------------------------|-----------------------------|
| KöTrigger AvgDurationMs | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| KöTrigger MinDurationMs | 8\.522                      |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om övervakning av funktionsappar](functions-monitoring.md)

[prissida]:https://azure.microsoft.com/pricing/details/functions/
[Azure-portal]: https://portal.azure.com
