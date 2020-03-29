---
title: Reliable Actors timers och påminnelser
description: Introduktion till timers och påminnelser för Service Fabric Reliable Actors, inklusive vägledning om när du ska använda varje.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 02d6220b31ee9c991e8450759bf46759af6177a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639623"
---
# <a name="actor-timers-and-reminders"></a>Aktörs timers och påminnelser
Aktörer kan schemalägga periodiskt arbete på sig själva genom att registrera antingen timers eller påminnelser. Den här artikeln visar hur du använder timers och påminnelser och förklarar skillnaderna mellan dem.

## <a name="actor-timers"></a>Aktör timers
Aktörstimers ger ett enkelt omslag runt en .NET- eller Java-timer för att säkerställa att motringningsmetoderna respekterar den turbaserade samtidighet som garanterar att Actors-körningen ger.

Aktörer kan `RegisterTimer`använda (C#) eller `registerTimer` `UnregisterTimer`(Java) och `unregisterTimer`(C#) eller (Java) metoder på sin basklass för att registrera och avregistrera sina timers. Exemplet nedan visar användningen av timer-API:er. API:erna är mycket lika .NET-timern eller Java-timern. I det här exemplet, när timern förfaller, `MoveObject`anropar Actors `moveObject`runtime metoden (C#) eller (Java). Metoden är garanterad att respektera den turbaserade samtidigheten. Det innebär att inga andra aktörsmetoder eller timer-/påminnelse-motringningar pågår förrän den här motringningen har slutförts.

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

Nästa period av timern startar när motringningen har slutfört körningen. Detta innebär att timern stoppas medan motringningen körs och startas när motringningen är klar.

Körningen Aktörer sparar ändringar som gjorts i aktörens statligt hanterare när motringningen är klar. Om ett fel uppstår när det gäller att spara tillståndet inaktiveras aktörsobjektet och en ny instans aktiveras.

Alla timers stoppas när aktören inaktiveras som en del av skräpinsamlingen. Inga timer-motringningar anropas efter det. Dessutom behåller Actors-körningen inte någon information om de timers som kördes före inaktivering. Det är upp till aktören att registrera alla timers som den behöver när den återaktiveras i framtiden. Mer information finns i avsnittet om [skräpinsamling](service-fabric-reliable-actors-lifecycle.md)för skådespelare .

## <a name="actor-reminders"></a>Skådespelare påminnelser
Påminnelser är en mekanism för att utlösa beständiga motringningar på en aktör vid angivna tidpunkter. Deras funktionalitet liknar timers. Men till skillnad från timers utlöses påminnelser under alla omständigheter tills aktören uttryckligen avregistrerar dem eller aktören uttryckligen tas bort. Påminnelser utlöses särskilt över aktörsavaktiveringar och redundans eftersom actors-körningen innehåller information om aktörens påminnelser med aktörstillståndsprovidern. Observera att tillförlitligheten hos påminnelser är knuten till de tillståndssäkerhetsgarantier som tillhandahålls av aktörsstatens leverantör. Detta innebär att för aktörer vars stat uthållighet är inställd på Ingen, påminnelser kommer inte eld efter en redundans. 

Om du vill registrera en `RegisterReminderAsync` påminnelse anropar en aktör den metod som anges i basklassen, vilket visas i följande exempel:

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

I det `"Pay cell phone bill"` här exemplet är påminnelsens namn. Det här är en sträng som aktören använder för att unikt identifiera en påminnelse. `BitConverter.GetBytes(amountInDollars)`(C#) är det sammanhang som är associerat med påminnelsen. Det kommer att skickas tillbaka till skådespelaren som ett argument till `IRemindable.ReceiveReminderAsync`påminnelsen motringning, dvs (C #) eller `Remindable.receiveReminderAsync`(Java).

Aktörer som använder påminnelser `IRemindable` måste implementera gränssnittet, som visas i exemplet nedan.

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

När en påminnelse utlöses anropar körningen Tillförlitliga `ReceiveReminderAsync`aktörer (C#) eller `receiveReminderAsync`(Java) på aktören. En aktör kan registrera flera `ReceiveReminderAsync`påminnelser och `receiveReminderAsync`(C#) eller (Java) anropas när någon av dessa påminnelser utlöses. Aktören kan använda påminnelsenamnet som skickas `ReceiveReminderAsync`in till `receiveReminderAsync`metoden (C#) eller (Java) för att ta reda på vilken påminnelse som utlöstes.

Den Aktörer runtime sparar skådespelarens tillstånd `ReceiveReminderAsync`när (C#) eller `receiveReminderAsync`(Java) samtalet avslutas. Om ett fel uppstår när det gäller att spara tillståndet inaktiveras aktörsobjektet och en ny instans aktiveras.

Om du vill avregistrera en `UnregisterReminderAsync`påminnelse anropar `unregisterReminderAsync`en aktör metoden (C#) eller (Java), som visas i exemplen nedan.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Som visas ovan `UnregisterReminderAsync`accepterar (C#) eller `unregisterReminderAsync`(Java) -metoden ett `IActorReminder`(C#) eller `ActorReminder`(Java)-gränssnitt. Aktörsbasklassen `GetReminder`stöder en (C#) eller `getReminder`(Java)-metod `IActorReminder`som kan `ActorReminder`användas för att hämta (C#) eller (Java) genom att skicka in påminnelsenamnet. Detta är praktiskt eftersom aktören `IActorReminder`inte behöver behålla `ActorReminder`(C#) eller (Java) gränssnitt som returnerades från `RegisterReminder`(C #) eller `registerReminder`(Java) metodanrop.

## <a name="next-steps"></a>Efterföljande moment
Läs mer om tillförlitliga skådespelare händelser och reentrancy:
* [Skådespelare händelser](service-fabric-reliable-actors-events.md)
* [Skådespelare reentrancy](service-fabric-reliable-actors-reentrancy.md)
