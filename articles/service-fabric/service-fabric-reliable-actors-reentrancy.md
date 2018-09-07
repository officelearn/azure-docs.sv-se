---
title: Återinträde i Azure Service Fabric actors | Microsoft Docs
description: Introduktion till återinträde för Service Fabric Reliable Actors.
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
ms.openlocfilehash: c7a4066a949ad6e66c45dff67f1e80801f2fa4cd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055268"
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
