---
title: Reliable Actors Anmärkningar om aktören serialisering av aktörstyp | Microsoft Docs
description: Beskriver grundläggande krav för att definiera serialiserbara klasser som kan användas för att definiera Service Fabric Reliable Actors tillstånd och gränssnitt
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c8eeeb0ade6ca002adf3211cbf49127be9b76edb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667523"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Anmärkningar om Service Fabric Reliable Actors serialisering av aktörstyp
Argumentet för alla metoder, resultattyper uppgifter som returneras av varje metod i en aktörsgränssnittet och objekt som lagras i en aktör tillståndshanterare måste vara [data kontrakt serialiserbara](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Detta gäller även för argumentet för metoderna i [händelse aktörsgränssnitt](service-fabric-reliable-actors-events.md). (Händelse aktörsgränssnittsmetoder alltid returnerar void.)

## <a name="custom-data-types"></a>Anpassade datatyper
I det här exemplet definierar följande aktörsgränssnittet en metod som returnerar en anpassad datatyp som kallas `VoicemailBox`:

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

Gränssnittet implementeras av en aktör som använder Hanteraren för tillstånd för att lagra en `VoicemailBox` objekt:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

I det här exemplet på `VoicemailBox` serialisera objektet när:

* Objektet överförs mellan en aktör-instans och en anropare.
* Objektet har sparats i hanteraren för tillstånd där den sparas till disk och replikeras till andra noder.

I Reliable Actor-ramverk används DataContract serialisering. Därför anpassade data-objekt och deras medlemmar får kommenteras med den **DataContract** och **DataMember** respektive attribut.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Nästa steg
* [Aktör livscykel och skräpinsamling samling](service-fabric-reliable-actors-lifecycle.md)
* [Aktören timers och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
* [Aktören händelser](service-fabric-reliable-actors-events.md)
* [Återinträde av aktör](service-fabric-reliable-actors-reentrancy.md)
* [Aktören polymorfism och objektorienterad designmönster](service-fabric-reliable-actors-polymorphism.md)
* [Aktören diagnostik och övervakning av programprestanda](service-fabric-reliable-actors-diagnostics.md)
