---
title: ReliableConcurrentQueue i Azure Service Fabric
description: ReliableConcurrentQueue är en kö för stora dataflöden som tillåter parallell placerar det i kö och dequeues.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: dbdfa4686c047fa7cf5d74cd9aca768447f9db93
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663660"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introduktion till ReliableConcurrentQueue i Azure Service Fabric
Tillförlitlig samtidiga kön är en kö för asynkron transaktionell och replikerade vilka funktioner hög samtidighet för sätta och ta bort från kön operations. Den är utformad för att tillhandahålla stora dataflöden och låg latens genom distributionsfrågebegränsningar strikt FIFO sorteringen tillhandahålls av [tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx) och i stället tillhandahåller en mån sortering.

## <a name="apis"></a>API:er

|Samtidiga kö                |Reliable Concurrent Queue                                         |
|--------------------------------|------------------------------------------------------------------|
| Annullera Enqueue(T item)           | Uppgiften EnqueueAsync (ITransaction tx, T objekt)                       |
| bool TryDequeue (ut T resultat)  | Uppgiften < ConditionalValue < T >> TryDequeueAsync (ITransaction tx)  |
| int Count()                    | lång Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Jämförelse med [tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Tillförlitlig samtidiga kö erbjuds som ett alternativ till [tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx). Det ska användas i fall där strikt FIFO-sortering inte krävs, som garanterar FIFO kräver en kompromiss med samtidighet.  [Tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx) använder lås för att framtvinga FIFO ordning, med högst en transaktion tillåts att köa och högst en transaktion kan ta bort från kön i taget. Däremot tillförlitlig samtidiga kö sänker skrivordning begränsningen och tillåter alla antalet samtidiga transaktioner interfoliering sina placera och ta bort från kön åtgärder. Mån ordning har angetts men relativa ordning av två värden i en tillförlitlig samtidiga kö kan aldrig garanteras.

Tillförlitlig samtidiga kö ger högre genomflöde och kortare svarstid än [tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx) när det finns flera samtidiga transaktioner som utför placerar det i kö och/eller dequeues.

Ett sampel användningsfall för ReliableConcurrentQueue är den [meddelandekö](https://en.wikipedia.org/wiki/Message_queue) scenario. I det här scenariot en eller flera meddelandeproducenter skapa och Lägg till objekt i kön och en eller flera meddelandet konsumenter pull-meddelanden från kön och bearbeta dem. Flera producenter och konsumenter fungerar oberoende av varandra, med samtidiga transaktioner ska kunna bearbeta kön.

## <a name="usage-guidelines"></a>Riktlinjer för användning
* Kön förväntar sig att objekten i kön har en låg kvarhållningsperiod. Det vill säga objekten skulle inte finns kvar i kön för lång tid.
* Kön garanterar inte strikt FIFO-ordning.
* Kön läser inte sin egen skrivningar. Om ett objekt i kö i en transaktion, kommer det inte att visas för en dequeuer inom samma transaktion.
* Dequeues inte är isolerade från varandra. Om objektet *A* har tagits bort från om kön i transaktionen *txnA*, även om *txnA* är därmed inte verkställas objekt *A* inte att visas för en samtidig transaktionen *txnB*.  Om *txnA* avbryter, *A* blir synliga för *txnB* omedelbart.
* *TryPeekAsync* beteende kan implementeras med hjälp av en *TryDequeueAsync* och sedan avbryts transaktionen. Ett exempel på detta finns i avsnittet Programming mönster.
* Antalet är icke-transaktionell. Den kan användas för att få en uppfattning om antalet element i kön, men representerar point-in-time och det går inte att förlita sig på.
* Dyra bearbetning på dequeued objekt bör inte utföras medan transaktionen är aktiv för att undvika långvariga transaktioner som kan ha en prestandapåverkan på systemet.

## <a name="code-snippets"></a>Kodfragment
Låt oss titta på några kodfragment och deras förväntade utdata. Undantagshantering ignoreras i det här avsnittet.

### <a name="enqueueasync"></a>EnqueueAsync
Här följer några kodfragment för att använda EnqueueAsync följt av deras förväntade utdata.

- *Fall 1: Placera aktivitet*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Anta att uppgiften har slutförts och att det fanns inga samtidiga transaktioner ändra kön. Du kan förvänta sig kön ska innehålla objekten i någon av följande order:

> 10, 20
> 
> 20, 10


- *Fall 2: Placera parallellt*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Anta att aktiviteterna slutförs korrekt, att aktiviteterna har körts parallellt och att det fanns inga andra samtidiga transaktioner ändra kön. Inga inferens kan göras om ordningen på objekten i kön. Objekten kan visas i någon av 4 för det här kodfragmentet! möjliga ordningar.  Kön försöker placera objekt i den ursprungliga ordningen (i kö), men kan tvingas att ordna om dem på grund av samtidiga åtgärder eller fel.


### <a name="dequeueasync"></a>DequeueAsync
Här följer några kodfragment för att använda TryDequeueAsync följt av de förväntade utdata. Anta att kön redan fyllts med följande objekt i kön:
> 10, 20, 30, 40, 50, 60

- *Fall 1: Enskild borttagningar uppgift*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Anta att uppgiften har slutförts och att det fanns inga samtidiga transaktioner ändra kön. Eftersom ingen inferens kan göras om ordningen på objekten i kön, alla tre objekt kan vara bort från kön, i valfri ordning. Kön försöker placera objekt i den ursprungliga ordningen (i kö), men kan tvingas att ordna om dem på grund av samtidiga åtgärder eller fel.  

- *Fall 2: Parallell borttagningar uppgift*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Anta att aktiviteterna slutförs korrekt, att aktiviteterna har körts parallellt och att det fanns inga andra samtidiga transaktioner ändra kön. Eftersom ingen inferens kan göras om ordningen på objekten i kön, listorna *dequeue1* och *dequeue2* var och en innehåller två objekt, i valfri ordning.

Samma objekt kommer *inte* visas i båda listorna. Därför om dequeue1 har *10*, *30*, och sedan dequeue2 skulle ha *20*, *40*.

- *Fall 3: Ta bort från kön ordning med transaktionen avbröt*

Avbryter en transaktion med pågående dequeues placeringar objekten igen chefen för kön. Den ordning som objekten är lägga tillbaka i huvudet för kön är inte säkert. Låt oss titta på följande kod:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Anta att objekten har tagits bort från kön i följande ordning:
> 10, 20

När vi avbryta transaktionen skulle objekten läggas till i sidhuvudet kön i någon av följande order:
> 10, 20
> 
> 20, 10

Detsamma gäller för alla fall där transaktionen inte har *genomförd*.

## <a name="programming-patterns"></a>Mönster för programmering
I det här avsnittet, låt oss titta på några programmering mönster som kan vara användbar i med hjälp av ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Batch Dequeues
Ett rekommenderat programming mönstret är för konsument-aktiviteten så att batch dess dequeues istället för att utföra en ta ur kö i taget. Användaren kan välja att begränsa fördröjningar mellan varje batch eller batchstorleken. Följande kodavsnitt visar den här programmeringsmodell.  Observera att i det här exemplet, bearbetningen är klar när transaktionen genomförs, så om ett fel uppstår under bearbetningen av, obearbetade objekten kommer att gå förlorade utan har bearbetats.  Bearbetningen kan också göras inom transaktionsomfattning, men detta kan ha en negativ inverkan på prestanda och kräver hantering av de objekt som redan bearbetats.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Mån meddelandebaserad bearbetning
En annan intressant programming mönstret använder antal API: et. Här kan vi implementera bästa meddelandebaserad bearbetning för kön. Kön antal kan användas för att begränsa en sätta eller ta uppgift.  Observera att som i föregående exempel, eftersom bearbetningen sker utanför transaktionen obearbetade objekt kan gå förlorade om ett fel uppstår under bearbetningen.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Mån Drain
Resurser för kön kan inte garanteras på grund av datastrukturen samtidiga.  Det är möjligt att även om inga användaråtgärder för kön är pågående, ett visst anrop till TryDequeueAsync inte kanske returnerar ett objekt som tidigare var i kö och Skickat.  Objektet i kön garanterar att *så småningom* blir synliga borttagningar, men utan en mekanism för out-of-band-kommunikation, en oberoende konsumenten kan inte vet att kön har nått en stabil även om alla tillverkare har stoppats och någon ny sätta åtgärder tillåts. Därför är åtgärden drain mån som implementeras nedan.

Du bör stoppa alla ytterligare producent och konsumentuppgifter och vänta tills alla pågående transaktioner att avbrytas eller genomföras innan du försöker att tömma kön.  Om användaren känner det förväntade antalet objekt i kön, kan de ställa in ett meddelande som visar att alla objekt har tagits bort från kön.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Granska
ReliableConcurrentQueue ger inte den *TryPeekAsync* api. Användarna kan få peek semantiska med hjälp av en *TryDequeueAsync* och sedan avbryts transaktionen. I det här exemplet dequeues bearbetas bara om objektets värde är större än *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Måste läsa
* [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services-aviseringar](service-fabric-reliable-services-notifications.md)
* [Reliable Services-säkerhetskopiering och återställning (Disaster Recovery)](service-fabric-reliable-services-backup-restore.md)
* [Konfiguration av Reliable State Manager](service-fabric-reliable-services-configuration.md)
* [Komma igång med Service Fabric Web API-tjänster](service-fabric-reliable-services-communication-webapi.md)
* [Avancerad användning av programmeringsmodellen Reliable Services](service-fabric-reliable-services-advanced-usage.md)
* [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
