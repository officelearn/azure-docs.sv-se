---
title: Reentrancy i Azure Service Fabric-aktörer
description: Introduktion till reentrancy för Service Fabric Reliable Actors, ett sätt att logiskt undvika blockering baserat på samtalskontext.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645539"
---
# <a name="reliable-actors-reentrancy"></a>Pålitliga aktörer reentrancy
Körningen Tillförlitliga aktörer tillåter som standard logiska samtalskontextbaserad reentrancy. Detta gör det möjligt för aktörer att återuppstätt om de är i samma samtal sammanhang kedja. Aktör A skickar till exempel ett meddelande till aktör B, som skickar ett meddelande till aktör C. Som en del av meddelandebearbetningen, om aktör C anropar aktör A, är meddelandet återansmätet, så det kommer att tillåtas. Alla andra meddelanden som ingår i en annan anropskontext blockeras på aktör A tills bearbetningen är klar.

Det finns två alternativ för aktörsreentrancy som definieras i uppräkningen: `ActorReentrancyMode`

* `LogicalCallContext`(standardbeteende)
* `Disallowed`- inaktiverar reentrancy

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
Reentrancy kan konfigureras `ActorService`i en ' s inställningar under registreringen. Inställningen gäller för alla aktörsinstanser som skapats i aktörstjänsten.

I följande exempel visas en aktörstjänst som anger `ActorReentrancyMode.Disallowed`reentrancy-läget till . I det här fallet, om en aktör skickar ett återuppmätande meddelande till en annan aktör, kommer ett undantag av typen `FabricException` att kastas.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Nästa steg
* Läs mer om reentrancy i [aktörs-API-referensdokumentationen](https://msdn.microsoft.com/library/azure/dn971626.aspx)
