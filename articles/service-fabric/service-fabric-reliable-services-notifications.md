---
title: Meddelanden om tillförlitliga tjänster
description: Konceptuell dokumentation för service fabric reliable services-meddelanden för reliable state manager och tillförlitlig ordlista
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: 1f3239ea1da252ccd84c6572b562756c8fd1677d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639572"
---
# <a name="reliable-services-notifications"></a>Meddelanden om tillförlitliga tjänster
Med aviseringar kan klienter spåra de ändringar som görs i ett objekt som de är intresserade av. Två typer av objekt stöder meddelanden: *Reliable State Manager* och Reliable *Dictionary*.

Vanliga orsaker till att använda meddelanden är:

* Skapa materialiserade vyer, till exempel sekundära index eller aggregerade filtrerade vyer av replikens tillstånd. Ett exempel är ett sorterat index över alla nycklar i Tillförlitlig ordlista.
* Skickar övervakningsdata, till exempel antalet användare som lagts till under den senaste timmen.

Meddelanden utlöses som en del av att tillämpa åtgärder. På grund av detta bör meddelanden hanteras så snabbt som möjligt, och synkrona händelser bör inte innehålla några dyra åtgärder.

## <a name="reliable-state-manager-notifications"></a>Tillförlitliga tillståndshanterare meddelanden
Reliable State Manager ger meddelanden för följande händelser:

* Transaktion
  * Checka in
* Statligt ansvarig
  * Återskapa
  * Tillägg av ett tillförlitligt tillstånd
  * Avlägsnande av ett tillförlitligt tillstånd

Reliable State Manager spårar de aktuella inflight-transaktionerna. Den enda ändring i transaktionstillståndet som gör att ett meddelande utlöses är en transaktion som genomförs.

Reliable State Manager upprätthåller en samling tillförlitliga tillstånd som tillförlitlig ordlista och tillförlitlig kö. Reliable State Manager utlöser meddelanden när den här samlingen ändras: ett tillförlitligt tillstånd läggs till eller tas bort, eller så återskapas hela samlingen.
Reliable State Manager-samlingen byggs om i tre fall:

* Återställning: När en replik startar återställs dess tidigare tillstånd från disken. I slutet av återställningen används **NotifyStateManagerChangedEventArgs** för att avfyra en händelse som innehåller uppsättningen återställda tillförlitliga tillstånd.
* Fullständig kopia: Innan en replik kan ansluta till konfigurationsuppsättningen måste den byggas. Ibland kräver detta en fullständig kopia av Reliable State Manager tillstånd från den primära repliken som ska tillämpas på den inaktiva sekundära repliken. Reliable State Manager på den sekundära repliken använder **NotifyStateManagerChangedEventArgs** för att avfyra en händelse som innehåller uppsättningen tillförlitliga tillstånd som den har förvärvat från den primära repliken.
* Återställning: I katastrofåterställningsscenarier kan replikens tillstånd återställas från en säkerhetskopia via **RestoreAsync**. I sådana fall använder Reliable State Manager på den primära repliken **NotifyStateManagerChangedEventArgs** för att avfyra en händelse som innehåller uppsättningen tillförlitliga tillstånd som återställs från säkerhetskopian.

Om du vill registrera dig för transaktionsmeddelanden och/eller tillståndshanterare måste du registrera dig med **händelserna TransactionChanged** eller **StateManagerChanged** på Reliable State Manager. En vanlig plats att registrera sig hos dessa händelsehanterare är konstruktorn för din tillståndskänsliga tjänst. När du registrerar dig på konstruktorn missar du inte något meddelande som orsakas av en ändring under **IReliableStateManagers**livstid .

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Eventhanteraren **TransactionChanged** använder **NotifyTransactionChangedEventArgs** för att ge information om händelsen. Den innehåller åtgärdsegenskapen (till exempel **NotifyTransactionChangedAction.Commit**) som anger typen av ändring. Den innehåller också transaktionsegenskapen som ger en referens till transaktionen som ändrades.

> [!NOTE]
> Idag **utlöses TransactionChanged-händelser** endast om transaktionen har genomförts. Åtgärden är sedan lika med **NotifyTransactionChangedAction.Commit**. Men i framtiden kan händelser tas upp för andra typer av ändringar i transaktionstillstånd. Vi rekommenderar att du kontrollerar åtgärden och bearbetar händelsen endast om den är en som du förväntar dig.
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

