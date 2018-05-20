---
title: Återinträde i aktören-baserad Azure mikrotjänster | Microsoft Docs
description: Introduktion till återinträde för Service Fabric Reliable Actors
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: be23464a-0eea-4eca-ae5a-2e1b650d365e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 40f52cb399f2d7391657ce4356a0c30921d46e5f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-actors-reentrancy"></a>Tillförlitliga aktörer återinträde
Körningsmiljön Reliable Actors tillåter som standard logiska anropet kontext-baserade återinträde. Detta ger aktörer ska fleraktivt om de finns i samma kedja för anrop kontext. Till exempel skickar aktören A ett meddelande till aktören B, som skickar ett meddelande till aktören C. Som en del av meddelandebehandling, om aktören C anropar aktören A och är meddelandet fleraktivt, så ska tillåtas. Andra meddelanden som ingår i ett annat sammanhang blockeras på aktören A tills bearbetningen har slutförts.

Det finns två alternativ för aktören återinträde som definierats i den `ActorReentrancyMode` uppräkningen:

* `LogicalCallContext` (standardinställningen)
* `Disallowed` -inaktiverar återinträde

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
Återinträde kan konfigureras i en `ActorService`'s inställningar under registreringen. Inställningen gäller för alla instanser av aktören som skapats i tjänsten aktören.

I följande exempel visas en aktören-tjänst som anger återinträde läge till `ActorReentrancyMode.Disallowed`. I det här fallet, om en aktör skickar ett fleraktivt meddelande till en annan aktören, ett undantag av typen `FabricException` genereras.

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
* Mer information om återinträde i den [aktören API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
