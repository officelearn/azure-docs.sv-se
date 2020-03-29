---
title: ReliableConcurrentQueue i Azure Service Fabric
description: ReliableConcurrentQueue är en kö med hög genomströmning som tillåter parallella köer och dequeues.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: a7115db8259fde0e87e53557ecef730f8e82d2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462737"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introduktion till ReliableConcurrentQueue i Azure Service Fabric
Tillförlitlig samtidig kö är en asynkron, transaktionell och replikerad kö som har hög samtidighet för enqueue- och avköningsåtgärder. Den är utformad för att leverera högt dataflöde och låg latens genom att koppla av den strikta FIFO-beställningen som tillhandahålls av [Reliable Queue](https://msdn.microsoft.com/library/azure/dn971527.aspx) och ger i stället en beställning med bäst insats.

## <a name="apis"></a>API:er

|Samtidig kö                |Reliable Concurrent Queue                                         |
|--------------------------------|------------------------------------------------------------------|
| annullera enqueue(T-objekt)           | AktivitetsenqueueAsync(ITransaction tx, T-objekt)                       |
| bool TryDequeue(ut T resultat)  | Aktivitet< villkorligt värde < T > > TryDequeueAsync(ITransaction tx)  |
| Antal int()                    | långa antal()                                                     |

## <a name="comparison-with-reliable-queue"></a>Jämförelse med [tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Tillförlitlig samtidig kö erbjuds som ett alternativ till [tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx). Det bör användas i fall där strikt FIFO beställning inte krävs, eftersom garantera FIFO kräver en avvägning med samtidighet.  [Tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx) använder lås för att framtvinga FIFO-beställning, med högst en transaktion tillåts att köa och högst en transaktion tillåts att avqueue åt gången. I jämförelse, Tillförlitlig samtidig kö slappnar ordningsbegränsningen och tillåter valfritt antal samtidiga transaktioner att interleave sina enqueue och dequeue operationer. Beställning med bäst ansträngning tillhandahålls, men den relativa ordningen på två värden i en tillförlitlig samtidig kö kan aldrig garanteras.

Tillförlitlig samtidig kö ger högre dataflöde och lägre svarstid än [tillförlitlig kö](https://msdn.microsoft.com/library/azure/dn971527.aspx) när det finns flera samtidiga transaktioner som utför efterköer och/eller dequeues.

Ett exempel på användningsfall för ReliableConcurrentQueue är scenariot [Meddelandekö.](https://en.wikipedia.org/wiki/Message_queue) I det här fallet skapar och lägger en eller flera meddelandeproducenter till objekt i kön, och ett eller flera meddelandekonsumenter hämtar meddelanden från kön och bearbetar dem. Flera producenter och konsumenter kan arbeta självständigt, med samtidiga transaktioner för att bearbeta kön.

## <a name="usage-guidelines"></a>Riktlinjer för användning
* Kön förväntar sig att objekten i kön har en låg kvarhållningsperiod. Det vill än om objekten inte skulle stanna i kön under en längre tid.
* Kön garanterar inte strikt FIFO-beställning.
* Kön läser inte sina egna skrivningar. Om en artikel är enqueued inom en transaktion, kommer den inte att vara synlig för en dequeuer inom samma transaktion.
* Dequeues är inte isolerade från varandra. Om artikel *A* är avqueuerad i transaktion *txnA*, även om *txnA* inte har bekräftats, skulle artikel *A* inte vara synlig för en samtidig transaktion *txnB*.  Om *txnA* avbryts blir *A* synligt för *txnB* omedelbart.
* *TryPeekAsync-beteende* kan implementeras med hjälp av en *TryDequeueAsync* och sedan avbryta transaktionen. Ett exempel på detta finns i avsnittet Programmeringsmönster.
* Antalet är icke-transaktionellt. Den kan användas för att få en uppfattning om antalet element i kön, men representerar en point-in-time och kan inte åberopas.
* Dyr bearbetning på de utsända artiklarna bör inte utföras medan transaktionen är aktiv, för att undvika långvariga transaktioner som kan ha en prestandapåverkan på systemet.

## <a name="code-snippets"></a>Kodfragment
Låt oss titta på några kodavsnitt och deras förväntade utdata. Undantagshantering ignoreras i det här avsnittet.

### <a name="instantiation"></a>Instansiering
Att skapa en instans av en tillförlitlig samtidig kö liknar alla andra tillförlitliga samlingar.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Här är några kodavsnitt för att använda EnqueueAsync följt av deras förväntade utdata.

- *Fall 1: En enda köuppgift*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Anta att uppgiften har slutförts och att det inte fanns några samtidiga transaktioner som ändrar kön. Användaren kan förvänta sig att kön innehåller artiklarna i någon av följande order:

> 10, 20
> 
> 20, 10


- *Fall 2: Parallell köuppgift*

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

Anta att de uppgifter som slutförts har slutförts, att aktiviteterna kördes parallellt och att det inte fanns några andra samtidiga transaktioner som ändrar kön. Ingen slutsats kan göras om ordningen på objekt i kön. För detta kodavsnitt kan objekten visas i någon av de 4! möjliga beställningar.  Kön försöker behålla artiklarna i den ursprungliga (enqueued) ordning, men kan tvingas att ändra ordning på dem på grund av samtidiga åtgärder eller fel.


### <a name="dequeueasync"></a>AvqueueAsync
Här är några kodavsnitt för att använda TryDequeueAsync följt av förväntade utdata. Anta att kön redan är ifylld med följande objekt i kön:
> 10, 20, 30, 40, 50, 60

- *Fall 1: Enkel Dequeue-uppgift*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Anta att uppgiften har slutförts och att det inte fanns några samtidiga transaktioner som ändrar kön. Eftersom ingen slutsats kan göras om ordningen på artiklarna i kön, kan tre av objekten uteslutas, i valfri ordning. Kön försöker behålla artiklarna i den ursprungliga (enqueued) ordning, men kan tvingas att ändra ordning på dem på grund av samtidiga åtgärder eller fel.  

- *Fall 2: Parallell dequeue uppgift*

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

Anta att de uppgifter som slutförts har slutförts, att aktiviteterna kördes parallellt och att det inte fanns några andra samtidiga transaktioner som ändrar kön. Eftersom ingen slutsats kan göras om ordningen på objekten i kön, kommer listorna *dequeue1* och *dequeue2* att innehålla två objekt vardera, i valfri ordning.

Samma objekt visas *inte* i båda listorna. Därför, om dequeue1 har *10*, *30*, då dequeue2 skulle ha *20*, *40*.

- *Fall 3: Avqueue beställning med transaktionen Avbryt*

Om du avbryter en transaktion med dequeues under flygning visas artiklarna tillbaka i köns huvud. Den ordning i vilken artiklarna sätts tillbaka på köhuvudet är inte garanterad. Låt oss titta på följande kod:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Anta att artiklarna har köats i följande ordning:
> 10, 20

När vi avbryter transaktionen läggs artiklarna tillbaka till chefen för kön i någon av följande order:
> 10, 20
> 
> 20, 10

Detsamma gäller för alla fall där transaktionen inte har *genomförts.*

## <a name="programming-patterns"></a>Programmeringsmönster
I det här avsnittet kan vi titta på några programmeringsmönster som kan vara till hjälp för att använda ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Batch-dequeues
Ett rekommenderat programmeringsmönster är att konsumentuppgiften batchar sina dequeues i stället för att utföra en dequeue i taget. Användaren kan välja att begränsa fördröjningar mellan varje batch eller batchstorlek. Följande kodavsnitt visar den här programmeringsmodellen. Tänk på att i det här exemplet utförs bearbetningen efter att transaktionen har genomförts, så om ett fel skulle inträffa under bearbetningen kommer de obearbetade artiklarna att gå förlorade utan att ha bearbetats.  Alternativt kan bearbetningen göras inom transaktionens omfattning, men det kan ha en negativ inverkan på prestanda och kräver hantering av de artiklar som redan bearbetats.

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

### <a name="best-effort-notification-based-processing"></a>Anmälan-baserad behandling för bästa försök
En annan intressant programmering mönster använder count API. Här kan vi implementera meddelandebaserad bearbetning för bästa möjliga insatser för kön. Kön Antal kan användas för att begränsa en enqueue eller en dequeue-uppgift.  Observera att som i föregående exempel, eftersom bearbetningen sker utanför transaktionen, kan obearbetade artiklar gå förlorade om ett fel uppstår under bearbetningen.

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

### <a name="best-effort-drain"></a>Best-Effort Drain
En tömning av kön kan inte garanteras på grund av datastrukturens samtidiga karaktär.  Det är möjligt att även om inga användaråtgärder i kön är under flygning, kan ett visst anrop till TryDequeueAsync inte returnera ett objekt som tidigare har köats och bekräftats.  Den köade varan är garanterad att *så småningom* bli synlig för att dequeue, men utan en out-of-band kommunikationsmekanism, en oberoende konsument kan inte veta att kön har nått en steady-state även om alla producenter har stoppats och inga nya kö verksamhet är tillåtna. Således är dräneringen bäst-ansträngning som genomförs nedan.

Användaren bör stoppa alla ytterligare producent- och konsumentuppgifter och vänta på att alla transaktioner under flygning ska genomföras eller avbrytas innan du försöker tömma kön.  Om användaren känner till det förväntade antalet objekt i kön kan de ställa in ett meddelande som signalerar att alla objekt har köats.

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
ReliableConcurrentQueue tillhandahåller inte *TryPeekAsync* api. Användare kan få peek semantiska med hjälp av en *TryDequeueAsync* och sedan avbryta transaktionen. I det här exemplet bearbetas dequeues endast om artikelns värde är större än *10*.

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
* [Snabbstart för tillförlitliga tjänster](service-fabric-reliable-services-quick-start.md)
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Meddelanden om tillförlitliga tjänster](service-fabric-reliable-services-notifications.md)
* [Tillförlitlig säkerhetskopiering och återställning av tjänster (haveriberedskap)](service-fabric-reliable-services-backup-restore.md)
* [Tillförlitlig tillståndshanterare konfiguration](service-fabric-reliable-services-configuration.md)
* [Komma igång med Service Fabric Web API Services](service-fabric-reliable-services-communication-webapi.md)
* [Avancerad användning av programmeringsmodellen för tillförlitliga tjänster](service-fabric-reliable-services-advanced-usage.md)
* [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
