---
title: Återinträde i Azure Service Fabric actors | Microsoft Docs
description: Introduktion till återinträde för Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: be23464a-0eea-4eca-ae5a-2e1b650d365e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46682787bac2d60d188384a4078ca2fa1f46ae7a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669035"
---
# <a name="reliable-actors-reentrancy"></a>Återinträde av Reliable Actors
Reliable Actors-runtime tillåter som standard logiska anrop kontext-baserade återinträde. Det möjliggör aktörer vara fleraktivt om de finns i samma anropskedja för kontexten. Till exempel skickar aktör A ett meddelande till aktören B, som skickar ett meddelande till aktören C. Som en del av meddelandebehandling, om aktören C anropar aktören A, är meddelandet fleraktivt, så kommer att tillåtas. Andra meddelanden som ingår i ett annat sammanhang blockeras på aktören A tills processen avslutas.

Det finns två alternativ för aktören återinträde som definierats i den `ActorReentrancyMode` enum:

* `LogicalCallContext` (standardinställning)
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
Återinträde kan konfigureras i en `ActorService`'s inställningar under registreringen. Inställningen gäller för alla aktörsinstanser som skapats i aktörstjänsten.

I följande exempel visas en actor-tjänst som anger återinträde-läge till `ActorReentrancyMode.Disallowed`. I det här fallet, om en aktör skickar ett fleraktivt meddelande till en annan aktören, ett undantag av typen `FabricException` utlöses.

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
