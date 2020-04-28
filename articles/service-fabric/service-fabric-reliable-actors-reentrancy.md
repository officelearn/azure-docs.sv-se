---
title: Återinträde i Azure Service Fabric-aktörer
description: Introduktion till återinträde för Service Fabric Reliable Actors, ett sätt att logiskt undvika blockering baserat på anrops kontext.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645539"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors återinträde
Reliable Actors runtime, som standard, tillåter Sammanhangs beroende återinträde för logiska anrop. Detta gör det möjligt för aktörer att bli omstrukturade om de befinner sig i samma anrops kontext kedja. Aktör A skickar exempelvis ett meddelande till skådespelare B, som skickar ett meddelande till skådespelaren C. Som en del av meddelande bearbetningen, om aktör C anropar aktör A, så är meddelandet omdeltagare, så det kommer att tillåtas. Alla andra meddelanden som ingår i en annan anrops kontext kommer att blockeras på aktör A tills bearbetningen är klar.

Det finns två tillgängliga alternativ för aktör återinträde som definierats `ActorReentrancyMode` i uppräkningen:

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
Återinträde kan konfigureras i `ActorService`inställningarna under registreringen. Inställningen gäller för alla aktörs instanser som skapats i aktörs tjänsten.

I följande exempel visas en aktörs tjänst som anger återinträde-läget `ActorReentrancyMode.Disallowed`till. I det här fallet kommer ett undantag av typen `FabricException` att genereras om en aktör skickar ett reentrant-meddelande till en annan aktör.

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
* Läs mer om återinträde i [referens dokumentationen för aktörs-API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
