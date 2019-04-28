---
title: Översikt över livscykeln för Azure Service Fabric-aktör | Microsoft Docs
description: Förklarar Service Fabric tillförlitliga aktörer livscykel, skräpinsamling och manuellt ta bort aktörer och deras tillstånd
services: service-fabric
documentationcenter: .net
author: amanbha
manager: chackdan
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: f81fde441a2f0dc2504601f82e5b890eb6e216de
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105300"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Aktören livscykel, skräpinsamling för automatisk och manuell delete
En aktör aktiveras första gången ett anrop görs till någon av dess metoder. En aktör är inaktiverad (skräpinsamling som samlats in av aktörer runtime) om den inte används för en konfigurerbar tidsperiod. En aktör och dess tillstånd kan också tas bort manuellt när som helst.

## <a name="actor-activation"></a>Aktivering av aktör
När en aktör är aktiverat inträffar följande:

* När ett anrop kommer för en aktör och ingen sådan redan är aktiv, skapas en ny aktör.
* Den aktörstillstånd har lästs in om upprätthållande av tillstånd.
* Den `OnActivateAsync` (C#) eller `onActivateAsync` (Java)-metoden (som kan åsidosättas i aktörsimplementeringen) anropas.
* Aktören anses nu vara aktiv.

## <a name="actor-deactivation"></a>Inaktivering av aktör
När en aktör inaktiveras, inträffar följande:

* När en aktör inte används för en viss tidsperiod, den tas bort från tabellen Active aktörer.
* Den `OnDeactivateAsync` (C#) eller `onDeactivateAsync` (Java)-metoden (som kan åsidosättas i aktörsimplementeringen) anropas. Detta tar bort alla timers för aktören. Aktören åtgärder som tillstånd ändringar inte ska anropas från den här metoden.

> [!TIP]
> Fabric Actors runtime genererar några [händelser relaterade till aktören aktivering och inaktivering](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). De är användbara i diagnostik och övervakning av programprestanda.
>
>

### <a name="actor-garbage-collection"></a>Aktören skräpinsamling
När en aktör inaktiveras referenser till objektet aktören släpps och det kan vara skräpinsamlats normalt av CLR (CLR) eller skräpinsamlare för java virtual machine (JVM). Skräpinsamling rensar endast aktör-objektet. den gör **inte** ta bort tillstånd som lagras på aktören State Manager. Nästa gång aktören har aktiverats kan en ny aktörobjekt skapas och dess tillstånd har återställts.

Vad räknas som ”används” i syfte att inaktivering och skräpinsamling?

* Ta emot ett samtal
* `IRemindable.ReceiveReminderAsync` metoden anropas (gäller endast om aktören använder påminnelser)

> [!NOTE]
> om aktören använder timers och dess motanrop timern har anropats, sker **inte** räknas som ”används”.
>
>

Innan vi går in på detaljnivå av inaktivering är det viktigt att definiera följande villkor:

* *Sökintervall*. Detta är intervallet då aktörer runtime söks igenom sin Active aktörer tabell aktörer som kan inaktivera dem och skräpinsamling som samlas in. Standardvärdet för det här är 1 minut.
* *Timeout vid inaktivitet*. Detta är hur lång tid som krävs för en aktör förblir oanvänt (inaktiv) innan den kan inaktiveras och skräpinsamling som samlas in. Standardvärdet för det här är 60 minuter.

Vanligtvis behöver du inte ändra dessa standardinställningar. Men om det behövs kan dessa intervall kan ändras via `ActorServiceSettings` när du registrerar din [Aktörstjänsten](service-fabric-reliable-actors-platform.md):

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
Actor runtime håller reda på hur lång tid som den har varit inaktiv (dvs. inte används) för varje active skådespelare. Actor runtime kontrollerar aktörer varje `ScanIntervalInSeconds` att se om det kan vara skräpinsamling samlas in och samlar in om det har varit inaktiv i `IdleTimeoutInSeconds`.

När du använder en aktör, återställs dess inaktivitetstid till 0. Därefter kan aktören kan skräpinsamlas endast om den igen har varit inaktiv för `IdleTimeoutInSeconds`. Kom ihåg att en aktör anses ha använts om gränssnittsmetoden en aktör eller ett aktören påminnelse motanrop körs. En aktör är **inte** anses ha använts om dess motanrop timer körs.

Följande diagram visar livscykeln för en enda aktör som illustrerar dessa begrepp.

![Exempel på hur lång tids inaktivitet][1]

Exemplet visar effekten av aktör metodanrop påminnelser och timers på livslängden för den här aktör. Följande saker övervägas om exemplet är värda att nämna:

* ScanInterval och IdleTimeout ställs till 5 och 10. (Enheter inte roll här, eftersom vårt syfte är endast för att illustrera konceptet.)
* Sökningen efter aktörer att skräpinsamlas sker på T = 0, 5, 10, 15, 20, 25, som definieras av genomsökningsintervall på 5.
* En regelbunden utlöses vid T = 4, 8, 12, 16, 20, 24, och dess motanrop körs. Det påverkar inte inaktivitetstiden för aktören.
* En aktör metodanrop på T = 7 återställer inaktivitetstiden till 0 och fördröjer skräpinsamling för aktören.
* Ett återanrop för aktören påminnelse körs klockan T = 14 och ytterligare fördröjer skräpinsamling för aktören.
* Inaktiv tid för den aktörens slutligen överskrider tidsgränsen för inaktivitet på 10 under skräpinsamling samling genomsökningen T = 25 och aktören samlas in som skräp.

En aktör blir aldrig skräpinsamlats medan det körs en av dess metoder, oavsett hur mycket tid på vid körning av den metoden. Såsom nämnts tidigare förhindrar körning av aktörsgränssnittsmetoder och påminnelse återanrop skräpinsamling genom att återställa den aktör inaktivitetstid till 0. Körningen av återanrop timern återställs inte hur lång tids inaktivitet till 0. Skräpinsamling för aktören är dock skjutas upp tills timer-återanrop har slutförts.

## <a name="manually-deleting-actors-and-their-state"></a>Ta bort aktörer och deras tillstånd manuellt
Skräpinsamling för inaktiverad aktörer rensar endast aktörobjekt, men tas inte bort data som lagras i en aktör State Manager. När en aktör aktiveras igen, få dess data igen tillgång till den via State Manager. I fall där aktörer lagra data i State Manager och är inaktiverad men aldrig aktiverats igen, kan det vara nödvändigt att rensa sina data.  Exempel på hur du tar bort aktörer läsa [ta bort aktörer och deras tillstånd](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Nästa steg
* [Aktören timers och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
* [Aktören händelser](service-fabric-reliable-actors-events.md)
* [Återinträde av aktör](service-fabric-reliable-actors-reentrancy.md)
* [Aktören diagnostik och övervakning av programprestanda](service-fabric-reliable-actors-diagnostics.md)
* [Aktören API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkoden](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
