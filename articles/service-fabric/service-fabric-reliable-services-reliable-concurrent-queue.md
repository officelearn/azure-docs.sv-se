---
title: ReliableConcurrentQueue i Azure Service Fabric
description: "ReliableConcurrentQueue är en hög genomströmning kö som tillåter parallella enqueues och dequeues."
services: service-fabric
documentationcenter: .net
author: sangarg
manager: timlt
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: sangarg
ms.openlocfilehash: 122cb48149477f295a65b8ee623c647b6db10a86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introduktion till ReliableConcurrentQueue i Azure Service Fabric
Tillförlitliga samtidiga kön är en asynkron transaktionell och replikerade kö vilka funktioner hög samtidighet för sätta och åtgärder som har status Created. Den är utformad för att leverera högt genomflöde och låg fördröjning av lugnt strikt FIFO ordningen som tillhandahålls av [tillförlitliga kön](https://msdn.microsoft.com/library/azure/dn971527.aspx) och i stället tillhandahåller en bästa sortering.

## <a name="apis"></a>API:er

|Samtidiga kön                |Tillförlitliga samtidiga kön                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Uppgiften EnqueueAsync (ITransaction tx T objekt)                       |
| bool TryDequeue (ut T resultat)  | Uppgiften < ConditionalValue < T >> TryDequeueAsync (tx ITransaction)  |
| int Count()                    | lång Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Jämförelse med [tillförlitliga kön](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Tillförlitliga samtidiga kön erbjuds som ett alternativ till [tillförlitliga kön](https://msdn.microsoft.com/library/azure/dn971527.aspx). Det ska användas i fall där strikt FIFO ordning inte krävs, som garanterar FIFO kräver en kompromiss med samtidighet.  [Tillförlitliga kön](https://msdn.microsoft.com/library/azure/dn971527.aspx) använder lås för att genomdriva FIFO ordning med maximalt en transaktion tillåts att köa och maximalt en transaktion får status Created i taget. Jämförelse tillförlitliga samtidiga kön sänker onlinebeställning begränsningen och gör att alla antalet samtidiga transaktioner interleave sina sätta och åtgärder som har status Created. Bästa ordning har angetts men den relativa sorteringen av två värden i en tillförlitlig samtidiga kö kan inte garanteras.

Tillförlitliga samtidiga kön ger högre genomflöde och kortare svarstid än [tillförlitliga kö](https://msdn.microsoft.com/library/azure/dn971527.aspx) när det finns flera samtidiga transaktioner som utför enqueues och/eller dequeues.

Ett exempel på en användningsfall för ReliableConcurrentQueue är den [meddelandekö](https://en.wikipedia.org/wiki/Message_queue) scenario. I det här scenariot en eller flera meddelandeproducenter skapa och Lägg till objekt i kön och en eller flera meddelandet konsumenter pull-meddelanden från kön och bearbeta dem. Flera producenter och konsumenter fungerar oberoende av varandra, med samtidiga transaktioner ska kunna bearbeta kön.

## <a name="usage-guidelines"></a>Riktlinjer för användning
* Kön förväntar sig att objekten i kön har en låg Bevarandeperiod. Det vill säga skulle objekt inte kvar i kön för lång tid.
* Kön garanterar inte strikt FIFO ordning.
* Kön läser inte sin egen skrivningar. Om ett objekt i kö i en transaktion, kommer den inte vara synliga för en dequeuer inom samma transaktion.
* Dequeues inte är isolerade från varandra. Om objektet *A* har tagits bort i transaktion *txnA*, även om *txnA* är därmed inte verkställas objektet *A* skulle inte till en samtidig transaktion *txnB*.  Om *txnA* avbryts, *A* ska vara synlig för *txnB* omedelbart.
* *TryPeekAsync* beteende kan implementeras med hjälp av en *TryDequeueAsync* och avbryter transaktionen. Ett exempel på detta finns i avsnittet Programming mönster.
* Antalet är icke-transaktionell. Den kan användas för att få en uppfattning om antalet element i kön, men representerar point-in-time och kan inte förlita sig på.
* Billigare bearbetning på dequeued objekt ska inte utföras medan transaktionen är aktiv för att undvika långvariga transaktioner som kan påverka prestanda på datorn.

## <a name="code-snippets"></a>Kodstycken
Låt oss titta på några kodstycken och deras förväntade produktion. Undantagshantering ignoreras i det här avsnittet.

### <a name="enqueueasync"></a>EnqueueAsync
Här följer några kodstycken för att använda EnqueueAsync följt av de förväntade utdata.

- *Fall 1: Enkel köa aktiviteten*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Anta att aktiviteten har slutförts och att det finns inga samtidiga transaktioner ändra kön. Användaren kan förvänta sig kön att innehålla objekt i något av följande ordning:

> 10, 20

> 20, 10


- *Fall 2: Parallell köa aktiviteten*

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

Anta att aktiviteterna har slutförts, att aktiviteterna har körts parallellt och att det inte finns några andra samtidiga transaktioner ändra kön. Inga härledning kan göras om ordningen på objekten i kön. Objekten kan visas i någon av 4 för det här kodstycket! möjliga ordningar.  Kön försöker placera objekten i ordningen de ursprungliga (köas), men kan tvingas att ändra ordning på dem på grund av samtidiga åtgärder eller fel.


### <a name="dequeueasync"></a>DequeueAsync
Här följer några kodstycken för att använda TryDequeueAsync följt av de förväntade utdata. Anta att kön redan fylls med följande objekt i kö:
> 10, 20, 30, 40, 50, 60

- *Fall 1: En har status Created aktivitet*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Anta att aktiviteten har slutförts och att det finns inga samtidiga transaktioner ändra kön. Eftersom ingen härledning kan göras om i vilken ordning av objekten i kön måste alla tre objekt kan vara togs bort från kön, i valfri ordning. Kön försöker placera objekten i ordningen de ursprungliga (köas), men kan tvingas att ändra ordning på dem på grund av samtidiga åtgärder eller fel.  

- *Fall 2: Parallell status Created aktivitet*

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

Anta att aktiviteterna har slutförts, att aktiviteterna har körts parallellt och att det inte finns några andra samtidiga transaktioner ändra kön. Eftersom ingen härledning kan göras om i vilken ordning av objekten i kön, listorna *dequeue1* och *dequeue2* varje innehåller två objekt, i vilken ordning som helst.

Samma objektet kommer *inte* visas i båda listorna. Därför om dequeue1 har *10*, *30*, dequeue2 skulle ha *20*, *40*.

- *Fall 3: Status Created ordning med Transaktionsavbrott*

Avbryter en transaktion med pågående dequeues placeringar objekten tillbaka på chefen för kön. Den ordning som objekten är lägga tillbaka i toppen av kön är inte säkert. Låt oss titta på följande kod:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Anta att objekten har tagits bort i följande ordning:
> 10, 20

När vi avbryter transaktionen skulle objekten läggas till chefen för kön i något av följande ordning:
> 10, 20

> 20, 10

Detsamma gäller för alla fall där transaktionen inte har *genomförd*.

## <a name="programming-patterns"></a>Mönster för programmering
I det här avsnittet tittar vi på några programmering mönster som kan vara användbart i med hjälp av ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Batch Dequeues
A rekommenderas programming mönstret är för konsumenten uppgiften att batchen dess dequeues i stället för att utföra en i taget har status Created. Användaren kan välja att begränsa fördröjningar mellan varje batch eller batchstorleken. Följande kodavsnitt visar den här programmeringsmodell.  Observera att i det här exemplet bearbetning görs när transaktionen är genomförd, så om ett fel uppstår under bearbetningen av, de obehandlade artiklarna försvinner utan har bearbetats.  Bearbetningen kan också ske inom transaktionsomfånget, men detta kan ha en negativ inverkan på prestanda och kräver hantering av de objekt som redan har bearbetats.

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

### <a name="best-effort-notification-based-processing"></a>Bästa meddelandebaserad bearbetning
En annan intressant programming mönster använder Count-API. Här kan vi implementera bästa meddelandebaserad bearbetning för kön. Kön antal kan användas för att begränsa en sätta eller en dequeue aktivitet.  Observera att som i föregående exempel, eftersom sker bearbetningen utanför transaktionen, obearbetat objekt kan gå förlorade om ett fel uppstår under bearbetningen.

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

### <a name="best-effort-drain"></a>Bästa tömning
Tömning av kön kan inte garanteras på grund av datastrukturen samtidiga.  Det är möjligt att även om inga användare åtgärder på kön finns relä, ett visst anrop till TryDequeueAsync inte kan returnera ett objekt som tidigare var köas och bekräftats.  Objektet köas garanterat *slutligen* bli synlig för status Created, men utan en mekanism för out-of-band-kommunikation, ett oberoende konsumenten kan inte vet att kön har uppnått ett stabilt tillstånd även om alla tillverkare har stoppats och inga nya sätta tillåts. Därför är åtgärden tömning bästa som implementeras nedan.

Användaren måste stoppa alla ytterligare producenten och konsumentuppgifter och vänta tills alla pågående transaktioner att avbrytas eller genomföras innan du försöker att tömma kön.  Om användaren känner det förväntade antalet objekt i kön, kan de ställa in ett meddelande som signalerar till att alla objekt har har tagits bort.

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
ReliableConcurrentQueue innehåller inte den *TryPeekAsync* api. Användare kan hämta titt semantiska med en *TryDequeueAsync* och avbryter transaktionen. I det här exemplet dequeues bearbetas bara om objektets värde är större än *10*.

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
* [Reliable Services-meddelanden](service-fabric-reliable-services-notifications.md)
* [Reliable Services säkerhetskopiering och återställning (Disaster Recovery)](service-fabric-reliable-services-backup-restore.md)
* [Konfiguration av tillförlitliga tillstånd Manager](service-fabric-reliable-services-configuration.md)
* [Komma igång med Service Fabric Web API-tjänster](service-fabric-reliable-services-communication-webapi.md)
* [Avancerad användning av tjänsterna tillförlitliga programmeringsmodellen](service-fabric-reliable-services-advanced-usage.md)
* [För utvecklare för tillförlitlig samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
