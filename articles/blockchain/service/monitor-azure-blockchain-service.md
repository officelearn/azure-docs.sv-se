---
title: Övervakning av Azure Blockchain-tjänsten (ABS)
description: Övervaka Azure Blockchain-tjänsten via Azure Monitor
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293257"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Övervaka Azure Blockchain-tjänsten via Azure Monitor  

När kunder kör blockchain-scenarier för produktionsklass på Azure Blockchain Service (ABS) blir det viktigt att övervaka resurserna för tillgänglighet, prestanda och åtgärder. I den här artikeln beskrivs övervakningsdata som genereras av Azure Blockchain Service och hur man kan använda de olika funktionerna och integreringarna i Azure Monitor för att analysera och avisera, för att hantera produktionsmiljöer.  

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?

Azure Blockchain Service skapar övervakningsdata med Azure Monitor, som är en fullständig stackövervakningstjänst i Azure som tillhandahåller en komplett uppsättning funktioner för att övervaka dina Azure-resurser. Mer information om Azure Monitor finns i [Övervaka Azure-resurser med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource).
 

Följande avsnitt bygger på den här artikeln genom att beskriva specifika data som samlats in från Azure Blockchain Service och ge exempel för att konfigurera datainsamling och analysera dessa data med Azure-verktyg.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Övervaka data som samlats in från Azure Blockchain Service  

