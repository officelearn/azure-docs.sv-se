---
title: Prestanda och skalning i varaktiga funktioner – Azure
description: Introduktion till Durable Functions-tillägget för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: 3c9227a34c1b7208210b84b5b7d64ecdc8654a83
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286388"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestanda och skalning i varaktiga funktioner (Azure Functions)

För att optimera prestanda och skalbarhet, är det viktigt att förstå de unika skalning egenskaperna för [varaktiga funktioner](durable-functions-overview.md).

Du måste förstå några av information om den underliggande providern i Azure Storage för att förstå beteendet skala.

## <a name="history-table"></a>Historiktabellen

Den **historik** tabell är en Azure Storage-tabell som innehåller historik över händelser för alla orchestration-instanser i en uppgift-hubb. Namnet på den här tabellen är i formatet *TaskHubName*historik. Eftersom instanserna körs, läggs nya rader i tabellen. Partitionsnyckeln för den här tabellen har härletts från instans-ID för dirigering. Ett instans-ID är slumpmässig i de flesta fall, vilket säkerställer optimala distribution av interna partitioner i Azure Storage.

När en orchestration-instans måste köras, laddas lämplig raderna i tabellen Historik i minnet. Dessa *Händelsehistorik* sedan återupprepas i Funktionskoden orchestrator för att få tillbaka den till dess tidigare med kontrollpunkt tillstånd. Användning av körningshistorik att återskapa tillstånd på så vis påverkas av den [mönstret för händelsekällor](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Instanser tabell

Den **instanser** tabellen är en annan Azure Storage-tabell som innehåller status för alla orchestration-instanser i en uppgift-hubb. Då instanser skapas, läggs nya rader i tabellen. Partitionsnyckeln för den här tabellen är orchestration instans-ID och Radnyckeln är en fast konstant. Det finns en rad per orchestration-instans.

Den här tabellen används för att uppfylla instans frågebegäranden från den [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET) och `getStatus` (JavaScript) API: er samt de [frågan om HTTP-API](durable-functions-http-api.md#get-instance-status). Den förblir konsekvent med innehållet i den **historik** beskrivits i tabellen. Användning av en separat Azure Storage-tabell för att effektivt uppfyller instans frågeåtgärder på så vis påverkas av den [mönster Command and Query Responsibility uppdelning (CQRS)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Intern Kölängd utlösare

Orchestrator-funktioner och Aktivitetsfunktioner utlöses både av interna köer i function-appens uppgift hub. Med hjälp av köer på så vis tillhandahåller tillförlitlig ”på minst en gång”-meddelande leveransgarantier. Det finns två typer av köer i varaktiga funktioner: den **kontroll kö** och **arbetsobjektet kö**.

### <a name="the-work-item-queue"></a>Arbetsobjektet kön

Det finns en arbetsobjektet kön varje uppgift hubben i varaktiga funktioner. Det är en grundläggande kö och fungerar på samma sätt som andra `queueTrigger` kön i Azure Functions. Den här kön används för att utlösa tillståndslösa *Aktivitetsfunktioner* av dequeueing ett enskilt meddelande i taget. Var och en av dessa meddelanden innehåller funktionen aktivitetsindata och ytterligare metadata, till exempel vilken funktion som ska köras. När ett varaktiga funktioner program skalas ut till flera virtuella datorer, dessa virtuella datorer som alla konkurrerar om att hämta arbete från arbetsobjektet kön.

### <a name="control-queues"></a>Kontrollen kö(er)

Det finns flera *styra köer* per uppgift hubben i varaktiga funktioner. En *kontroll kö* är mer sofistikerade än enklare arbetsobjektet kön. Kontrollen köer används för att utlösa tillståndskänsliga orchestrator-funktioner. Eftersom orchestrator-funktion-instanser är tillståndskänsliga singletons, går det inte att använda en konkurrerande konsument-modell för att fördela belastningen över virtuella datorer. I stället är orchestrator-meddelanden Utjämning av nätverksbelastning i kontrollen köer. Mer information om detta finns i följande avsnitt.

Kontrollen köer innehåller en mängd olika typer av orchestration livscykel meddelanden. Exempel är [orchestrator kontrollmeddelanden](durable-functions-instance-management.md), aktivitet funktionen *svar* meddelanden och meddelanden för timer. Upp till 32 meddelanden kommer att tagits bort från en kontroll kön i en avsökning. Dessa meddelanden innehåller nyttolast data samt metadata, inklusive vilken orchestration-instans som den är avsedd för. Om flera dequeued meddelanden är avsedda för samma orchestration-instans, kommer att behandlas som en batch.

### <a name="queue-polling"></a>Kö-avsökning

Tillägget varaktiga uppgift implementerar en exponentiell backoff-algoritmen för att minska effekten av idle-kön avsökning lagringskostnader för transaktionen. När ett meddelande hittas söker körningen omedelbart efter en annan message; När inget meddelande hittas, väntar en viss tidsperiod innan du försöker igen. Efter flera misslyckade försök att hämta ett kömeddelande fortsätter väntetiden att öka tills den når maximal väntetid, där standardinställningen är 30 sekunder.

Maximal avsökningen fördröjningen kan konfigureras via den `maxQueuePollingInterval` -egenskapen i den [host.json filen](../functions-host-json.md#durabletask). Du anger detta till ett högre värde kan resultera i högre svarstider för meddelandebehandling. Högre latens är förväntat förrän perioder av inaktivitet. Du anger detta till ett lägre värde kan resultera i högre kostnader för lagring på grund av ökad lagringstransaktioner.

> [!NOTE]
> När du kör i Azure Functions-förbrukning och Premium-planer i [Azure Functions skala Controller](../functions-scale.md#how-the-consumption-plan-works) ska söka varje kontroll och arbetsobjektet kö var 10: e sekund. Den här ytterligare avsökningen är nödvändigt för att avgöra om att aktivera funktionen app-instanserna och att fatta beslut om skalning. Vid tidpunkten som skrivs 10 andra intervallet är konstant och kan inte konfigureras.

## <a name="storage-account-selection"></a>Val av Storage-konto

Köer, tabeller och blobbar som används av varaktiga funktioner skapas i ett konfigurerat Azure Storage-konto. Kontot som ska användas som kan anges med hjälp av den `durableTask/azureStorageConnectionStringName` i **host.json** fil.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Om inte anges används standardvärdet `AzureWebJobsStorage` lagringskontot används. För resultatdrivna arbetsbelastningar kan dock rekommenderas konfigurera ett icke-standard storage-konto. Varaktiga funktioner använder Azure Storage kraftigt och använder ett dedikerat lagringskonto isolerar varaktiga funktioner lagringsanvändning från intern användning av Azure Functions-värden.

## <a name="orchestrator-scale-out"></a>Orchestrator-utskalning

Aktivitetsfunktioner är tillståndslösa och skalade ut automatiskt genom att lägga till virtuella datorer. Orchestrator-funktioner, å andra sidan är *partitionerade* över en eller fler köer för kontrollen. Antalet köer kontroll definieras i den **host.json** fil. I följande exempel host.json kodfragment anges den `durableTask/partitionCount` egenskap `3`.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

En uppgift-hubb kan konfigureras med mellan 1 och 16 partitioner. Om inte anges är standardvärdet för partitionsantal **4**.

Vid utskalning till flera funktionen värd-instanser (normalt på olika virtuella datorer), får varje instans ett lås på en kontroll köer. Hantera implementeras internt som blob storage-lån och se till att en orchestration-instans körs bara på en enda värd-instans i taget. Om en uppgift hubb är konfigurerad med tre kontroll köer, kan orchestration-instanser vara Utjämning av nätverksbelastning över upp till tre virtuella datorer. Ytterligare virtuella datorer kan läggas till att öka kapaciteten för körning av aktiviteten funktion.

Följande diagram illustrerar hur Azure Functions-värden interagerar med storage-entiteter i en miljö som skalats ut.

![Skala diagram](./media/durable-functions-perf-and-scale/scale-diagram.png)

I diagrammet ovan visas alla virtuella datorer tävlar om meddelanden i kön arbetsobjektet. Men bara tre virtuella datorer kan hämta meddelanden från köer med kontroll och varje virtuell dator låser en enda kontroll-kö.

Orchestration-instanser är fördelade över alla instanser av kontroll kö. Distributionen görs med hash-instans-ID för dirigering. Instans-ID som standard är slumpmässig GUID, att säkerställa att instanser fördelas jämnt över alla köer i kontrollen.

Generellt sett orchestrator-funktioner är avsedda att vara lätt och bör inte kräver mycket datorkraft. Det är därför inte nödvändigt att skapa ett stort antal kontroll kö partitioner för att få bra dataflöde. De flesta av tunga arbetet ska göras i tillståndslösa Aktivitetsfunktioner som kan skaländras ut oändligt.

## <a name="auto-scale"></a>Autoskala

Som med alla Azure Functions som körs i förbrukningsplanen varaktiga funktioner har stöd för automatisk skalning via den [Azure Functions skala controller](../functions-scale.md#runtime-scaling). Kontrollanten skala övervakar svarstiden för alla köer genom att regelbundet utfärda _peek_ kommandon. Utifrån fördröjning av peeked meddelanden avgör kontrollanten skala om du vill lägga till eller ta bort virtuella datorer.

Om kontrollanten skala avgör att kontrollen kö meddelande svarstiderna är för högt, läggs VM-instanser tills antingen meddelandet svarstiden minskas till en godtagbar nivå eller den når antalet partitioner kontroll kö. På samma sätt skala kontrollanten kontinuerligt lägger till VM-instanser om arbetsobjektet kö svarstiderna är hög, oavsett antalet partitioner.

## <a name="thread-usage"></a>Tråd-användning

Orchestrator-funktioner körs på en enda tråd så att körningen kan vara deterministisk för många repetitioner. På grund av den här Enkeltrådig körning är det viktigt att orchestrator-funktion trådar inte utför processorintensiva uppgifter, göra i/o eller blockeras av någon anledning. Allt arbete som kan kräva att i/o, som hindrar dig, eller flera trådar som ska flyttas till Aktivitetsfunktioner.

Aktivitetsfunktioner ha samma funktioner som vanliga kö-utlösta funktioner. De kan på ett säkert sätt göra i/o, köra beräkningsintensiva aktiviteter för CPU och använda flera trådar. Eftersom aktiviteten utlösare är tillståndslösa, skala de fritt ut till ett obegränsat antal virtuella datorer.

## <a name="concurrency-throttles"></a>Begränsningar för samtidighet

Azure Functions kan du köra flera funktioner samtidigt i en enda app-instans. Den här samtidig körning ökar parallellitet och minimerar antalet ”kallstarter” som en typisk app får över tid. Hög samtidighet kan dock leda hög per VM minnesanvändning. Beroende på behoven i funktionsappen, kan det vara nödvändigt att begränsa per instans samtidighet för att undvika risken att minnet i situationer med hög belastning.

Båda aktivitet funktionen och orchestrator-funktion samtidighetsgränser kan konfigureras i den **host.json** fil. De relevanta inställningarna är `durableTask/maxConcurrentActivityFunctions` och `durableTask/maxConcurrentOrchestratorFunctions` respektive.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

I exemplet ovan kan högst 10 orchestrator-funktioner och 10 Aktivitetsfunktioner köras på en enskild virtuell dator samtidigt. Om inte anges är antalet samtidiga aktiviteter och orchestrator funktionskörningar högst 10 gånger antalet kärnor på den virtuella datorn.

> [!NOTE]
> De här inställningarna är användbara för att hantera minne och CPU-användning på en enskild virtuell dator. När skalade ut över flera virtuella datorer, men har varje virtuell dator en egen uppsättning gränser. De här inställningarna kan inte användas för att styra samtidighet på global nivå.

## <a name="orchestrator-function-replay"></a>Återuppspelning av orchestrator-funktion

Som tidigare nämnts är orchestrator-funktioner återupprepas med innehållet i den **historik** tabell. Som standard spelas Funktionskoden orchestrator varje gång en grupp med meddelanden har tagits bort från en kontroll kön.

Det här beteendet för aggressiva repetitionsattacker kan inaktiveras genom att aktivera **utökade sessioner**. När utökad sessioner är aktiverade, sparas orchestrator-funktion-instanser i minnet längre och nya meddelanden kan bearbetas utan en fullständig repetitionsattacker. Utökade sessioner är aktiverade genom att ange `durableTask/extendedSessionsEnabled` till `true` i den **host.json** fil. Den `durableTask/extendedSessionIdleTimeoutInSeconds` inställningen används för att styra hur lång tid en inaktiv session hålls kvar i minnet:

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

Vanliga effekten av att aktivera utökad sessioner minskar i/o mot Azure Storage-konto och övergripande bättre datagenomflöde.

En potentiell nackdel med den här funktionen är dock den inaktiva orchestrator-funktion som instanser ska vara kvar i minnet längre. Det finns två effekter känna till:

1. Ökning i funktionen app minnesanvändning.
2. Minska övergripande i dataflöde om det finns många samtidiga, tillfälliga orchestrator funktionskörningar.

Till exempel om `durableTask/extendedSessionIdleTimeoutInSeconds` är inställd på 30 sekunder och sedan avsnittet en tillfällig orchestrator-funktion som körs i mindre än 1 sekund tar fortfarande upp minne i 30 sekunder. Den också räknas mot den `durableTask/maxConcurrentOrchestratorFunctions` kvot som beskrivs ovan potentiellt förhindrar andra orchestrator-funktioner från att köras.

> [!NOTE]
> De här inställningarna bör endast användas när en orchestrator-funktion har fullständigt utvecklas och testas. Standardbeteendet för aggressiva repetitionsattacker är användbart för att upptäcka idempotens fel i orchestrator-funktioner under utvecklingsfasen.

## <a name="performance-targets"></a>Prestandamål

Det är viktigt att tänka på prestandakraven tidigt i planeringsprocessen när du planerar att använda varaktiga funktioner för ett produktionsprogram. Det här avsnittet beskrivs vissa grundläggande Användningsscenarier och den förväntade maximala dataflödet siffror.

* **Sekventiell aktivitetskörning**: Det här scenariot beskriver en orchestrator-funktion som kör en serie Aktivitetsfunktioner en efter en. Den mest liknar den [funktionen länkning](durable-functions-sequence.md) exemplet.
* **Parallell aktivitetskörning**: Det här scenariot beskriver en orchestrator-funktion som körs många Aktivitetsfunktioner parallellt med den [Fan-out, Fan-in](durable-functions-cloud-backup.md) mönster.
* **Parallell bearbetning av certifikatsvar**: Det här scenariot är den andra halvan av den [Fan-out, Fan-in](durable-functions-cloud-backup.md) mönster. Den fokuserar på prestanda hos bläddra in. Det är viktigt att Observera att till skillnad från bläddra ut, bläddra in gör du genom en enda orchestrator-funktion-instans, och därför kan bara köras på en enskild virtuell dator.
* **Externa händelsebearbetning**: Det här scenariot representerar en enskild orchestrator-funktion-instans som väntar på [externa händelser](durable-functions-external-events.md), en i taget.

> [!TIP]
> Till skillnad från bläddra ut-åtgärder, så är bläddra in-åtgärder begränsade till en enda virtuell dator. Om programmet använder fan-out, fan-in mönster och du är orolig fan-in prestanda, Överväg att dividera icke aktivitet funktionen fan-out över flera [underordnade orkestreringar](durable-functions-sub-orchestrations.md).

I följande tabell visas den förväntade *maximala* dataflöde-nummer för de tidigare beskrivna scenarierna. ”Instans” refererar till en enda instans av en orchestrator-funktion som körs på en enda liten ([A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) virtuell dator i Azure App Service. I samtliga fall det förutsätts att [utökade sessioner](#orchestrator-function-replay) är aktiverade. De faktiska resultaten kan variera beroende på CPU eller i/o-arbetet som utförs av funktionskoden.

| Scenario | Maximalt dataflöde |
|-|-|
| Sekventiell aktivitetskörning | 5 aktiviteter per sekund per instans |
| Parallell aktivitetskörning (fan-out) | 100 aktiviteter per sekund per instans |
| Parallell bearbetning av certifikatsvar (fan-in) | 150 svar per sekund per instans |
| Externa händelsebearbetning | 50 händelser per sekund per instans |

> [!NOTE]
> Dessa siffror är från v1.4.0 (GA)-versionen av tillägget varaktiga funktioner. Dessa siffror kan ändras över tid när funktionen utvecklas och optimeringar görs.

Om du inte ser dataflöde siffrorna du förväntar dig och din CPU och minnesanvändning visas felfritt, kontrollerar du om orsaken är relaterat till [hälsotillståndet för ditt lagringskonto](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Varaktiga funktioner som tillägg kan placera betydande belastningen på ett Azure Storage-konto och tillräckligt hög belastning kan leda till begränsning av storage-konto.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa din första beständiga funktion i C#](durable-functions-create-first-csharp.md)
