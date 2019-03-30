---
title: Reliable Actors timers och påminnelser | Microsoft Docs
description: Introduktion till timers och påminnelser för Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 323de842645cced3c6f490e98112fcbcd184aa64
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667437"
---
# <a name="actor-timers-and-reminders"></a>Aktören timers och påminnelser
Aktörer kan schemalägga regelbunden arbete på själva genom att registrera timers och påminnelser. Den här artikeln visar hur du använder timers och påminnelser och förklarar skillnaderna mellan dem.

## <a name="actor-timers"></a>Timers aktör
Aktören timers tillhandahåller en enkel omslutning runt en .NET och Java timer för att säkerställa att callback-metoderna respekterar samtidighet tur-baserade garanterar att aktörer runtime tillhandahåller.

Aktörer kan använda den `RegisterTimer`(C#) eller `registerTimer`(Java) och `UnregisterTimer`(C#) eller `unregisterTimer`(Java) metoder på basklass för att registrera och avregistrera sina timers. Exemplet nedan visar användningen av timer API: er. API: erna är mycket lika .NET timer eller Java-timer. I det här exemplet när timern förfaller aktörer runtime anropar den `MoveObject`(C#) eller `moveObject`(Java)-metoden. Metoden är säkert att respektera tur-baserade samtidigheten. Detta innebär att ingen annan aktör metoder eller timer/påminnelse återanrop pågår förrän den här återanrop är slutfört.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

Nästa period av timern startar när är återanropet som slutfört. Detta innebär att timern har stoppats medan återanropet som körs och är igång när motringningen är klar.

Aktörer runtime sparar ändringar i aktören State Manager när motringningen är klar. Om ett fel uppstår i Spara tillståndet, aktören objektet inaktiveras och en ny instans aktiveras.

Alla stoppas när aktören inaktiveras som en del av skräpinsamling. Inga timer-återanrop anropas efter detta. Aktörer runtime behåller dessutom information om timers som kördes innan inaktivering. Det är upp till aktören för att registrera eventuella timers som den behöver, när den aktiveras i framtiden. Mer information finns i avsnittet på [aktören skräpinsamling](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Aktören påminnelser
Påminnelser är en mekanism för att utlösa beständiga återanrop på en aktör vid angivna tidpunkter. Deras funktioner liknar timers. Men till skillnad från timers, påminnelser utlöses i alla fall tills aktören Avregistrerar uttryckligen dem eller aktören uttryckligen tar bort. Mer specifikt utlöses påminnelser i aktören avaktiveringar och växling vid fel eftersom aktörer runtime kvarstår information om den aktör påminnelser med tillståndsprovider för aktören. Observera att tillförlitligheten för påminnelser är knutna till tillförlitlighet garantier tillstånd som tillståndsprovider aktör. Det innebär att för aktörer vars sessionslägets beständighet är inställt på Ingen, påminnelserna inte utlöses efter en redundansväxling. 

Registrera en påminnelse genom en aktör anropar den `RegisterReminderAsync` metod som tillhandahålls på basklassen, som visas i följande exempel:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

I det här exemplet `"Pay cell phone bill"` heter påminnelse. Det här är en sträng som aktören använder för att unikt identifiera en påminnelse. `BitConverter.GetBytes(amountInDollars)`(C#) är den kontext som är associerad med påminnelsen. Den skickas tillbaka till aktören som ett argument till påminnelse-motringning, dvs. `IRemindable.ReceiveReminderAsync`(C#) eller `Remindable.receiveReminderAsync`(Java).

Aktörer som använder påminnelser måste implementera de `IRemindable` gränssnitt som visas i exemplet nedan.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

När en påminnelse utlöses Reliable Actors-runtime anropar den `ReceiveReminderAsync`(C#) eller `receiveReminderAsync`(Java)-metod på aktören. En aktör kan registrera flera påminnelser och `ReceiveReminderAsync`(C#) eller `receiveReminderAsync`(Java)-metoden anropas när någon av dessa påminnelser utlöses. Aktören kan använda påminnelse namn som skickas till den `ReceiveReminderAsync`(C#) eller `receiveReminderAsync`(Java)-metod för att ta reda på vilka påminnelse utlöstes.

Aktörer runtime sparar aktören körningstillstånd när den `ReceiveReminderAsync`(C#) eller `receiveReminderAsync`(Java)-anropet har slutförts. Om ett fel uppstår i Spara tillståndet, aktören objektet inaktiveras och en ny instans aktiveras.

Om du vill avregistrera en påminnelse en aktör anropar den `UnregisterReminderAsync`(C#) eller `unregisterReminderAsync`(Java)-metod som du ser i exemplen nedan.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Enligt ovan, den `UnregisterReminderAsync`(C#) eller `unregisterReminderAsync`(Java)-metoden godkänner en `IActorReminder`(C#) eller `ActorReminder`(Java)-gränssnittet. Aktören basklass stöder en `GetReminder`(C#) eller `getReminder`(Java)-metod som kan användas för att hämta den `IActorReminder`(C#) eller `ActorReminder`(Java)-gränssnittet genom att ange namnet på påminnelse. Detta är praktiskt eftersom aktören inte behöver bevara den `IActorReminder`(C#) eller `ActorReminder`(Java)-gränssnitt som returnerades från den `RegisterReminder`(C#) eller `registerReminder`(Java)-anrop.

## <a name="next-steps"></a>Nästa steg
Läs mer om Reliable Actor-händelser och återinträde:
* [Aktören händelser](service-fabric-reliable-actors-events.md)
* [Återinträde av aktör](service-fabric-reliable-actors-reentrancy.md)