Azure Blockchain-tjänsten samlar in samma typ av övervakningsdata som andra Azure-resurser, som beskrivs i [Övervakningsdata](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) från Azure-resurser. Se [Övervaka Azure Blockchain Service-datareferens](#monitor-azure-blockchain-service-data-reference) för en detaljerad referens av loggar och mått som skapats av Azure Blockchain Service.

Översiktssidan i Azure-portalen för varje Azure Blockchain Service-medlemsresurs innehåller en kort översikt över transaktionerna, inklusive de begäranden som hanteras och bearbetas block. Vissa av dessa data samlas in automatiskt och är tillgängliga för analys när du skapar Azure Blockchain Service-medlemsresursen, medan du kan aktivera ytterligare datainsamling med ytterligare konfiguration.

## <a name="diagnostic-settings"></a>Diagnostikinställningar  

Plattformsmått och aktivitetsloggen samlas in automatiskt, men du måste skapa en diagnostikinställning för att samla in resursloggar eller vidarebefordra dem utanför Azure Monitor. Se [Skapa diagnostikinställning för att samla in plattformsloggar och mått i Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) för den detaljerade processen för att skapa en diagnostikinställning med Azure-portalen, CLI eller PowerShell.

När du skapar en diagnostikinställning anger du vilka kategorier av loggar som ska samlas in. Kategorierna för Azure Blockchain Service visas nedan.

**Blockchain proxy loggar** - Välj den kategori om du vill övervaka NGNIX proxyloggar. Alla kundtransaktionsuppgifter är tillgängliga för granskning och felsökning.  

**Blockchain-programloggar** – Välj kategori för att få loggar över blockchain-programmet som finns hos den hanterade tjänsten. För en MEDLEM i ABS-Kvorum skulle dessa loggar till exempel vara loggarna från quorum själv.  

**Måttbegäranden**: Välj alternativet för att samla in måttdata från Azure Cosmos DB till destinationerna i diagnostikinställningen, som samlas in automatiskt i Azure-mått. Samla in måttdata med resursloggar för att analysera båda typerna av data tillsammans och för att skicka måttdata utanför Azure Monitor.

## <a name="analyze-metric-data"></a>Analysera måttdata  

Du kan analysera mått för Azure Blockchain Service med statistikutforskaren, navigera till fliken Mått under avsnittet Övervakning i ABS-resursbladet. Mer information om hur du använder verktyget finns i Komma igång med Utforskaren för [Azure Metrics.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) De fullständiga måtten för Azure Blockchain-tjänsten finns i standardmåtten för Azure Blockchain Service.The complete metrics for Azure Blockchain Service are in the namespace Azure Blockchain Service standard metrics.

Du kan använda **noddimension** när du lägger till ett filter eller delar upp måtten, som i princip ger måttvärden per transaktionsnoder och validerarnoder för ABS-medlemmen.

## <a name="analyze-log-data"></a>Analysera loggdata

Här är några frågor som du kan ange i loggsökfältet som hjälper dig att övervaka dina Azure Blockchain Service-medlemmar. Dessa frågor fungerar med det [nya språket](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Om du vill fråga felvillkoren i Blockchain-programloggarna använder du följande fråga:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Om du vill fråga felvillkoren i Blockchain-proxyloggarna använder du frågan nedan  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Du kan använda tidsfiltren som är tillgängliga i Azure-loggar för att filtrera frågan för ett visst tidsintervall.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Övervaka Datareferens för Azure Blockchain-tjänsten  

Den här artikeln innehåller en referens för logg- och måttdata som samlats in för att analysera prestanda och tillgänglighet för Azure Blockchain Service.  

### <a name="resource-logs"></a>Resursloggar

Alla resursloggar delar ett gemensamt schema på den högsta nivån med få unika egenskaper som är specifika för blockchain-tjänsten. Du kan läsa artikeln [Schema för resursloggar på den högsta nivån,](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema)information om de specifika egenskaperna för Azure Blockchain-tjänsten beskrivs nedan  

I följande tabell visas egenskaperna för Azure Blockchain-proxyloggar när de samlas in i Azure Monitor Logs eller Azure Storage.  


| Egenskapsnamn  | Beskrivning |
|:---|:---|
| time | Datum och tid (UTC) när åtgärden inträffade. |
| resourceID (resurs-ID)  | Azure Blockchain Service-resursen som loggar är aktiverade för.  |
| category  |För Azure Blockchain-tjänsten är de möjliga värdena **Proxylogs** och **Applicationlogs**. |
| operationName  | Namnet på den åtgärd som representeras av den här händelsen.   |
| Loggnivå  | Som standard aktiverar Azure Blockchain Service **informationsloggnivå.**   |
| NodLokalisering  | Azure-region där blockchain-medlemmen distribueras.  |
| BlockchainNodeName  | Namnet på noden för Azure Blockchain Service-medlemmen som åtgärden utförs på.   |
| EtMethod (etmethod)  | Metoden, som kallas av det underliggande blockchain-protokollet, i Quorum, kan det vara eth_sendTransactions, eth_getBlockByNumber etc.  |
| Agent  | Användaragenten som agerar på uppdrag av en användare, till exempel webbläsaren Mozilla, Edge etc. Exempel på värdena är: "Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414.77"  |
| Kod   | HTTP-felkoder. Vanligtvis är 4XX och 5XX felförhållanden.  |
| NodeHost (nodeHost)  | NODEN DNS-namn.   |
| RequestMethodName | HTTP-metod kallas, de möjliga värdena här är PUT för att skapa medlem, FÅ för att få information om befintlig medlem, TA BORT för borttagning medlem, PATCH för uppdatering av medlem.   |
| BlockchainMemberName (BlockchainMemberName)  | Azure Blockchain-tjänstmedlemsnamnet som tillhandahålls av användaren.  |
| Konsortium | Konsortiets namn enligt användarens.   |
| Remote  | IP för klienten där begäran kommer.  |
| Begäransstorlek  | Storleken på begäran som görs i byte.  |
| Tid för begäran  | Varaktigheten av begäran i millisekunder.|




I följande tabell visas egenskaperna för Azure Blockchain-programloggar.


| Egenskapsnamn  | Beskrivning |
|:---|:---|
| time | Datum och tid (UTC) när åtgärden inträffade. |
| resourceID (resurs-ID)  | Azure Blockchain Service-resursen som loggar är aktiverade för.|
| category  |För Azure Blockchain-tjänsten är värdet som är möjliga **Proxylogs** och **Applicationlogs**.  |
| operationName  | Namnet på den åtgärd som representeras av den här händelsen.   |
| Loggnivå  | Som standard aktiverar Azure Blockchain Service **informationsloggnivå.**   |
| NodLokalisering  | Azure-region där blockchain-medlemmen distribueras.  |
| BlockchainNodeName  | Namnet på noden för Azure Blockchain Service-medlemmen som åtgärden utförs på.   |
| BlockchainMessage (BlockchainMessage)    | Det här fältet innehåller blockchain-programloggen som är datafilernas oformaterade loggar. För ABS-Kvorum, skulle detta ha kvorum loggar. Den har information om vilken typ av loggpost är det som är informativ, fel, varning och en sträng som ger mer information om åtgärden som utförs.   |
| TenantID (Klient-ID)    | Den regionspecifika klienten för Azure Blockchain-tjänsten. Formatet för det https://westlake-rp-prodhär fältet är . <region>.cloudapp.azure.com där regionen anger Azure-regionen för den distribuerade medlemmen.       |
| SourceSystem   | Systemet fyller i loggarna, i det här fallet är det **Azure**.    |



### <a name="metrics"></a>Mått

I följande tabeller visas de plattformsmått som samlats in för Azure Blockchain Service. Alla mått lagras i standardmåtten för Azure **Blockchain Service** i namnområdet Azure Blockchain Service.

En lista över alla Azure Monitor-mått som stöds (inklusive Azure Blockchain Service) finns i [Azure Monitor-mått som stöds](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).

### <a name="blockchain-metrics"></a>Mätvärden för blockkedjor

Följande tabell anger listan över Blockchain-mått som samlas in för Azure Blockchain Service-medlemsresursen.


| Måttnamn | Enhet  |  Sammansättningstyp| Beskrivning   |
|---|---|---|---|
| Väntande transaktioner   | Antal  |  Medel | Antalet transaktioner som väntar på att brytas.   |
| Bearbetade block   | Antal  | Summa  |  Antalet block som bearbetas i varje tidsintervall. För närvarande blockstorleken är 5 sekunder, alltså i en minut varje nod kommer att bearbeta 12 block och 60 block i 5 minuter.   |
|Bearbetade transaktioner    | Antal  | Summa  | Antalet transaktioner som bearbetas i ett block.    |
|Transaktioner i kö    |  Antal | Medel  | Antalet transaktioner som inte omedelbart kan brytas. Det kan bero på att de kom ur funktion och den framtida väntar på att tidigare transaktion ska komma fram. Eller, det kan vara två transaktioner har samma antal som bara används en gång (nonce) och samma gasvärde, därav den andra kan inte brytas.   |

### <a name="connection-metrics"></a>Anslutningsmått  

I följande tabell visas de olika anslutningsmått som samlas in för Azure Blockchain Service-medlemsresursen. Dessa är NGINX proxy mått.


| Måttnamn | Enhet  |  Sammansättningstyp| Beskrivning |
|---|---|---|---|
| Godkända anslutningar   | Antal  |  Summa | Det totala antalet accepterade klientanslutningar.   |
| Aktiva anslutningar  | Antal  | Medel  |  Det aktuella antalet aktiva klientanslutningar, inklusive Väntande anslutningar.    |
|Hanterade anslutningar    | Antal  | Summa  | Det totala antalet hanterade anslutningar. I allmänhet är parametervärdet detsamma som accepterade anslutningar om inte vissa resursgränser har uppnåtts.     |
|Hanterade förfrågningar     |  Antal | Summa  | Det totala antalet klientbegäranden.  |


### <a name="performance-metrics"></a>Prestandamått

I följande tabell visas de prestandamått som samlas in för var och en av noderna i Azure Blockchain-medlemsresursen.  


| Måttnamn | Enhet  |  Sammansättningstyp| Beskrivning   |
|---|---|---|---|
| Procentandel av CPU-användningen   | Procent  |  Max | Procentandelen cpu-användning.     |
| IO läs byte   | Kilobyte   | Summa  |  Summan av IO-läsbyten för alla noder i blockchain-medlemsresursen.      |
|IO skriv byte     | Kilobyte   | Summa  | Summan av IO skriver byte över alla noder i blockchain-medlemsresursen.     |
|Minnesgräns       |  Gigabyte   | Medel    | Maximalt minne tillgängligt för blockkedjan per nod. |
|Minnesanvändning     | Gigabyte  |  Medel | Mängden minne som används i genomsnitt för alla noder.  |
| Procentandel av minnesanvändningen     | Procent   | Medel  |  Procentandelen av det minne som används i genomsnitt för alla noder.       |
|Användning av lagring      | Gigabyte   | Medel  | Gb lagringsutrymme som används i genomsnitt för alla noder.       |


## <a name="next-steps"></a>Efterföljande moment

Läs mer om [Blockchain Data Manager](https://docs.microsoft.com/azure/blockchain/service/data-manager) för att samla in och omvandla blockchain-data till Azure Event Grid.
