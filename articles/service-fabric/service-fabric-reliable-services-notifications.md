---
title: Reliable Services-meddelanden | Microsoft Docs
description: Konceptuell dokumentationen för Service Fabric Reliable Services meddelanden
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: 4455b259ef2159f9e1ec4991fc533f1843899682
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-services-notifications"></a>Reliable Services-meddelanden
Meddelanden tillåter klienter att spåra ändringar som görs i ett objekt som de är intresserad av. Två typer av objekt som stöder meddelanden: *tillförlitliga Tillståndshanterare* och *tillförlitliga ordlista*.

Vanliga orsaker till med hjälp av meddelanden är:

* Skapa materialiserade vyer, till exempel sekundärindex eller aggregeras filtrerade datavyer repliken tillstånd. Ett exempel är en sorterade index över alla nycklar i tillförlitliga ordlistan.
* Skicka övervakningsdata, till exempel hur många användare som lagts till i den senaste timmen.

Meddelanden skickas som en del av åtgärder. På grund av att ska meddelanden hanteras så snabbt som möjligt och synkron händelser inte får innehåller några kostsamma åtgärder.

## <a name="reliable-state-manager-notifications"></a>Tillstånd för tillförlitlig Manager meddelanden
Tillförlitlig Tillståndshanterare visar meddelanden för följande händelser:

* Transaktionen
  * Checka in
* Tillstånd manager
  * Återskapa
  * Lägga till en tillförlitlig tillstånd
  * Borttagning av en tillförlitlig tillstånd

Tillförlitlig Tillståndshanterare spårar de aktuella inflight transaktionerna. Den enda förändringen i transaktionstillstånd som orsakar en avisering till att utlösa är en transaktion som allokeras.

Tillförlitlig Tillståndshanterare upprätthåller en samling av tillförlitliga tillstånd som tillförlitliga ordlista och tillförlitlig kön. Tillförlitlig Tillståndshanterare utlöses meddelanden när den här samlingen ändras: tillståndet tillförlitliga läggs till eller tas bort eller återskapas hela samlingen.
Samlingen tillförlitliga Tillståndshanterare återskapas i tre fall:

* Återställning: När en replik startar den återställer dess tidigare tillstånd från disken. I slutet av recovery används **NotifyStateManagerChangedEventArgs** eller en händelse som innehåller de återställda tillförlitliga tillstånd.
* Fullständig kopia: innan konfigurationen kan delta i en replik, den har skapas. Ibland kan kräver detta en fullständig kopia av tillförlitliga Tillståndshanterare tillstånd från den primära repliken ska tillämpas på den sekundära repliken som inaktiv. Tillstånd för tillförlitlig Manager på den sekundära repliken använder **NotifyStateManagerChangedEventArgs** eller en händelse som innehåller de tillförlitliga tillstånd som det har fått från den primära repliken.
* Återställ: I scenarier med haveriberedskap repliken kan återställas från en säkerhetskopia via **RestoreAsync**. I sådana fall kan den tillförlitliga tillstånd Manager på den primära repliken använder **NotifyStateManagerChangedEventArgs** eller en händelse som innehåller de tillförlitliga tillstånd som den har återställts från säkerhetskopian.

För att registrera för meddelanden och/eller tillstånd manager meddelanden måste du registrera den **TransactionChanged** eller **StateManagerChanged** händelser på tillförlitliga Tillståndshanterare. En gemensam plats att registrera med dessa händelsehanterare är konstruktören för tillståndskänsliga tjänsten. När du registrerar på konstruktorn kan du inte missar alla aviseringar som orsakas av en ändring under livslängden för **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Den **TransactionChanged** händelsehanteraren använder **NotifyTransactionChangedEventArgs** att ge information om händelsen. Den innehåller egenskapen action (till exempel **NotifyTransactionChangedAction.Commit**) som anger vilken typ av ändring. Den innehåller också transaktionsegenskapen som ger en referens till den transaktion som har ändrats.

> [!NOTE]
> Idag **TransactionChanged** händelser aktiveras bara om genomförs transaktionen. Åtgärden är lika med **NotifyTransactionChangedAction.Commit**. Men i framtiden, händelser kan aktiveras för andra typer av tillståndsändringar för transaktionen. Vi rekommenderar att kontroll av åtgärden och bearbetar händelsen endast om det är något som du förväntar dig.
> 
> 

Följande är ett exempel **TransactionChanged** händelsehanterare.

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

