---
title: Reliable Actors timers och påminnelser
description: Introduktion till timers och påminnelser för Service Fabric Reliable Actors, inklusive vägledning om när du ska använda dem.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 02d6220b31ee9c991e8450759bf46759af6177a3
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639623"
---
# <a name="actor-timers-and-reminders"></a>Timers för aktör och påminnelser
Aktörer kan schemalägga periodiska arbeten genom att registrera antingen timers eller påminnelser. Den här artikeln visar hur du använder timers och påminnelser och förklarar skillnaderna mellan dem.

## <a name="actor-timers"></a>Aktörs tider
Aktörs timers tillhandahåller en enkel omslutning runt en .NET-eller Java-timer för att se till att callback-metoderna respekterar de omvända samtidighets garantier som kör körnings miljön för aktörerna.

Aktörer kan använda metoderna `RegisterTimer`(C#) eller `registerTimer`(Java) och `UnregisterTimer`(C#) eller `unregisterTimer`(Java) i sin basklass för att registrera och avregistrera sina timers. I exemplet nedan visas användningen av timer-API: er. API: erna liknar .NET timer eller Java timer. I det här exemplet kommer körnings miljön att anropa metoden `MoveObject`(C#) eller `moveObject`(Java) när timern förfaller. Metoden garanteras för att respektera den omvända samtidigheten. Det innebär att inga andra aktörs metoder eller återanrop av timer/påminnelser kommer att pågå tills återanropet har slutförts.

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

Nästa period i timern startar när återanropet har slutförts. Detta innebär att timern stoppas medan motringningen körs och startas när återanropet slutförs.

Aktörs körningen sparar ändringar som gjorts i aktörens tillstånds hanterare när återanropet slutförs. Om ett fel inträffar när du sparar status inaktive ras aktörens objekt och en ny instans aktive ras.

Alla timers stoppas när aktören inaktive ras som en del av skräp insamling. Inga timer-återanrop anropas efter det. Dessutom bevarar inte aktörernas körnings information om timers som kördes före inaktive ringen. Det är upp till aktören att registrera alla timers som behövs när den återaktiveras i framtiden. Mer information finns i avsnittet om [aktörens skräp insamling](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Aktörs påminnelser
Påminnelser är en mekanism för att utlösa beständiga återanrop på en aktör vid angivna tidpunkter. Deras funktioner liknar timers. Men till skillnad från timers utlöses påminnelser under alla omständigheter tills aktören uttryckligen avregistrerar dem eller aktören tas bort explicit. Mer specifikt utlöses påminnelser om inaktive ring av skådespelare och redundans, eftersom kör tid för aktörerna bevarar information om aktörens påminnelser med hjälp av aktörs leverantör. Observera att tillförlitligheten för påminnelser är knuten till de tillstånds Tillförlitlighets garantier som tillhandahålls av aktörens tillstånds leverantör. Det innebär att för aktörer vars beständighet har angetts till ingen, aktive ras påminnelser inte efter en redundansväxling. 

För att registrera en påminnelse, anropar en aktör `RegisterReminderAsync` metoden som anges i Bask Lassen, som du ser i följande exempel:

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

I det här exemplet är `"Pay cell phone bill"` påminnelse namnet. Det här är en sträng som aktören använder för att unikt identifiera en påminnelse. `BitConverter.GetBytes(amountInDollars)`(C#) är den kontext som är associerad med påminnelsen. Den skickas tillbaka till aktören som ett argument till återställnings återanropet, t. exC#. `IRemindable.ReceiveReminderAsync`() eller `Remindable.receiveReminderAsync`(Java).

Aktörer som använder påminnelser måste implementera `IRemindable` gränssnittet, som du ser i exemplet nedan.

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

När en påminnelse aktive ras anropar Reliable Actors runtime `ReceiveReminderAsync`(C#) eller `receiveReminderAsync`(Java)-metoden på aktören. En aktör kan registrera flera påminnelser och metoden `ReceiveReminderAsync`(C#) eller `receiveReminderAsync`(Java) anropas när någon av dessa påminnelser utlöses. Aktören kan använda påminnelse namnet som skickas till metoden `ReceiveReminderAsync`(C#) eller `receiveReminderAsync`(Java) för att ta reda på vilken påminnelse som har utlösts.

Aktörs körningen sparar aktörens tillstånd när anropet `ReceiveReminderAsync`C#() eller `receiveReminderAsync`(Java) har slutförts. Om ett fel inträffar när du sparar status inaktive ras aktörens objekt och en ny instans aktive ras.

Om du vill avregistrera en påminnelse anropar en aktör metodenC#`UnregisterReminderAsync`() eller `unregisterReminderAsync`(Java), som visas i exemplen nedan.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Som du ser ovan godkänner metoden `UnregisterReminderAsync`C#() eller `unregisterReminderAsync`(Java) ett `IActorReminder`(C#) eller `ActorReminder`(Java)-gränssnitt. Aktörs Bask Lassen har stöd förC#en `GetReminder`() eller `getReminder`(Java)-metod som kan användas för attC#Hämta `IActorReminder`() eller `ActorReminder`(Java)-gränssnittet genom att skicka in påminnelse namnet. Detta är användbart eftersom aktören inte behöver spara `IActorReminder`(C#) eller `ActorReminder`Java-gränssnittet som returnerades från `RegisterReminder`(C#) eller `registerReminder`(Java) metod anropet.

## <a name="next-steps"></a>Efterföljande moment
Lär dig mer om pålitliga aktörs händelser och återinträde:
* [Aktörs händelser](service-fabric-reliable-actors-events.md)
* [Aktör återinträde](service-fabric-reliable-actors-reentrancy.md)
