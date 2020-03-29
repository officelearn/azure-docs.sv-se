---
title: Reliable Actors anteckningar om skådespelare typ serialisering
description: I artikeln beskrivs grundläggande krav för att definiera serialisable-klasser som kan användas för att definiera tillstånd och gränssnitt för service fabric reliable actors
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 876c4f5f45ff6c81a53274cf32e8bebecc1acfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349312"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Anteckningar om Service Fabric Reliable Actors typ serialisering
Argumenten för alla metoder, resultattyper för de uppgifter som returneras av varje metod i ett aktörsgränssnitt och objekt som lagras i en aktörs tillståndshanterare måste vara [datakontrakt serialiserbara](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Detta gäller även argumenten för de metoder som definierats i [aktörshändelsegränssnitt](service-fabric-reliable-actors-events.md). (Metoder för händelsegränssnitt för aktör returnerar alltid ogiltiga.)

## <a name="custom-data-types"></a>Anpassade datatyper
I det här exemplet definierar följande aktörsgränssnitt en `VoicemailBox`metod som returnerar en anpassad datatyp som kallas :

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

Gränssnittet implementeras av en aktör som använder tillståndshanteraren för att lagra ett `VoicemailBox` objekt:

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

I det här `VoicemailBox` exemplet serialiseras objektet när:

* Objektet överförs mellan en aktörsinstans och en anropare.
* Objektet sparas i tillståndshanteraren där det sparas på disken och replikeras till andra noder.

Reliable Actor-ramverket använder Serialisering av datakontrakt. Därför måste anpassade dataobjekt och deras medlemmar kommenteras med attributen **DataContract** respektive **DataMember.**

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
* [Skådespelarens livscykel och skräpinsamling](service-fabric-reliable-actors-lifecycle.md)
* [Aktörs timers och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
* [Skådespelare händelser](service-fabric-reliable-actors-events.md)
* [Skådespelare reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Skådespelare polymorfism och objektorienterade designmönster](service-fabric-reliable-actors-polymorphism.md)
* [Skådespelare diagnostik och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md)
