---
title: Återinträde i Azure Service Fabric-aktörer
description: Introduktion till återinträde för Service Fabric Reliable Actors, ett sätt att logiskt undvika blockering baserat på anrops kontext.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 100cf1f7bf8a0c903cfd61d93d2f923c32cabd11
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260945"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors återinträde
Reliable Actors runtime, som standard, tillåter Sammanhangs beroende återinträde för logiska anrop. Detta gör det möjligt för aktörer att bli omstrukturade om de befinner sig i samma anrops kontext kedja. Aktör A skickar exempelvis ett meddelande till skådespelare B, som skickar ett meddelande till skådespelaren C. Som en del av meddelande bearbetningen, om aktör C anropar aktör A, så är meddelandet omdeltagare, så det kommer att tillåtas. Alla andra meddelanden som ingår i en annan anrops kontext kommer att blockeras på aktör A tills bearbetningen är klar.

Det finns två tillgängliga alternativ för aktör återinträde som definierats i `ActorReentrancyMode` uppräkningen:

* `LogicalCallContext`(standard beteende)
* `Disallowed`-inaktiverar återinträde

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
Återinträde kan konfigureras i `ActorService` inställningarna under registreringen. Inställningen gäller för alla aktörs instanser som skapats i aktörs tjänsten.

I följande exempel visas en aktörs tjänst som anger återinträde-läget till `ActorReentrancyMode.Disallowed` . I det här fallet kommer ett undantag av typen att genereras om en aktör skickar ett reentrant-meddelande till en annan aktör `FabricException` .

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
* Läs mer om återinträde i [referens dokumentationen för aktörs-API](/previous-versions/azure/dn971626(v=azure.100))
