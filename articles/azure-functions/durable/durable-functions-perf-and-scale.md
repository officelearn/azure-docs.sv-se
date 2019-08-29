---
title: Prestanda och skalning i Durable Functions – Azure
description: Introduktion till Durable Functions-tillägget för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: ed0fe22903412d4164fb3a85dbd9afafdc7023e6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098005"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestanda och skalning i Durable Functions (Azure Functions)

För att optimera prestanda och skalbarhet är det viktigt att förstå de unika skalnings egenskaperna för [Durable Functions](durable-functions-overview.md).

För att förstå skalnings beteendet måste du förstå lite information om underliggande Azure Storage-providern.

## <a name="history-table"></a>Historik tabell

**Historik** tabellen är en Azure Storage tabell som innehåller historik händelser för alla Dirigerings instanser i en aktivitets hubb. Namnet på den här tabellen är i formatet *TaskHubName*historik. När instanser körs läggs nya rader till i den här tabellen. Partitionsnyckel för den här tabellen härleds från instans-ID: t för dirigeringen. Ett instans-ID är slumpmässigt i de flesta fall, vilket säkerställer optimal distribution av interna partitioner i Azure Storage.

När en Dirigerings instans måste köras läses rätt rader i historik tabellen in i minnet. Dessa *Historik händelser* spelas sedan upp i Orchestrator-funktions koden för att komma tillbaka till sitt tidigare tillstånd. Användningen av körnings historiken för att återskapa tillstånd på det här sättet påverkas av [mönstret för händelse källor](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Instans tabell

**Instans** tabellen är en annan Azure Storage tabell som innehåller status för alla Dirigerings instanser i en aktivitets hubb. När instanser skapas läggs nya rader till i den här tabellen. Den här tabellens partitionsnyckel är Orchestration instance-ID: t och rad nyckeln är en fast konstant. Det finns en rad per Orchestration-instans.

Den här tabellen används för att uppfylla instans förfrågningar från [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.net) och `getStatus` Java Script-API: er samt [status frågans http-API](durable-functions-http-api.md#get-instance-status). Det hålls i enlighet med innehållet i **Historik** tabellen ovan. Användningen av en separat Azure Storage tabell för att effektivt uppfylla instanser av instans frågor på det här sättet påverkas av [mönstret uppdelning av kommando-och frågeåtgärder (CQRS)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Interna köade utlösare

Funktioner och aktivitets funktioner i Orchestrator utlöses båda av interna köer i Function-programmets aktivitets hubb. Med hjälp av köer på det här sättet får du en tillförlitlig garanti för meddelande leverans på minst en gång. Det finns två typer av köer i Durable Functions: **kontroll kön** och **arbets objekt kön**.

### <a name="the-work-item-queue"></a>Arbets objekts kön

Det finns en kö för arbets objekt per aktivitets hubb i Durable Functions. Det är en grundläggande kö och fungerar på samma sätt som andra `queueTrigger` köer i Azure Functions. Den här kön används för att utlösa tillstånds lösa *aktivitets funktioner* genom att köa ett enskilt meddelande i taget. Vart och ett av dessa meddelanden innehåller indata för aktivitets funktion och ytterligare metadata, till exempel vilken funktion som ska köras. När ett Durable Functions-program skalar ut till flera virtuella datorer, konkurrerar de virtuella datorerna med att förvärva arbete från arbets objekt kön.

### <a name="control-queues"></a>Kontroll köer

Det finns flera *kontroll köer* per aktivitets nav i Durable functions. En *kontroll kö* är mer avancerad än den enklare arbets objekts kön. Kontroll köer används för att utlösa tillstånds känsliga Orchestrator-funktioner. Eftersom Orchestrator Function-instanserna är tillstånds känsliga singleton är det inte möjligt att använda en konkurrerande konsument modell för att distribuera belastningen mellan virtuella datorer. I stället är Orchestrator-meddelanden belastningsutjämnade i kontroll köerna. Mer information om det här problemet finns i följande avsnitt.

Kontroll köer innehåller olika typer av typer av Dirigerings-livs cykel meddelanden. Exempel på detta är [Orchestrator-kontrollmeddelanden](durable-functions-instance-management.md), meddelanden om aktivitets funktions *svar* och timer-meddelanden. Så många som 32 meddelanden tas i kö från en kontroll kö i en enda omröstning. Dessa meddelanden innehåller nytto Last data samt metadata, inklusive vilken Orchestration-instans den är avsedd för. Om flera meddelanden i kön är avsedda för samma Dirigerings instans kommer de att bearbetas som en batch.

### <a name="queue-polling"></a>Köa avsökning

Det varaktiga aktivitets tillägget implementerar en slumpmässig, exponentiell algoritm för att minska inaktive rad avsökningar på lagrings transaktions kostnader. När ett meddelande hittas söker körningen omedelbart efter ett annat meddelande. När inget meddelande hittas väntar den en stund innan den försöker igen. Efter efterföljande misslyckade försök att hämta ett Queue-meddelande fortsätter vänte tiden att öka tills den når den maximala vänte tiden, vilket är 30 sekunder.

Den maximala avsöknings fördröjningen kan konfigureras `maxQueuePollingInterval` via egenskapen i [Host. JSON-filen](../functions-host-json.md#durabletask). Om du anger ett högre värde kan det leda till högre meddelande fördröjning. Högre fördröjningar förväntas bara efter perioder av inaktivitet. Om du anger ett lägre värde kan högre lagrings kostnader uppstå på grund av ökade lagrings transaktioner.

> [!NOTE]
> När du kör i Azure Functions förbruknings-och Premium-planerna, avsöker [Azure Functions skalnings styrenheten](../functions-scale.md#how-the-consumption-and-premium-plans-work) varje kontroll och arbets objekts kön var tionde sekund. Denna ytterligare avsökning är nödvändig för att avgöra när du ska aktivera Function App-instanser och fatta skalnings beslut. Vid tidpunkten för skrivning är det här 10 sekunder konstant och kan inte konfigureras.

## <a name="storage-account-selection"></a>Val av lagrings konto

Köer, tabeller och blobbar som används av Durable Functions skapas i ett konfigurerat Azure Storage konto. Kontot som ska användas kan anges med `durableTask/azureStorageConnectionStringName` inställningen i **Host. JSON** -filen.

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

Om inget värde anges används standard `AzureWebJobsStorage` lagrings kontot. För prestanda känsliga arbets belastningar rekommenderas dock inte att konfigurera ett lagrings konto som inte är standard. Durable Functions använder Azure Storage kraftigt och använder ett dedikerat lagrings konto för att isolera Durable Functions lagrings användningen från den interna användningen av Azure Functions-värden.

## <a name="orchestrator-scale-out"></a>Skalbarhet för Orchestrator

Aktivitets funktionerna är tillstånds lösa och skalas ut automatiskt genom att lägga till virtuella datorer. Orchestrator-funktioner, å andra sidan, *partitioneras* i en eller flera kontroll köer. Antalet kontroll köer definieras i **Host. JSON** -filen. Följande exempel på Host. JSON-kodfragment anger `durableTask/partitionCount` egenskapen till `3`.

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

En aktivitets hubb kan konfigureras med mellan 1 och 16 partitioner. Om inget värde anges är standardpartitions antalet **4**.

Vid skalning till flera funktions värd instanser (vanligt vis på olika virtuella datorer) får varje instans ett lås i en av kontroll köerna. Dessa lås implementeras internt som Blob Storage-lån och säkerställer att en Dirigerings instans bara körs på en enda värd instans i taget. Om en aktivitets hubb har kon figurer ATS med tre kontroll köer kan Dirigerings instanser vara belastningsutjämnade över upp till tre virtuella datorer. Ytterligare virtuella datorer kan läggas till för att öka kapaciteten för körning av aktivitets funktionen.

Följande diagram illustrerar hur Azure Functions-värden interagerar med lagrings enheterna i en skalad miljö.

![Skala diagram](./media/durable-functions-perf-and-scale/scale-diagram.png)

Som du ser i föregående diagram konkurrerar alla virtuella datorer om meddelanden i arbets objekt kön. Men endast tre virtuella datorer kan hämta meddelanden från kontroll köer och varje virtuell dator låser en enskild kontroll kö.

Dirigerings instanserna distribueras över alla instanser av kontroll kön. Fördelningen görs genom hashing av instans-ID: t för dirigeringen. Instans-ID: n är som standard slumpmässiga GUID, vilket säkerställer att instanserna är jämnt fördelade över alla kontroll köer.

I allmänhet är Orchestrator-funktioner avsedda att vara lätta att vara lätta och bör inte kräva stora mängder data behandlings kraft. Det är därför inte nödvändigt att skapa ett stort antal Control-diskpartitioner för att få bra data flöde. Det mesta av det tunga arbetet bör utföras i tillstånds lösa aktivitets funktioner som kan skalas ut oändligt.

## <a name="auto-scale"></a>Autoskala

Precis som med alla Azure Functions som körs i förbruknings planen stöder Durable Functions automatisk skalning via [Azure Functions skalnings styrenheten](../functions-scale.md#runtime-scaling). Skalnings kontrollen övervakar svars tiden för alla köer genom att regelbundet skicka gransknings kommandon. Baserat på fördröjningen hos de granskade meddelandena bestämmer skalnings styrenheten om du vill lägga till eller ta bort virtuella datorer.

Om skalnings styrenheten bestämmer att meddelande fördröjningarna för kontroll kön är för höga, lägger den till virtuella dator instanser tills svars tiden minskas till en acceptabel nivå eller når gränsen för kontroll köns partition. På samma sätt kommer skalnings styrenheten att kontinuerligt lägga till virtuella dator instanser om svars tiden för arbets objekt i kö är hög, oavsett antalet partitioner.

## <a name="thread-usage"></a>Tråd användning

Orchestrator-funktioner körs på en enda tråd för att säkerställa att körningen kan vara deterministisk över flera uppspelningar. På grund av den här enkla körningen är det viktigt att Orchestrator Function-trådar inte utför processor intensiva uppgifter, gör I/O eller blockera av någon anledning. Allt arbete som kan kräva I/O, blockera eller flera trådar bör flyttas till aktivitets funktioner.

Aktivitets funktioner har samma beteende som regelbundna köade funktioner. De kan på ett säkert sätt göra I/O, köra processor intensiva åtgärder och använda flera trådar. Eftersom aktivitets utlösare är tillstånds lösa kan de fritt skalas ut till ett obegränsat antal virtuella datorer.

## <a name="concurrency-throttles"></a>Samtidighets begränsningar

Azure Functions stöder körning av flera funktioner samtidigt inom en enda App-instans. Den här samtidiga körningen bidrar till att öka parallellitet och minimerar antalet "kall starter" som en typisk app kommer att uppleva över tid. Hög samtidighet kan dock leda till hög minnes användning per virtuell dator. Beroende på behoven i Function-appen kan det vara nödvändigt att reglera per-instans-samtidigheten för att undvika möjligheten att få slut på minne i stora inläsnings situationer.

Både aktivitets funktionen och Orchestrator-funktionens samtidighets gränser kan konfigureras i **Host. JSON** -filen. De relevanta inställningarna är `durableTask/maxConcurrentActivityFunctions` `durableTask/maxConcurrentOrchestratorFunctions` respektive.

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

I föregående exempel kan högst 10 funktioner för Orchestrator och 10 aktiviteter köras på en enskild virtuell dator samtidigt. Om detta inte anges, är antalet körningar av samtidiga aktiviteter och Orchestrator-funktionen ett tak på 10X antalet kärnor på den virtuella datorn.

> [!NOTE]
> De här inställningarna är användbara för att hantera minnes-och CPU-användning på en enskild virtuell dator. Men när de skalas ut över flera virtuella datorer har varje virtuell dator en egen uppsättning gränser. De här inställningarna kan inte användas för att kontrol lera samtidighet på global nivå.

## <a name="orchestrator-function-replay"></a>Uppspelning av Orchestrator-funktion

Som tidigare nämnts spelas Orchestrator-funktionerna om med hjälp av innehållet i **Historik** tabellen. Som standard spelas Orchestrator-funktions koden upp varje gång en grupp meddelanden tas ur kö från en kontroll kö.

Det här aggressiva omuppspelnings beteendet kan inaktive ras genom att aktivera **utökade sessioner**. När utökade sessioner är aktiverade hålls Orchestrator Function-instanser i minnet längre och nya meddelanden kan bearbetas utan en fullständig omuppspelning. Utökade sessioner aktive ras genom `durableTask/extendedSessionsEnabled` att `true` ställas in på i **Host. JSON** -filen. `durableTask/extendedSessionIdleTimeoutInSeconds` Inställningen används för att styra hur länge en inaktiv session ska lagras i minnet:

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

Den normala påverkan av att aktivera utökade sessioner minskar I/O mot Azure Storage kontot och det övergripande data flödet.

En potentiell nack av den här funktionen är dock att inaktiva Orchestrator Function-instanser kommer att stanna kvar i minnet längre. Det finns två effekter att vara medveten om:

1. Total ökning av minnes användningen för Function-appar.
2. Den totala minskningen i data flödet om det finns många samtidiga körningar av Orchestrator-funktionen i ett kort levde.

Exempel: om `durableTask/extendedSessionIdleTimeoutInSeconds` är inställt på 30 sekunder, kommer en tjänst för en snabb och en Orchestrator-funktion som körs på mindre än en sekund fortfarande att uppta minne i 30 sekunder. Det kommer också att räknas mot `durableTask/maxConcurrentOrchestratorFunctions` den kvot som anges ovan, vilket kan leda till att andra Orchestrator-funktioner inte körs.

> [!NOTE]
> De här inställningarna bör endast användas när en Orchestrator-funktion har utvecklats helt och testats. Standard beteendet för aggressiva omuppspelning är användbart för att identifiera idempotens-fel i Orchestrator-funktioner vid utvecklings tiden.

## <a name="performance-targets"></a>Prestanda mål

När du planerar att använda Durable Functions för ett produktions program är det viktigt att beakta prestanda kraven tidigt i planerings processen. I det här avsnittet beskrivs några grundläggande användnings scenarier och förväntade högsta data flödes nummer.

* **Körning av sekventiell aktivitet**: I det här scenariot beskrivs en Orchestrator-funktion som kör en serie aktivitets funktioner en efter den andra. Det liknar närmast [funktionen kedje](durable-functions-sequence.md) exempel.
* **Körning av parallell aktivitet**: I det här scenariot beskrivs en Orchestrator-funktion som kör många aktivitets funktioner parallellt med [fläkt-och fläkt](durable-functions-cloud-backup.md) mönster.
* **Bearbetning av parallella svar**: Det här scenariot är den andra halvan [](durable-functions-cloud-backup.md) av det nya mönstret för fläktning. Den fokuserar på prestandan hos fläkten. Det är viktigt att tänka på att till skillnad från fläkt, som görs av en enda Orchestrator Function-instans och därför bara kan köras på en enda virtuell dator.
* **Extern händelse bearbetning**: Det här scenariot representerar en enda Orchestrator Function-instans som väntar på [externa händelser](durable-functions-external-events.md), en i taget.

> [!TIP]
> Till skillnad från bläddra ut-åtgärder, så är bläddra in-åtgärder begränsade till en enda virtuell dator. Om ditt program använder fläkten, fläkten och du är bekymrad om fläkt prestanda bör du överväga att dela upp aktivitetens fläkt över flera underordningar. [](durable-functions-sub-orchestrations.md)

I följande tabell visas de förväntade *högsta* data flödes numren för de scenarier som beskrivs ovan. "Instance" syftar på en enda instans av en Orchestrator-funktion som körs på en enskild liten ([a1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) virtuell dator i Azure App Service. I samtliga fall förutsätts att [utökade sessioner](#orchestrator-function-replay) är aktiverade. De faktiska resultaten kan variera beroende på processor-eller I/O-arbete som utförs av funktions koden.

| Scenario | Maximalt dataflöde |
|-|-|
| Körning av sekventiell aktivitet | 5 aktiviteter per sekund, per instans |
| Körning av parallell aktivitet (fläkt ut) | 100 aktiviteter per sekund, per instans |
| Bearbetning av parallella svar (fläkt-in) | 150 svar per sekund, per instans |
| Extern händelse bearbetning | 50 händelser per sekund, per instans |

> [!NOTE]
> De här talen är aktuella från och med v 1.4.0-versionen (GA) av Durable Functions-tillägget. De här talen kan ändras med tiden när funktionen är vuxen och som optimeringar.

Om du inte ser de data flödes nummer du förväntar dig och din CPU-och minnes användning verkar vara felfri, kontrollerar du om orsaken är relaterad till [ditt lagrings kontos hälso tillstånd](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Durable Functions-tillägget kan leda till betydande belastning på ett Azure Storage konto och tillräckligt stora belastningar kan resultera i begränsning av lagrings kontot.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa din första beständiga funktion i C#](durable-functions-create-first-csharp.md)
