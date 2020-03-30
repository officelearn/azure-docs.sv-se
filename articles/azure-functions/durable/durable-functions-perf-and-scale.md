---
title: Prestanda och skalning i varaktiga funktioner - Azure
description: Introduktion till Durable Functions-tillägget för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 260811c4ae15b45de6f7bc1b22e3ed6dcea44259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277913"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestanda och skalning i Durable Functions (Azure Functions)

För att optimera prestanda och skalbarhet är det viktigt att förstå de unika skalningsegenskaperna [hos varaktiga funktioner](durable-functions-overview.md).

För att förstå skalningsbeteendet måste du förstå några av detaljerna i den underliggande Azure Storage-providern.

## <a name="history-table"></a>Tabell över historik

Tabellen **Historik** är en Azure Storage-tabell som innehåller historikhändelser för alla orchestration-instanser i en aktivitetsnav. Namnet på den här tabellen finns i formuläret *TaskHubName*History. När instanser körs läggs nya rader till i den här tabellen. Partitionsnyckeln för den här tabellen härleds från instans-ID för orkestrering. Ett instans-ID är slumpmässigt i de flesta fall, vilket säkerställer optimal distribution av interna partitioner i Azure Storage.

När en orkestreringsinstans måste köras läses lämpliga rader i tabellen Historik in i minnet. Dessa *historikhändelser* spelas sedan upp i orchestrator-funktionskoden för att få tillbaka den till sitt tidigare kontrollpunktstillstånd. Användningen av körningshistorik för att återskapa tillståndet på detta sätt påverkas av [mönstret Event Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabell över instanser

**Instanstabellen** är en annan Azure Storage-tabell som innehåller status för alla orchestration- och entitetsinstanser i en aktivitetsnav. När instanser skapas läggs nya rader till i den här tabellen. Partitionsnyckeln för den här tabellen är orchestration instance ID eller entitetsnyckel och radnyckeln är en fast konstant. Det finns en rad per orkestrering eller entitetsinstans.

Den här tabellen används för att `GetStatusAsync` uppfylla instansfrågebegäranden från API:erna (.NET) och `getStatus` (JavaScript) samt [statusfrågan HTTP API](durable-functions-http-api.md#get-instance-status). Det hålls så småningom överensstämmer med innehållet i **tabellen Historia** som nämnts tidigare. Användningen av en separat Azure Storage-tabell för att effektivt uppfylla instansfrågaåtgärder på det här sättet påverkas av [mönstret Kommando- och frågeansvarssaegregering (CQRS).](https://docs.microsoft.com/azure/architecture/patterns/cqrs)

## <a name="internal-queue-triggers"></a>Interna köutlösare

Orchestrator-funktioner och aktivitetsfunktioner utlöses båda av interna köer i funktionsappens aktivitetsnav. Att använda köer på detta sätt ger tillförlitliga "minst en gång" meddelandeleveransgarantier. Det finns två typer av köer i Varaktiga funktioner: **kontrollkön** och **arbetsobjektkön**.

### <a name="the-work-item-queue"></a>Kön för arbetsobjekt

Det finns en arbetsobjektkö per aktivitetsnav i Varaktiga funktioner. Det är en grundläggande kö och fungerar `queueTrigger` på samma sätt som alla andra köer i Azure Functions. Den här kön används för att utlösa tillståndslösa *aktivitetsfunktioner* genom att avköera ett enskilt meddelande åt gången. Vart och ett av dessa meddelanden innehåller aktivitetsfunktionsindata och ytterligare metadata, till exempel vilken funktion som ska köras. När ett program med varaktiga funktioner skalas ut till flera virtuella datorer konkurrerar dessa virtuella datorer om att få arbete från arbetsobjektkön.

### <a name="control-queues"></a>Kontrollera köer

Det finns flera *kontrollköer* per aktivitetsnav i Varaktiga funktioner. En *kontrollkö* är mer sofistikerad än den enklare arbetsobjektskön. Kontrollköer används för att utlösa tillståndskänsliga orchestrator- och entitetsfunktioner. Eftersom orchestrator- och entitetsfunktionsinstanserna är tillståndskänsliga singletons är det inte möjligt att använda en konkurrerande konsumentmodell för att distribuera inläsning över virtuella datorer. Orchestrator- och entitetsmeddelanden är i stället belastningsbalanserade över kontrollköerna. Mer information om detta finns i efterföljande avsnitt.

Kontrollköer innehåller en mängd olika typer av orchestration lifecycle-meddelanden. Exempel på detta är [orchestrator-kontrollmeddelanden,](durable-functions-instance-management.md) *svarsmeddelanden för* aktivitetsfunktioner och timermeddelanden. Så många som 32 meddelanden kommer att uteslutas från en kontrollkö i en enda omröstning. Dessa meddelanden innehåller nyttolastdata samt metadata, inklusive vilken orkestreringsinstans den är avsedd för. Om flera avföljda meddelanden är avsedda för samma orkestreringsinstans bearbetas de som en batch.

### <a name="queue-polling"></a>Avsökning av kö

Det varaktiga aktivitetstillägget implementerar en slumpmässig exponentiell back-off-algoritm för att minska effekten av inaktiv kö avsökning på lagringstransaktionskostnader. När ett meddelande hittas söker körningen omedelbart efter ett annat meddelande. När inget meddelande hittas väntar det en viss tid innan du försöker igen. Efter efterföljande misslyckade försök att få ett kömeddelande fortsätter väntetiden att öka tills den når den maximala väntetiden, som standard till 30 sekunder.

Den maximala avsökningsfördröjningen kan konfigureras via egenskapen `maxQueuePollingInterval` i filen [host.json](../functions-host-json.md#durabletask). Om du anger den här egenskapen till ett högre värde kan det leda till högre meddelandebearbetningsdämpning. Högre svarstider förväntas först efter perioder av inaktivitet. Om du ställer in den här egenskapen på ett lägre värde kan det leda till högre lagringskostnader på grund av ökade lagringstransaktioner.

> [!NOTE]
> När du kör i Azure Functions Consumption and Premium-abonnemangen kommer [Azure Functions Scale Controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) att avsöka varje kontroll- och arbetsobjektkö en gång var tionde sekund. Den här ytterligare avsökningen är nödvändig för att avgöra när funktionsappinstanserna ska aktiveras och för att fatta skalningsbeslut. I skrivande stund är det här 10-sekundersintervallet konstant och kan inte konfigureras.

## <a name="storage-account-selection"></a>Val av lagringskonto

De köer, tabeller och blobbar som används av varaktiga funktioner skapas i ett konfigurerat Azure Storage-konto. Kontot som ska användas kan `durableTask/storageProvider/connectionStringName` anges med `durableTask/azureStorageConnectionStringName` inställningen (eller inställningen i Varaktiga funktioner 1.x) i **filen host.json.**

### <a name="durable-functions-2x"></a>Hållbara funktioner 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Hållbara funktioner 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Om inget anges används `AzureWebJobsStorage` standardlagringskontot. För prestandakänsliga arbetsbelastningar rekommenderas dock att konfigurera ett lagringskonto som inte är standard. Varaktiga funktioner använder Azure Storage kraftigt och med hjälp av ett dedikerat lagringskonto isolerar varaktiga funktioner lagringsanvändning från den interna användningen av Azure Functions-värden.

## <a name="orchestrator-scale-out"></a>Orchestrator skala ut

Aktivitetsfunktionerna är tillståndslösa och skalas ut automatiskt genom att lägga till virtuella datorer. Orchestrator-funktioner och entiteter, å andra sidan, *är partitionerade* över en eller flera kontrollköer. Antalet kontrollköer definieras i **filen host.json.** I följande exempel som host.json-kodavsnitt anger egenskapen `durableTask/storageProvider/partitionCount` (eller `durableTask/partitionCount` `3`i Varaktiga funktioner 1.x) till .

### <a name="durable-functions-2x"></a>Hållbara funktioner 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Hållbara funktioner 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

En aktivitetsnav kan konfigureras med mellan 1 och 16 partitioner. Om inget anges är standardantalet för partition **4**.

När du skalar ut till flera funktionsvärdinstanser (vanligtvis på olika virtuella datorer) får varje instans ett lås på en av kontrollköerna. Dessa lås implementeras internt som blob storage-lån och säkerställer att en orkestreringsinstans eller entitet endast körs på en enda värdinstans åt gången. Om en aktivitetsnav har konfigurerats med tre kontrollköer kan orchestration-instanser och entiteter belastningsbalanseras över upp till tre virtuella datorer. Ytterligare virtuella datorer kan läggas till för att öka kapaciteten för körning av aktivitetsfunktionen.

Följande diagram illustrerar hur Azure Functions-värden interagerar med lagringsenheterna i en utskalad miljö.

![Skala diagram](./media/durable-functions-perf-and-scale/scale-diagram.png)

Som visas i föregående diagram konkurrerar alla virtuella datorer om meddelanden i arbetsobjektskön. Endast tre virtuella datorer kan dock hämta meddelanden från kontrollköer och varje virtuell dator låser en enda kontrollkö.

Orchestration instanser och entiteter distribueras över alla kontrollkö instanser. Distributionen görs genom att instans-ID:t för orkestrering eller entitetsnamn och nyckelpar ska utföras. Orchestration-instans-ID:er som standard är slumpmässiga GUID:er, vilket säkerställer att instanserna är jämnt fördelade över alla kontrollköer.

Generellt sett är orchestrator funktioner avsedda att vara lätta och bör inte kräva stora mängder datorkraft. Det är därför inte nödvändigt att skapa ett stort antal kontrollköpartitioner för att få bra dataflöde för orkestreringar. Det mesta av det tunga arbetet bör utföras i statslösa aktivitetsfunktioner, som kan skalas ut oändligt.

## <a name="auto-scale"></a>Automatisk skala

Precis som med alla Azure-funktioner som körs i förbruknings- och elastiskpremi-abonnemangen stöder varaktiga funktioner automatisk skalning via [Azure Functions-skalningsstyrenheten](../functions-scale.md#runtime-scaling). Skalkontrollanten övervakar svarstiden för alla köer _peek_ genom att regelbundet utfärda peek-kommandon. Baserat på svarstider för de intittade meddelandena bestämmer skalkontrollanten om den ska lägga till eller ta bort virtuella datorer.

Om scale controller bestämmer att kontrollkön meddelande latenser är för hög, kommer det att lägga VM-instanser tills antingen meddelandet svarstiden minskar till en acceptabel nivå eller den når kontrollkö partitionen räknas. På samma sätt lägger skalningsstyrenheten kontinuerligt till VM-instanser om svarstider för arbetsobjektköer är höga, oavsett antalet partitioner.

> [!NOTE]
> Från och med Varaktiga funktioner 2.0 kan funktionsappar konfigureras för att köras inom VNET-skyddade tjänstslutpunkter i Elastic Premium-planen. I den här konfigurationen initierar de varaktiga funktionerna skalningsbegäranden i stället för skalningsstyrenheten.

## <a name="thread-usage"></a>Användning av tråd

Orchestrator-funktioner körs på en enda tråd för att säkerställa att körningen kan vara deterministisk över många repriser. På grund av den här entrådiga körningen är det viktigt att orchestrator-funktionstrådar inte utför PROCESSOR-intensiva uppgifter, gör I/O eller blockerar av någon anledning. Allt arbete som kan kräva I/O, blockering eller flera trådar bör flyttas till aktivitetsfunktioner.

Aktivitetsfunktioner har alla samma beteenden som vanliga köutlösta funktioner. De kan säkert göra I/O, utföra CPU-intensiva åtgärder och använda flera trådar. Eftersom aktivitetsutlösare är tillståndslösa kan de fritt skala ut till ett obegränsat antal virtuella datorer.

Entitetsfunktioner körs också på en enda tråd och åtgärder bearbetas en i taget. Entitetsfunktioner har dock inga begränsningar för vilken typ av kod som kan köras.

## <a name="concurrency-throttles"></a>Begränsningar för samtidighet

Azure Functions stöder körning av flera funktioner samtidigt i en enda appinstans. Detta samtidiga utförande hjälper till att öka parallellismen och minimerar antalet "kalla starter" som en typisk app kommer att uppleva över tiden. Hög samtidighet kan dock avgasa systemresurser per virtuell dator, sådana nätverksanslutningar eller tillgängligt minne. Beroende på funktionsappens behov kan det vara nödvändigt att begränsa samtidigheten per instans för att undvika risken för att minnet får i situationer med hög belastning.

Aktivitets-, orchestrator- och entitetsfunktionskonsensens gränser kan konfigureras i **filen host.json.** De relevanta `durableTask/maxConcurrentActivityFunctions` inställningarna gäller `durableTask/maxConcurrentOrchestratorFunctions` för aktivitetsfunktioner och för både orchestrator- och entitetsfunktioner.

### <a name="functions-20"></a>Funktioner 2.0

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

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

I föregående exempel kan högst 10 orchestrator- eller entitetsfunktioner och 10 aktivitetsfunktioner köras på en enda virtuell dator samtidigt. Om inget anges begränsas antalet samtidiga aktivitets- och orchestrator- eller entitetsfunktionskörningar till 10X antalet kärnor på den virtuella datorn.

> [!NOTE]
> Dessa inställningar är användbara för att hantera minne och CPU-användning på en enda virtuell dator. Men när skalas ut över flera virtuella datorer har varje virtuell dator sin egen uppsättning gränser. Dessa inställningar kan inte användas för att styra samtidighet på global nivå.

## <a name="extended-sessions"></a>Utökade sessioner

Utökade sessioner är en inställning som håller orkestreringar och entiteter i minnet även efter att de har bearbetat meddelanden. Den typiska effekten av att aktivera utökade sessioner är minskad I/O mot Azure Storage-kontot och övergripande förbättrat dataflöde.

Du kan aktivera utökade `durableTask/extendedSessionsEnabled` `true` sessioner genom att ange i **filen host.json.** Inställningen `durableTask/extendedSessionIdleTimeoutInSeconds` kan användas för att styra hur länge en inaktiv session kommer att hållas i minnet:

**Funktioner 2.0**
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

**Funktioner 1,0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Det finns två potentiella nackdelar med den här inställningen att vara medveten om:

1. Det finns en övergripande ökning av funktion app minnesanvändning.
2. Det kan finnas en total minskning av dataflödet om det finns många samtidiga, kortlivade orchestrator eller entitet funktionskörningar.

Om du `durableTask/extendedSessionIdleTimeoutInSeconds` till exempel är inställd på 30 sekunder upptar ett kortlivade orchestrator- eller entitetsfunktionsavsnitt som körs på mindre än 1 sekund fortfarande minne i 30 sekunder. Den räknas också `durableTask/maxConcurrentOrchestratorFunctions` mot den kvot som nämnts tidigare, vilket kan förhindra att andra orchestrator- eller entitetsfunktioner körs.

De specifika effekterna av utökade sessioner på orchestrator- och entitetsfunktioner beskrivs i nästa avsnitt.

### <a name="orchestrator-function-replay"></a>Spelas upp om orchestrator-funktionen

Som tidigare nämnts spelas orchestrator-funktionerna upp med hjälp av innehållet i tabellen **Historik.** Som standard spelas orchestrator-funktionskoden upp varje gång en batch med meddelanden ska tas bort från en kontrollkö. Även om du använder fan-out, fan-in mönster och väntar på alla uppgifter `Task.WhenAll` att slutföra `context.df.Task.all` (till exempel med hjälp av .NET eller i JavaScript), kommer det att finnas repriser som inträffar som grupper av uppgiftssvar bearbetas över tiden. När utökade sessioner är aktiverade lagras orchestrator-funktionsinstanser i minnet längre och nya meddelanden kan bearbetas utan en fullständig historikuppspelning.

Prestandaförbättringen av förlängda sessioner observeras oftast i följande situationer:

* När det finns ett begränsat antal orchestration-instanser som körs samtidigt.
* När orkestreringar har ett stort antal sekventiella åtgärder (t.ex. hundratals aktivitetsfunktionsanrop) som slutförs snabbt.
* När orkestreringar fan-out och fan-in ett stort antal åtgärder som slutförs ungefär samtidigt.
* När orchestrator funktioner behöver bearbeta stora meddelanden eller göra någon CPU-intensiv databearbetning.

I alla andra situationer finns det vanligtvis ingen observerbar prestandaförbättring för orchestrator-funktioner.

> [!NOTE]
> Dessa inställningar bör endast användas efter att en orchestrator-funktion har utvecklats och testats fullt ut. Standardbeteendet för aggressiv repris kan vara användbart för att identifiera [orchestrator-funktionsbegränsningar](durable-functions-code-constraints.md) vid utvecklingstid och är därför inaktiverat som standard.

### <a name="entity-function-unloading"></a>Avlastning av entitetsfunktion

Entitetsfunktioner bearbetar upp till 20 operationer i en enda batch. Så snart en entitet har bearbetat en batch med åtgärder kvarstår den sitt tillstånd och tas bort från minnet. Du kan fördröja avlastningen av entiteter från minnet med hjälp av inställningen för utökade sessioner. Entiteter fortsätter att bevara sina tillståndsändringar som tidigare, men finns kvar i minnet under den konfigurerade tidsperioden för att minska antalet laster från Azure Storage. Den här minskningen av belastningar från Azure Storage kan förbättra det övergripande dataflödet för enheter som används ofta.

## <a name="performance-targets"></a>Prestationsmål

När du planerar att använda varaktiga funktioner för ett produktionsprogram är det viktigt att ta hänsyn till prestandakraven tidigt i planeringsprocessen. Det här avsnittet innehåller några grundläggande användningsscenarier och förväntade maximala dataflödesnummer.

* **Sekventiell aktivitetskörning**: Det här scenariot beskriver en orchestrator-funktion som kör en serie aktivitetsfunktioner en efter en. Det mest liknar [funktionskedja](durable-functions-sequence.md) provet.
* **Parallell aktivitetskörning**: Det här scenariot beskriver en orchestrator-funktion som utför många aktivitetsfunktioner parallellt med hjälp av [Fan-out, Fan-in-mönster.](durable-functions-cloud-backup.md)
* **Parallell svarsbehandling**: Detta scenario är den andra halvan av [Fan-out, Fan-in](durable-functions-cloud-backup.md) mönster. Den fokuserar på utförandet av fan-in. Det är viktigt att notera att till skillnad från fan-out, fan-in görs av en enda orchestrator funktion instans, och därför kan bara köras på en enda virtuell dator.
* **Bearbetning av externa händelser**: Det här scenariot representerar en enskild orchestratorfunktionsinstans som väntar på [externa händelser](durable-functions-external-events.md), en i taget.
* **Bearbetning av entitetsåtgärd:** Det här scenariot testar hur snabbt en _enda_ [counter entitet](durable-functions-entities.md) kan bearbeta en konstant flöde av åtgärder.

> [!TIP]
> Till skillnad från fan-out, fan-in åtgärder är begränsade till en enda virtuell dator. Om ditt program använder fan-out, fan-in mönster och du är orolig för fan-in prestanda, överväga att dela upp aktivitetsfunktionen fan-out över flera [sub-orkestreringar](durable-functions-sub-orchestrations.md).

I följande tabell visas förväntade *maximala* dataflödesnummer för de tidigare beskrivna scenarierna. "Instans" refererar till en enda instans av en orchestrator-funktion som körs på en enda liten ([A1](../../virtual-machines/sizes-previous-gen.md)) VIRTUELL i Azure App Service. I samtliga fall antas det att [utökade sessioner](#orchestrator-function-replay) är aktiverade. Faktiska resultat kan variera beroende på cpu eller I/O-arbete som utförs av funktionskoden.

| Scenario | Maximalt dataflöde |
|-|-|
| Körning av sekventiell aktivitet | 5 aktiviteter per sekund, per instans |
| Parallellt aktivitetsutförande (utfläktning) | 100 aktiviteter per sekund, per instans |
| Parallell responsbehandling (infläktning) | 150 svar per sekund, per instans |
| Behandling av externa händelser | 50 händelser per sekund, per instans |
| Bearbetning av entitetsoperation | 64 operationer per sekund |

> [!NOTE]
> Dessa siffror är aktuella från och med v1.4.0 (GA) release av varaktiga funktioner förlängning. Dessa siffror kan ändras med tiden när funktionen mognar och som optimeringar görs.

Om du inte ser de dataflödesnummer du förväntar dig och processor- och minnesanvändningen ser felfri ut kontrollerar du om orsaken är relaterad till [hälsotillståndet för ditt lagringskonto](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Tillägget Varaktiga funktioner kan lägga stor belastning på ett Azure Storage-konto och tillräckligt höga belastningar kan resultera i begränsning av lagringskontot.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om katastrofåterställning och geodistribution](durable-functions-disaster-recovery-geo-distribution.md)
