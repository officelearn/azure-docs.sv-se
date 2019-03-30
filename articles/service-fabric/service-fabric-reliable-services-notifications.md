---
title: Reliable Services-aviseringar | Microsoft Docs
description: Konceptuell dokumentation för Service Fabric Reliable Services-meddelanden
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: a3df5f28475b03f1799dc1e245c3a7e904b49cb3
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662677"
---
# <a name="reliable-services-notifications"></a>Reliable Services-aviseringar
Meddelanden tillåter klienter att spåra ändringar som görs i ett objekt som de är intresserad av. Två typer av objekt stöder meddelanden: *Reliable State Manager* och *tillförlitlig ordlista*.

Vanliga orsaker till med hjälp av meddelanden är:

* Skapa materialiserade vyer, till exempel sekundära index eller aggregeras filtrerade datavyer repliken tillstånd. Ett exempel är en sorterad index för alla nycklar i tillförlitlig ordlista.
* Skicka övervakningsdata, till exempel hur många användare som lagts till i den senaste timmen.

Meddelanden skickas som en del av tillämpa åtgärder. På grund av att ska meddelanden hanteras så snabbt som möjligt och synkrona händelser inte ska innehålla alla kostsamma åtgärder.

## <a name="reliable-state-manager-notifications"></a>Reliable State Manager-aviseringar
Reliable State Manager visar meddelanden för följande händelser:

* Transaktion
  * Checka in
* Tillståndshanteraren
  * Återskapa
  * Lägga till en tillförlitlig tillstånd
  * Borttagning av en tillförlitlig tillstånd

Reliable State Manager spårar de aktuella aktiva transaktionerna. Den enda förändringen i transaktionstillstånd som orsakar ett meddelande till att utlösa är en transaktion att checkas in.

Reliable State Manager upprätthåller en samling av tillförlitliga tillstånd som tillförlitlig ordlista och tillförlitlig kö. Reliable State Manager utlöses meddelanden när den här samlingen ändras: ett tillförlitlig tillstånd läggs till eller tas bort eller hela samlingen har återskapats.
Samlingen Reliable State Manager återskapas i tre fall:

* Återställning: När en replik startar återställer dess tidigare tillstånd från disken. I slutet av recovery används **NotifyStateManagerChangedEventArgs** innan en händelse som innehåller de återställda tillförlitlig tillstånd.
* Fullständig kopia: Innan en replik kan ansluta till konfigurationen, har det ska skapas. Ibland kan kräver detta en fullständig kopia av Reliable State Manager tillstånd från den primära repliken ska tillämpas på en inaktiv sekundär replik. Används i Reliable State Manager på den sekundära repliken **NotifyStateManagerChangedEventArgs** innan en händelse som innehåller de tillförlitlig tillstånd som det har fått från den primära repliken.
* Återställ: I scenarier med haveriberedskap repliken återställas från en säkerhetskopia via **RestoreAsync**. I sådana fall kan Reliable State Manager på den primära repliken använder **NotifyStateManagerChangedEventArgs** innan en händelse som innehåller de tillförlitlig tillstånd som den har återställts från säkerhetskopian.

För att registrera för meddelanden och/eller tillstånd manager-aviseringar, du måste registrera dig med den **TransactionChanged** eller **StateManagerChanged** händelser i Reliable State Manager. En gemensam plats att registrera med dessa händelsehanterare är konstruktorn för din tillståndskänslig tjänst. När du registrerar på konstruktorn kan du inte missar något meddelande som orsakas av en ändring under livslängd **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Den **TransactionChanged** händelsehanteraren använder **NotifyTransactionChangedEventArgs** att ge information om händelsen. Den innehåller egenskapen action (till exempel **NotifyTransactionChangedAction.Commit**) som anger typen av ändring. Den innehåller också transaktionsegenskapen som ger en referens till den transaktion som har ändrats.

> [!NOTE]
> Idag, **TransactionChanged** händelser aktiveras bara om transaktionen genomförs. Åtgärden är sedan lika **NotifyTransactionChangedAction.Commit**. Men i framtiden, händelser kan aktiveras för andra typer av tillståndsändringar för transaktionen. Vi rekommenderar kontroll av åtgärden och bearbetning av händelsen endast om det är en som du förväntar dig.
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