Den **StateManagerChanged** händelsehanteraren använder **NotifyStateManagerChangedEventArgs** att ge information om händelsen.
**NotifyStateManagerChangedEventArgs** har två underklasser: **NotifyStateManagerRebuildEventArgs** och **NotifyStateManagerSingleEntityChangedEventArgs**.
Du använder egenskapen action i **NotifyStateManagerChangedEventArgs** att omvandla **NotifyStateManagerChangedEventArgs** till rätt underklass:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** och **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Följande är ett exempel **StateManagerChanged** meddelandehanteraren.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Tillförlitliga ordlista meddelanden
Tillförlitliga ordlista visar meddelanden för följande händelser:

* Återskapa: Anropas om **ReliableDictionary** dess tillstånd har återställts från en återställda eller kopierade lokala tillstånd eller säkerhetskopiering.
* Rensa: Anropas om tillståndet för **ReliableDictionary** har rensats via den **ClearAsync** metod.
* Lägg till: Anropas när ett objekt har lagts till **ReliableDictionary**.
* Uppdatering: Anropas när ett objekt i **IReliableDictionary** har uppdaterats.
* Ta bort: Anropas när ett objekt i **IReliableDictionary** har tagits bort.

För att få tillförlitliga ordlista meddelanden, måste du registrera med den **DictionaryChanged** händelsehanteraren på **IReliableDictionary**. En gemensam plats att registrera med dessa händelsehanterare är i den **ReliableStateManager.StateManagerChanged** lägga till meddelanden.
Registrera när **IReliableDictionary** har lagts till i **IReliableStateManager** garanterar att du inte missar eventuella meddelanden.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** är exempelmetoden som den föregående **OnStateManagerChangedHandler** exempel anrop.
> 
> 

Föregående koduppsättningar den **IReliableNotificationAsyncCallback** gränssnitt, tillsammans med **DictionaryChanged**. Eftersom **NotifyDictionaryRebuildEventArgs** innehåller en **IAsyncEnumerable** gränssnitt--som behöver räknas asynkront--återskapa meddelanden skickas via  **RebuildNotificationAsyncCallback** i stället för **OnDictionaryChangedHandler**.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> I föregående kod, som en del av meddelandet återskapa är först behålla sammansatt tillstånd avmarkerad. Eftersom samlingen tillförlitliga återskapas med ett nytt tillstånd, är alla tidigare meddelanden irrelevanta.
> 
> 

Den **DictionaryChanged** händelsehanteraren använder **NotifyDictionaryChangedEventArgs** att ge information om händelsen.
**NotifyDictionaryChangedEventArgs** har fem underklasser. Använda egenskapen action i **NotifyDictionaryChangedEventArgs** att omvandla **NotifyDictionaryChangedEventArgs** till rätt underklass:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add** och **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Rekommendationer
* *Gör* slutföra meddelandehändelser så snabbt som möjligt.
* *Inte* köra alla kostsamma åtgärder (till exempel i/o-operationer) som en del av synkron händelser.
* *Gör* Kontrollera åtgärdstypen innan du bearbetar händelsen. Nya åtgärdstyper kan läggas till i framtiden.

Här följer några saker att tänka på:

* Meddelanden skickas som en del av en åtgärd. Till exempel utlöses ett meddelande om återställning som det sista steget i en återställning. En återställning slutförs inte förrän händelsen meddelande bearbetas.
* Eftersom meddelanden skickas som en del av åtgärderna använder finns klienter endast meddelanden för lokalt allokerat åtgärder. Och eftersom åtgärder garanterat bara genomföras lokalt (med andra ord inloggad), de kan eller kan inte ångras i framtiden.
* Gör om, för utlöses ett enda meddelande för varje tillämpade åtgärd. Det innebär att om transaktionen T1 omfattar Create(X), Delete(X) och Create(X), får du en avisering för att skapa X, en för borttagning och en för att skapa igen, i den ordningen.
* Åtgärder tillämpas för transaktioner som innehåller flera åtgärder i den ordning som de togs emot på den primära repliken från användaren.
* Som en del av FALSKT pågår, kan vissa åtgärder att ångra. Aviseringar genereras för dessa ångra-åtgärder, återställer tillståndet för repliken till en stabil. En viktig skillnad Ångra meddelanden är att händelser som har dubblettnycklar aggregeras. Till exempel om är som ångras transaktionen T1, visas ett enda meddelande Delete(X).

## <a name="next-steps"></a>Nästa steg
* [Tillförlitliga samlingar](service-fabric-work-with-reliable-collections.md)
* [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Tillförlitlig säkerhetskopiering och återställning (återställning)](service-fabric-reliable-services-backup-restore.md)
* [För utvecklare för tillförlitlig samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

