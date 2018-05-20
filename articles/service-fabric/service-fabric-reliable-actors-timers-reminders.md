---
title: Tillförlitliga aktörer timers och påminnelser | Microsoft Docs
description: Introduktion till timers och påminnelser för Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: e43aec6630a4a688ffd6c52a5e5bd711243fa662
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="actor-timers-and-reminders"></a>Aktören timers och påminnelser
Aktörer kan schemalägga regelbunden arbete på själva genom att registrera timers eller påminnelser. Den här artikeln visar hur du använder timers och påminnelser och förklarar skillnaderna mellan dem.

## <a name="actor-timers"></a>Aktören timers
Aktören timers ger en enkel omslutning runt en .NET eller Java timer så att callback-metoderna respekterar samtidighet bygger garanterar att aktörer runtime tillhandahåller.

Aktörer kan använda den `RegisterTimer`(C#) eller `registerTimer`(Java) och `UnregisterTimer`(C#) eller `unregisterTimer`(Java)-metoder på basklass för att registrera och avregistrera sina timers. Exemplet nedan visar hur du använder timer-API: er. API: erna liknar .NET timer eller Java-timern. I det här exemplet när timern förfaller aktörer runtime anropar den `MoveObject`(C#) eller `moveObject`(Java)-metoden. Metoden är säkert att respektera bygger samtidighet. Detta innebär att inga andra aktören metoder eller timer/påminnelse återanrop pågår förrän den här återanrop körning.

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

Timern nästa period startar när återanropet är slutfört. Detta innebär att timern har stoppats medan återanropet körs och är igång när återanropet är klar.

Aktörer runtime sparar ändringar som gjorts i aktören Tillståndshanterare när återanropet är klar. Om ett fel uppstår i Spara tillståndet, aktören objektet inaktiveras och en ny instans aktiveras.

Alla stoppas när aktören inaktiveras som en del av skräpinsamling. Inga timer-återanrop anropas efter. Aktörer runtime behåller dessutom inte någon information om timers som kördes före inaktivering. Det är aktören att registrera alla timers som krävs när den aktiveras i framtiden. Mer information finns i avsnittet på [aktören skräpinsamling](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Aktören påminnelser
Påminnelser är en mekanism för att utlösa beständiga återanrop på en aktör vid angivna tidpunkter. Deras funktioner liknar timers. Men till skillnad från timers, påminnelser utlöses i alla fall tills aktören Avregistrerar dem explicit eller aktören uttryckligen tas bort. Mer specifikt utlöses påminnelser över aktören avaktiveringar och växling vid fel eftersom aktörer runtime kvarstår information om den aktören påminnelser med aktören tillståndsprovidern. Observera att tillförlitligheten hos påminnelser är knutna till tillförlitlighet garantier tillstånd som tillståndsprovidern aktören. Det innebär att för aktörer vars beständighet är inställd på None, påminnelser inte kommer att utlösa en efter en redundansväxling. 

Om du vill registrera en påminnelse om en aktör anropar den `RegisterReminderAsync` metoden som angetts i basklassen, som visas i följande exempel:

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

I det här exemplet `"Pay cell phone bill"` är påminnelse namn. Detta är en sträng som aktören används för att unikt identifiera en påminnelse. `BitConverter.GetBytes(amountInDollars)`(C#) är den kontext som är associerad med påminnelsen. Den skickas tillbaka till aktören som ett argument till påminnelse motringning, d.v.s. `IRemindable.ReceiveReminderAsync`(C#) eller `Remindable.receiveReminderAsync`(Java).

Aktörer som använder påminnelser måste implementera den `IRemindable` gränssnitt som visas i exemplet nedan.

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

När en påminnelse utlöses körningsmiljön Reliable Actors ska anropa den `ReceiveReminderAsync`(C#) eller `receiveReminderAsync`(Java)-metoden på aktören. En aktören kan registrera flera påminnelser och `ReceiveReminderAsync`(C#) eller `receiveReminderAsync`(Java)-metoden anropas när någon av dessa påminnelser utlöses. Aktören kan använda påminnelse namn som skickas till den `ReceiveReminderAsync`(C#) eller `receiveReminderAsync`(Java)-metod för att ta reda på vilka påminnelse utlöstes.

Aktörer runtime sparar aktören tillstånd när den `ReceiveReminderAsync`(C#) eller `receiveReminderAsync`(Java)-anropet har slutförts. Om ett fel uppstår i Spara tillståndet, aktören objektet inaktiveras och en ny instans aktiveras.

Om du vill avregistrera en påminnelse om en aktör anropar den `UnregisterReminderAsync`(C#) eller `unregisterReminderAsync`(Java)-metoden, som visas i exemplen nedan.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Enligt ovan, den `UnregisterReminderAsync`(C#) eller `unregisterReminderAsync`(Java) metoden godkänner ett `IActorReminder`(C#) eller `ActorReminder`(Java) gränssnitt. Aktören basklass stöder en `GetReminder`(C#) eller `getReminder`(Java)-metod som används för att hämta den `IActorReminder`(C#) eller `ActorReminder`(Java)-gränssnittet genom att passera i påminnelse namn. Det är praktiskt eftersom aktören inte behöver bevara den `IActorReminder`(C#) eller `ActorReminder`(Java) gränssnitt som returnerades från den `RegisterReminder`(C#) eller `registerReminder`(Java)-anrop.

## <a name="next-steps"></a>Nästa steg
Mer information om tillförlitlig aktören händelser och återinträde:
* [Aktören händelser](service-fabric-reliable-actors-events.md)
* [Aktören återinträde](service-fabric-reliable-actors-reentrancy.md)
