---
title: Prestanda och skalning i varaktiga funktioner – Azure
description: Introduktion till tillägget varaktiga funktioner för Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 110f393e723c7e784a4bd7e79559dd9d55147140
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599440"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestanda och skalning i varaktiga funktioner (Azure-funktioner)

För att optimera prestanda och skalbarhet, är det viktigt att förstå de unika skalning egenskaperna för [varaktiga funktioner](durable-functions-overview.md).

Du måste förstå några av information om den underliggande providern i Azure Storage för att förstå beteendet skala.

## <a name="history-table"></a>Historiktabellen

Den **historik** tabell är en Azure Storage-tabell som innehåller tidigare händelser för alla orchestration-instanser i en aktivitet hubb. Namnet på den här tabellen är i formatet *TaskHubName*historik. Eftersom instanser kör läggs nya rader till den här tabellen. Partitionsnyckeln för den här tabellen är härledd från instans-ID för orchestration. Ett instans-ID är slumpmässig i de flesta fall, vilket säkerställer att optimala distribution av interna partitioner i Azure Storage.

När en orchestration-instans måste köras, laddas de lämpliga raderna i tabellen Historik i minnet. Dessa *Händelsehistorik* sedan skickas vidare till orchestrator Funktionskoden gå tillbaka till den till dess tidigare kontrollpunkt tillstånd. Användning av körningstiden att återskapa tillstånd på det här sättet påverkas av den [händelse källa mönster](https://docs.microsoft.com/en-us/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Instanser tabell

Den **instanser** tabell är en annan Azure Storage-tabell som innehåller status för alla orchestration-instanser i en aktivitet hubb. Eftersom instanser skapas läggs nya rader till i den här tabellen. Partitionsnyckeln för den här tabellen är orchestration instans-ID och Radnyckeln är en fast konstant. Det finns en rad per orchestration-instans.

Den här tabellen används för att uppfylla instans frågebegäranden från den [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) API samt de [status frågan HTTP API](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-http-api#get-instance-status). Den förvaras överensstämmelse med innehållet i den **historik** tabellen tidigare nämnts. Användning av en separat Azure Storage-tabell för att effektivt uppfylla instans frågeåtgärder i det här sättet påverkas av den [mönster för kommandot och fråga ansvar ansvarsfördelning (CQRS)](https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Intern Kölängd utlösare

Orchestrator-funktioner och funktioner för aktiviteten initieras både av interna köer i appen funktionen uppgiften hubb. Med hjälp av köer på detta sätt ger tillförlitliga ”på-minst en gång” meddelande leveransgarantier. Det finns två typer av köer i varaktiga funktioner: den **kontroll kön** och **arbetsobjekt kön**.

### <a name="the-work-item-queue"></a>Arbetsobjekt kön

Det finns en kö för arbetsobjekt per aktivitet hubben i varaktiga funktioner. Det är en enkel kö och fungerar på samma sätt som andra `queueTrigger` kön i Azure Functions. Den här kön används för att utlösa tillståndslös *aktivitet funktioner* av dequeueing ett enda meddelande i taget. Var och en av dessa meddelanden innehåller funktionen aktivitetsindata och ytterligare metadata, till exempel vilken funktion som ska köras. När ett program för beständig funktioner skalas ut till flera virtuella datorer kan dessa virtuella datorer som alla konkurrerar att förvärva arbete från arbetsobjekt kön.

### <a name="control-queues"></a>Kontrollen kö(er)

Det finns flera *styra köer* per aktivitet hubben i varaktiga funktioner. En *kontroll kön* är mer avancerad än enklare arbetsobjekt kön. Kontrollen köer för att utlösa tillståndskänslig orchestrator-funktioner. Eftersom instanserna för orchestrator-funktionen är tillståndskänslig singletons, går det inte att använda en konkurrerande konsument-modell för att fördela belastningen över virtuella datorer. I stället orchestrator meddelanden är belastningsutjämning i kontrollen köer. Mer information om det här problemet finns i följande avsnitt.

Kontrollen köer innehåller en mängd olika orchestration livscykel meddelandetyper. Exempel är [orchestrator kontrollmeddelanden](durable-functions-instance-management.md), aktivitet funktionen *svar* meddelanden och meddelanden som timer. Upp till 32 meddelanden kommer att tagits bort från en kontroll kön i en avsökning. Dessa meddelanden innehåller nyttolasten samt metadata, inklusive vilka orchestration-instans som den är avsedd för. Om flera dequeued meddelanden är avsedda för samma orchestration-instans, kommer att behandlas som en batch.

## <a name="storage-account-selection"></a>Val av tjänstkonto för lagring

Köer, tabeller och blobbar som används av beständiga funktioner skapas med i ett konfigurerat Azure Storage-konto. Kan ange kontot som ska användas med den `durableTask/azureStorageConnectionStringName` i **host.json** fil.

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

Om inget annat anges, standard `AzureWebJobsStorage` lagringskontot används. För prestanda känslig arbetsbelastningar dock rekommenderas konfigurera ett icke-förvalt lagringskonto. Beständiga funktioner använder Azure Storage kraftigt och använder ett dedikerat lagringskonto isolerar lagringskvoten på varaktiga funktioner från den interna användningen av Azure Functions-värden.

## <a name="orchestrator-scale-out"></a>Orchestrator skalbar

Funktioner för aktiviteten är tillståndslösa och skaländras ut automatiskt genom att lägga till virtuella datorer. Orchestrator-funktioner, å andra sidan är *partitionerade* över en eller fler köer för kontrollen. Antalet köer för kontrollen har definierats i den **host.json** fil. Följande exempel host.json fragment uppsättningar av `durableTask/partitionCount` egenskapen `3`.

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```
En uppgift hubb kan konfigureras med mellan 1 och 16 partitioner. Om inget anges är antalet partitioner standard **4**.

När skala ut till flera funktionen värden instanser (vanligtvis på olika virtuella datorer) hämtar varje instans ett lås på en kontroll köer. Dessa Lås implementeras internt som blob storage-lån och se till att en orchestration-instans körs bara på en enda värd-instans i taget. Om en aktivitet hubb konfigureras med tre kontrollen köer, kan orchestration-instanser vara belastningsutjämnad över upp till tre virtuella datorer. Ytterligare virtuella datorer kan läggas till öka kapaciteten för funktionen aktivitetskörningen.

Följande diagram illustrerar hur Azure Functions värden samverkar med lagring entiteter i en miljö som skalats ut.

![Skala diagram](media/durable-functions-perf-and-scale/scale-diagram.png)

I föregående diagram visas alla virtuella datorer konkurrerar om meddelanden i kön arbetsobjekt. Dock endast tre virtuella datorer kan hämta meddelanden från kontrollen köer och varje virtuell dator låser en enskild kontroll-kö.

Orchestration-instanser är fördelade på alla instanser av kontrollen kön. Distributionen görs av hash-instans-ID för orchestration. Instans-ID: N som standard är slumpmässig GUID, se till att instanser jämnt fördelade över alla köer för kontrollen.

Generellt sett är avsedda att lightweight orchestrator-funktioner och bör inte kräver mycket datorkraft. Det är därför inte nödvändigt att skapa ett stort antal kontrollen kön partitioner få bra genomströmning. De flesta av tunga arbetet bör göras i tillståndslös aktivitet funktioner som kan skalas ut oändligt.

## <a name="auto-scale"></a>Autoskala

Som med alla Azure Functions körs i förbrukning planen varaktiga Functions stöder Autoskala via den [Azure Functions skala controller](functions-scale.md#runtime-scaling). Skala Controller övervakar svarstiden för alla köer genom att utfärda regelbundet _titt_ kommandon. Baserat på svarstiderna peeked meddelanden, besluta skala Controller om du vill lägga till eller ta bort virtuella datorer.

Om skala styrenhet avgör att kontrollen kön meddelandet svarstiderna är för högt, läggs VM-instanser tills meddelandet svarstiden minskas till en godtagbar nivå eller den når antalet partitioner kontrollen kön. På liknande sätt kan skala domänkontrollant kontinuerligt lägger till VM-instanser om arbetsobjektet kön svarstiderna är hög, oavsett antalet partitioner.

## <a name="thread-usage"></a>Tråden användning

Orchestrator-funktioner utförs på en enkel tråd så att körningen kan vara entydig över många repetitioner. På grund av den här Enkeltrådig körning är det viktigt att orchestrator-funktionen trådar inte utföra processorintensiva uppgifter, gör i/o eller blockera av någon anledning. Allt arbete som kan kräva att i/o, blockera, eller flera trådar ska flyttas till aktiviteten funktioner.

Aktiviteten har samma funktioner som vanlig kö-utlösta funktioner. De kan på ett säkert sätt göra i/o, utföra CPU-intensiv åtgärder och använder flera trådar. Eftersom aktiviteten utlösare är tillståndslös, kan de fritt skala ut till ett obegränsat antal virtuella datorer.

## <a name="concurrency-throttles"></a>Concurrency begränsar

Azure Functions stöder flera funktioner samtidigt i en enda app-instansen körs. Den här samtidig körning ökar parallellitet och minimerar antalet ”kallstart” som en typisk app får över tid. Hög samtidighet kan dock medföra minnesanvändningen är hög per-VM. Beroende på behov av appen funktionen, kan det vara nödvändigt att begränsa per instans samtidighet för att undvika risken för slut på minne i situationer med hög belastning.

Båda aktivitet funktionen och orchestrator-funktionen samtidighet gränser kan konfigureras i den **host.json** fil. De relevanta inställningarna är `durableTask/maxConcurrentActivityFunctions` och `durableTask/maxConcurrentOrchestratorFunctions` respektive.

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
  }
}
```

I det förra exemplet kan högst 10 orchestrator-funktioner och 10 aktivitet funktioner köras på en enda virtuell dator samtidigt. Om inget anges är antalet samtidiga aktivitet och orchestrator-funktionen körningar högst 10 X antal kärnor på den virtuella datorn.

> [!NOTE]
> De här inställningarna är användbara för att hantera minne och CPU-användning på en enda virtuell dator. När skala ut över flera virtuella datorer, men har varje virtuell dator en egen uppsättning uppsättning gränser. De här inställningarna kan inte användas för att styra samtidighet på global nivå.

## <a name="orchestrator-function-replay"></a>Spela upp orchestrator-funktion
Som tidigare nämnts orchestrator funktioner spelas med innehållet i den **historik** tabell. Som standard spelas Funktionskoden orchestrator varje gång en grupp med meddelanden har tagits bort från en kontroll kön.

Problemet aggressivt replay kan inaktiveras genom att aktivera **utökad sessioner**. När utökad sessioner är aktiverade, förvaras orchestrator funktionen instanser i minnet kan längre och nya meddelanden bearbetas utan en fullständig repetitionsattacker. Utökade sessionerna aktiveras genom att ange `durableTask/extendedSessionsEnabled` till `true` i den **host.json** fil. Den `durableTask/extendedSessionIdleTimeoutInSeconds` inställningen används för att styra hur länge en inaktiv session ska hållas i minnet:

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Vanliga effekten av att aktivera utökad sessioner minskar i/o mot Azure Storage-konto och övergripande bättre genomflöde.

En potentiell Nackdelen med den här funktionen är dock inaktiv orchestrator funktionen förblir instanser i minnet längre. Det finns två effekter vara medveten om:

1. Ökning i funktionen app minnesanvändning.
2. Minska den totala genomflödet i om det finns många samtidiga, tillfällig orchestrator funktionen körningar.

Exempelvis om `durableTask/extendedSessionIdleTimeoutInSeconds` är inställd på 30 sekunder sedan en tillfällig orchestrator funktionen avsnitt som körs i mindre än 1 sekund fortfarande innehar minne i 30 sekunder. Den också räknas av mot de `durableTask/maxConcurrentOrchestratorFunctions` kvot som beskrivs ovan potentiellt hindrar andra orchestrator-funktioner från att köras.

> [!NOTE]
> De här inställningarna bör endast användas när en orchestrator-funktion har utvecklats och testats fullständigt. Standardbeteendet för aggressivt replay är användbart för att upptäcka idempotens fel i orchestrator-funktioner i tid.

## <a name="performance-targets"></a>Prestandamål

När du planerar att använda beständiga funktioner för ett produktionsprogram är det viktigt att tänka prestandakrav tidigt i processen. Det här avsnittet beskriver några grundläggande Användningsscenarier och förväntade maximalt dataflöde siffror.

* **Sekventiell aktivitet körning**: det här scenariot beskriver en orchestrator-funktion som kör en serie av aktiviteten funktioner en efter en. Den mest liknar den [funktionen länkning](durable-functions-sequence.md) exempel.
* **Parallell körning av aktiviteten**: det här scenariot beskriver en orchestrator-funktion som körs många funktioner för aktiviteten parallellt med hjälp av [Fan-out, Fan-in](durable-functions-cloud-backup.md) mönster.
* **Parallell bearbetning av certifikatsvar**: det här scenariot är den andra delen av den [Fan-out, Fan-in](durable-functions-cloud-backup.md) mönster. Den fokuserar på prestandan hos fan-in. Det är viktigt att Observera att till skillnad från fan-out, fan-in görs av en enda orchestrator funktionen instans, och därför kan endast köras på en enda virtuell dator.
* **Externa händelsebearbetning**: det här scenariot representerar en enskild orchestrator funktionen-instans som väntar på [externa händelser](durable-functions-external-events.md), en i taget.

> [!TIP]
> Till skillnad från fan-out är fan-in åtgärder begränsad till en enda virtuell dator. Om programmet använder fan-out, fan-in mönster och du är orolig fan-in prestanda, Överväg att dividera underordnad aktivitet funktionen fan-out över flera [underordnade orkestreringarna](durable-functions-sub-orchestrations.md).

I följande tabell visas den förväntade *maximala* genomströmning nummer för scenarier som beskrivits tidigare. ”Instans” refererar till en enda instans av en orchestrator-funktion som körs på en enda liten ([A1](../virtual-machines/windows/sizes-previous-gen.md#a-series)) VM i Azure App Service. I samtliga fall det förutsätts att [utökad sessioner](#orchestrator-function-replay) är aktiverade. Faktiska resultat kan variera beroende på CPU eller i/o-arbete som utförs av funktionskoden.

| Scenario | Maximalt dataflöde |
|-|-|
| Sekventiell aktivitet körning | 5 aktiviteter per sekund per instans |
| Parallell aktivitet körning (fan-out) | 100 aktiviteter per sekund per instans |
| Parallell bearbetning av certifikatsvar (fan-in) | 150 svar per sekund per instans |
| Externa händelsebearbetning | 50 händelser per sekund per instans |

> [!NOTE]
> Dessa siffror är från v1.4.0 (GA) versionen av tillägget varaktiga funktioner. Dessa värden kan ändras med tiden när funktionen utvecklas och optimeringar görs.

Om du inte ser genomströmning tal som du förväntar dig och din processor och minnesanvändning visas felfri, kontrollerar du om orsaken är relaterad till [hälsotillståndet för ditt lagringskonto](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Beständiga funktioner som tillägget kan placera betydande belastning på ett Azure Storage-konto och tillräckligt hög belastning kan resultera i begränsning storage-konto.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Installera tillägget beständiga funktioner och exempel](durable-functions-install.md)