Följande är ett exempel **StateManagerChanged** meddelandehanterare.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Tillförlitlig ordlista meddelanden
Tillförlitlig ordlista innehåller meddelanden för följande händelser:

* Återskapa: Anropas när **ReliableDictionary** har återställts sitt tillstånd från en återställd eller kopierade lokalt läge eller säkerhetskopiering.
* Rensa: Anropas när tillståndet för **ReliableDictionary** har rensats via den **ClearAsync** metod.
* Lägg till: Anropas när ett objekt läggs till i **ReliableDictionary**.
* Uppdatera: Anropas när ett objekt i **IReliableDictionary** har uppdaterats.
* Ta bort: Anropas när ett objekt i **IReliableDictionary** har tagits bort.

För att få tillförlitlig ordlista meddelanden kan du behöva registrera med den **DictionaryChanged** händelsehanteraren på **IReliableDictionary**. En gemensam plats att registrera med dessa händelsehanterare är i den **ReliableStateManager.StateManagerChanged** Lägg till meddelande.
Registrerar när **IReliableDictionary** läggs till i **IReliableStateManager** säkerställer att du inte missar några meddelanden.

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
> **ProcessStateManagerSingleEntityNotification** är exempelmetoden som föregående **OnStateManagerChangedHandler** exempel anropar.
> 
> 

Föregående koduppsättningar den **IReliableNotificationAsyncCallback** gränssnitt, tillsammans med **DictionaryChanged**. Eftersom **NotifyDictionaryRebuildEventArgs** innehåller en **IAsyncEnumerable** gränssnitt – som behöver att räkna upp asynkront--återskapning meddelanden skickas via  **RebuildNotificationAsyncCallback** i stället för **OnDictionaryChangedHandler**.

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
> I föregående kod, som en del av att bearbeta meddelandet återskapning först avmarkeras behålla aggregerade tillståndet. Eftersom tillförlitlig samling återskapas med statusen ny, är alla tidigare meddelanden inte relevant.
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
* *Inte* köra alla kostsamma åtgärder (till exempel i/o-åtgärder) som en del av synkron händelser.
* *Gör* Kontrollera åtgärdstypen innan du bearbetar händelsen. Nya åtgärdstyper kan läggas till i framtiden.

Här följer några saker att tänka på:

* Meddelanden skickas som en del av en åtgärd. Till exempel utlöses ett meddelande om återställning som det sista steget i en återställningsåtgärd. En återställning slutförs inte förrän händelsen meddelande bearbetas.
* Eftersom meddelanden skickas som en del av de tillämpa åtgärderna, se klienter endast meddelanden för lokalt allokerade åtgärder. Och eftersom operations garanterat bara genomföras lokalt (d.v.s. loggat) kan de kanske eller kanske inte att ångra i framtiden.
* Gör om, för utlöses ett enda meddelande för varje tillämpad åtgärd. Det innebär att om transaktionen T1 innehåller Create(X) och Delete(X) Create(X), du får ett meddelande för att skapa X, en för borttagningen och en för att skapa igen, i den ordningen.
* För transaktioner som innehåller flera åtgärder, tillämpas åtgärder i den ordning som de togs emot på den primära repliken från användaren.
* Som en del av bearbetningsförlopp FALSKT, kan vissa åtgärder ångras. Meddelanden har aktiverats för sådana ångra åtgärder, som återställer tillståndet för replikeringen till en stabil. En viktig skillnad Ångra meddelanden är att händelser som har dubblettnycklar räknas samman. Till exempel om transaktionen T1 är tillintetgörs, visas ett enda meddelande till Delete(X).

## <a name="next-steps"></a>Nästa steg
* [Tillförlitliga samlingar](service-fabric-work-with-reliable-collections.md)
* [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Reliable Services-säkerhetskopiering och återställning (haveriberedskap)](service-fabric-reliable-services-backup-restore.md)
* [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