**Eventhanteraren StateManagerChanged** använder **NotifyStateManagerChangedEventArgs** för att ge information om händelsen.
**NotifyStateManagerChangedEventArgs** har två underklasser: **NotifyStateManagerRebuildEventArgs** och **NotifyStateManagerSingleEntityChangedEventArgs**.
Du kan använda åtgärdsegenskapen i **NotifyStateManagerChangedEventArgs** för att **casta NotifyStateManagerChangedEventArgs** till rätt underklass:

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

## <a name="reliable-dictionary-notifications"></a>Tillförlitliga ordlistemeddelanden
Reliable Dictionary innehåller meddelanden för följande händelser:

* Återskapa: Anropad när **ReliableDictionary** har återställt sitt tillstånd från ett återställt eller kopierat lokalt tillstånd eller säkerhetskopiering.
* Rensa: Anropad när tillståndet **för ReliableDictionary** har rensats med metoden **ClearAsync.**
* Lägg till: Anropas när ett objekt har lagts till **i ReliableDictionary**.
* Uppdatering: Anropas när ett objekt i **IReliableDictionary** har uppdaterats.
* Ta bort: Anropas när ett objekt i **IReliableDictionary** har tagits bort.

För att få Tillförlitliga ordlistemeddelanden måste du registrera dig hos **Händelsehanteraren DictionaryChanged** på **IReliableDictionary**. En vanlig plats att registrera med dessa händelsehanterare finns i **meddelandet ReliableStateManager.StateManagerChanged.**
Registrering när **IReliableDictionary** läggs till **IReliableStateManager** säkerställer att du inte missar några meddelanden.

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
> **ProcessStateManagerSingleEntityNotification** är den exempelmetod som föregående **OnStateManagerChangedHandler** exempel anropar.
> 
> 

Den föregående koden anger **gränssnittet IReliableNotificationAsyncCallback,** tillsammans med **DictionaryChanged**. Eftersom **NotifyDictionaryRebuildEventArgs** innehåller ett **IAsyncEnumerable** gränssnitt - som måste räknas upp asynkront - återskapa meddelanden avfyras via **RebuildNotificationAsyncCallback** istället för **OnDictionaryChangedHandler**.

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
> I föregående kod, som en del av bearbetningen av återskapningsmeddelandet, rensas först det underhållna aggregerade tillståndet. Eftersom den tillförlitliga samlingen återskapas med ett nytt tillstånd är alla tidigare meddelanden irrelevanta.
> 
> 

Händelsehanteraren **DictionaryChanged** använder **NotifyDictionaryChangedEventArgs** för att ge information om händelsen.
**NotifyDictionaryChangedEventArgs** har fem underklasser. Använd åtgärdsegenskapen i **NotifyDictionaryChangedEventArgs** för att casta **NotifyDictionaryChangedEventArgs** till rätt underklass:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add:** **NotifyDictionaryItemAddedEventArgs**
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
* *Gör* fullständiga meddelandehändelser så fort som möjligt.
* *Kör inte* några dyra åtgärder (till exempel I/O-åtgärder) som en del av synkrona händelser.
* *Kontrollera* åtgärdstypen innan du bearbetar händelsen. Nya åtgärdstyper kan läggas till i framtiden.

Här är några saker att tänka på:

* Meddelanden utlöses som en del av körningen av en åtgärd. Ett återställningsmeddelande utlöses till exempel som det sista steget i en återställningsåtgärd. En återställning slutförs inte förrän meddelandehändelsen har bearbetats.
* Eftersom meddelanden utlöses som en del av de tillämpande åtgärderna ser klienter endast meddelanden för lokalt genomförda åtgärder. Och eftersom åtgärder garanteras endast vara lokalt engagerade (med andra ord loggade), kan de ångras eller inte i framtiden.
* På omriseringssökvägen utlöses ett enda meddelande för varje tillämpad åtgärd. Det innebär att om transaktion T1 innehåller Create(X), Delete(X) och Create(X) får du ett meddelande om att skapa X, en för borttagningen och en för att skapa igen, i den ordningen.
* För transaktioner som innehåller flera operationer tillämpas åtgärder i den ordning som de togs emot på den primära repliken från användaren.
* Som en del av bearbetningen av felaktiga förlopp kan vissa åtgärder ångras. Meddelanden utlöses för sådana ångra-åtgärder, vilket rullar tillbaka replikens tillstånd till en stabil punkt. En viktig skillnad med ångra-meddelanden är att händelser med dubblettnycklar aggregeras. Om transaktion T1 till exempel ångras visas ett enda meddelande till Delete(X).

## <a name="next-steps"></a>Nästa steg
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Snabbstart för Tillförlitliga tjänster](service-fabric-reliable-services-quick-start.md)
* [Reliable Services säkerhetskopiering och återställning (haveriberedskap)](service-fabric-reliable-services-backup-restore.md)
* [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

