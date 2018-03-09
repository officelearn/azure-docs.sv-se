---
title: "Översikt över aktören-baserad Azure mikrotjänster livscykel | Microsoft Docs"
description: "Beskriver Service Fabric tillförlitliga aktören livscykel, skräpinsamling och manuellt ta bort aktörer och deras tillstånd"
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: dd45acd75e1cf263029c869d88c87b28f56d50cc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Aktören livscykel, automatisk skräpinsamling och manuellt ta bort
En aktör aktiveras första gången ett anrop görs till någon av dess metoder. En aktör är inaktiverad (skräp som samlats in av aktörer runtime) om den inte används för en konfigurerbar tidsperiod. En aktör och dess tillstånd kan också tas bort manuellt när som helst.

## <a name="actor-activation"></a>Aktören aktivering
När en aktör aktiveras inträffar följande:

* När ett samtal kommer för en aktör och ingen sådan redan är aktiv, skapas en ny aktören.
* Den aktörstillstånd har lästs in om den underhåller tillstånd.
* Den `OnActivateAsync` (C#) eller `onActivateAsync` (Java)-metoden (som kan åsidosättas i aktören implementeringen) anropas.
* Aktören anses nu aktiv.

## <a name="actor-deactivation"></a>Aktören avaktivering
När en aktör inaktiveras inträffar följande:

* När en aktör inte används för vissa tidsperiod, bort den från tabellen Active aktörer.
* Den `OnDeactivateAsync` (C#) eller `onDeactivateAsync` (Java)-metoden (som kan åsidosättas i aktören implementeringen) anropas. Rensar alla timers för aktören. Aktören åtgärder som tillstånd ändringar inte ska anropas från den här metoden.

> [!TIP]
> Fabric aktörer runtime skickar vissa [händelser relaterade till aktören aktivering och inaktivering av](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). De är användbara i diagnostik- och prestandaövervakning.
>
>

### <a name="actor-garbage-collection"></a>Aktören skräpinsamling
När en aktör inaktiveras referenser till objektet aktören släpps och det kan vara skräpinsamlats normalt av CLR (CLR) eller java virtual machine (JVM) skräpinsamlingen. Skräpinsamling endast rensar aktören-objektet. Det gör **inte** ta bort statusen som lagras i aktören tillstånd Manager. Nästa gång aktören aktiveras aktören objekt skapas och dess tillstånd har återställts.

Vad räknas som ”används” för inaktivering och skräpinsamling?

* Ta emot ett samtal
* `IRemindable.ReceiveReminderAsync` metoden anropas (gäller endast om aktören använder påminnelser)

> [!NOTE]
> om aktören använder timers och dess timer-återanropet anropas, sker **inte** antal som ”används”.
>
>

Innan vi gå in information om inaktiveringen är det viktigt att definiera följande villkor:

* *Skanna intervall*. Detta är det intervall då aktörer runtime söker igenom Active aktörer tabellen aktörer kan inaktiveras och skräpinsamlats. Standardvärdet för det här är 1 minut.
* *Inaktivitetstid*. Det här är tidsperiod som en aktör behöver förblir oanvänt (inaktiv) innan den kan inaktiveras och skräpinsamlats. Standardvärdet för det här är 60 minuter.

Vanligtvis behöver du inte ändra standardinställningarna. Men om det behövs dessa intervall kan ändras via `ActorServiceSettings` när du registrerar din [aktören Service](service-fabric-reliable-actors-platform.md):

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
Aktören runtime håller reda på hur lång tid som den har varit inaktiv (dvs. inte används) för varje active aktören. Aktören runtime kontrollerar var och en av berörda varje `ScanIntervalInSeconds` att se om det kan vara skräp samlas in och samlar in om det har varit inaktiv i `IdleTimeoutInSeconds`.

Varje gång som en aktör används återställs dess inaktivitetstid till 0. Sedan kan aktören kan vara skräpinsamlats endast om den igen är vilande för `IdleTimeoutInSeconds`. Kom ihåg att en aktör betraktas som används om en gränssnittsmetod aktören eller ett aktören påminnelse motanrop körs. En aktör är **inte** betraktas som används om dess timer motanrop körs.

Följande diagram visar livscykeln för ett enda aktören att illustrera dessa begrepp.

![Exempel på inaktivitetstid][1]

Exemplet visar effekten av aktören metodanrop, påminnelser och timers på den här aktören livstid. Följande punkter om exemplet är värt att nämna:

* ScanInterval och IdleTimeout ställs till 5 och 10. (Enheter inte roll här, eftersom exemplet är bara för att illustrera begreppen.)
* Genomsökningen efter aktörer på att skräpinsamlas sker på T = 0, 5, 10, 15, 20, 25, som definieras i intervallet för sökning på 5.
* En periodisk timer utlöses vid T = 4, 8, 12, 16, 20, 24, och dess motanrop körs. Inaktivitetstid aktören påverkas inte.
* En aktören metodanrop på T = 7 återställs den inaktiva tiden till 0 och försenar skräpinsamling aktören.
* Ett återanrop för aktören påminnelse körs på T = 14 och ytterligare fördröjningar skräpinsamling aktören.
* Under skräp samling avsökningen T = 25 skådespelare, inaktivitetstid slutligen överskrider tidsgränsen för inaktivitet 10 och aktören samlas in som skräp.

En aktör blir aldrig skräpinsamlats medan det körs en av dess metoder, oavsett hur lång tid det tar vid körning av den här metoden. Som tidigare nämnts förhindrar körning av aktören gränssnittsmetoder och påminnelse återanrop skräpinsamling genom att återställa den aktören inaktivitetstid till 0. Körningen av timer återanrop återställs inte den inaktiva tiden till 0. Skräpinsamling aktören skjuts tills timer-återanrop har slutförts.

## <a name="deleting-actors-and-their-state"></a>Ta bort aktörer och deras tillstånd
Skräpinsamling inaktiverade aktörer endast rensar aktören objektet, men det tar inte bort data som lagras i en aktör tillstånd Manager. När en aktör aktiveras igen, få dess data igen ska tillgång till den via hanteraren tillstånd. I fall där aktörer lagra data i Tillståndshanterare och är inaktiverad men aldrig aktiverats igen, kan det vara nödvändigt att rensa sina data.

Den [aktören Service](service-fabric-reliable-actors-platform.md) innehåller en funktion för att ta bort aktörer från en fjärransluten anropare:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Om du tar bort en aktör ger följande effekter beroende på om huruvida aktören är för närvarande är aktiva:

* **Aktiva aktören**
  * Aktören tas bort från listan med aktiva aktörer och inaktiveras.
  * Dess tillstånd tas bort permanent.
* **Inaktiva aktören**
  * Dess tillstånd tas bort permanent.

Observera att det går inte att anropa en aktör ta bort på sig själv från någon av dess metoder aktören eftersom aktören inte kan tas bort körs inom en aktören anrop-kontext där körningsmiljön har fått ett lås runt aktören anropet att tillämpa Enkeltrådig åtkomst.

## <a name="next-steps"></a>Nästa steg
* [Aktören timers och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
* [Aktören händelser](service-fabric-reliable-actors-events.md)
* [Aktören återinträde](service-fabric-reliable-actors-reentrancy.md)
* [Aktören diagnostik- och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md)
* [Aktören API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-kodexempel](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
