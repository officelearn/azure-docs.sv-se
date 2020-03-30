---
title: Översikt över azure service fabric-aktörens livscykel
description: Förklarar livscykeln för en tillförlitlig aktör för serviceinfrastruktur, skräpinsamling och manuellt ta bort aktörer och deras tillstånd
author: amanbha
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: b05da78091260297d94062c06cba100d01ce7e2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258322"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Skådespelarens livscykel, automatisk skräpinsamling och manuell borttagning
En aktör aktiveras första gången ett samtal görs till någon av dess metoder. En aktör inaktiveras (skräp som samlas in av Actors-körningen) om den inte används för en konfigurerbar tidsperiod. En aktör och dess tillstånd kan också tas bort manuellt när som helst.

## <a name="actor-activation"></a>Aktivering av skådespelare
När en aktör aktiveras inträffar följande:

* När ett samtal kommer för en skådespelare och en inte redan är aktiv skapas en ny aktör.
* Aktörens tillstånd läses in om det upprätthåller tillståndet.
* Metoden `OnActivateAsync` (C#) `onActivateAsync` eller (Java) (som kan åsidosättas i aktörsimplementeringen) anropas.
* Skådespelaren anses nu vara aktiv.

## <a name="actor-deactivation"></a>Avaktivering av aktör
När en aktör inaktiveras inträffar följande:

* När en aktör inte används under en viss tid tas den bort från tabellen Aktiva aktörer.
* Metoden `OnDeactivateAsync` (C#) `onDeactivateAsync` eller (Java) (som kan åsidosättas i aktörsimplementeringen) anropas. Detta rensar alla timers för skådespelaren. Aktörsåtgärder som tillståndsändringar bör inte anropas från den här metoden.

> [!TIP]
> Fabric Actors runtime avger vissa [händelser relaterade till aktivering och avaktivering](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters)av aktören . De är användbara i diagnostik och prestandaövervakning.
>
>

### <a name="actor-garbage-collection"></a>Skådespelare sophämtning
När en aktör inaktiveras släpps referenser till aktörsobjektet och det kan vara skräp som samlas in normalt av den vanliga språkkörningen (CLR) eller java-sophämtaren (Virtual Machine). Skräpsamling rensar bara aktörsobjektet. Det tar **inte** bort tillstånd som lagras i aktörens state manager. Nästa gång aktören aktiveras skapas ett nytt aktörsobjekt och dess tillstånd återställs.

Vad räknas som "används" för avaktivering och sophämtning?

* Ta emot ett samtal
* `IRemindable.ReceiveReminderAsync`anropas (gäller endast om aktören använder påminnelser)

> [!NOTE]
> Om aktören använder timers och dess timer motringning anropas, räknas det **inte** som "används".
>
>

Innan vi går in på detaljer om avaktivering är det viktigt att definiera följande termer:

* *Skanningsintervall*. Det här är det intervall med vilket skådespelarna genomsöker tabellen Aktiva aktörer för aktörer som kan inaktiveras och skräp samlas in. Standardvärdet för detta är 1 minut.
* *Tidsgränsen för inaktiv.* Detta är den tid som en aktör måste förbli oanvänd (inaktiv) innan den kan inaktiveras och skräp samlas in. Standardvärdet för detta är 60 minuter.

Vanligtvis behöver du inte ändra dessa standardvärden. Om det behövs kan dessa intervall `ActorServiceSettings` dock ändras när du registrerar din [aktörstjänst:](service-fabric-reliable-actors-platform.md)

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
För varje aktiv aktör håller aktörens körning reda på hur lång tid den har varit inaktiv (dvs. inte används). Aktören runtime kontrollerar var `ScanIntervalInSeconds` och en av aktörerna var för att se om `IdleTimeoutInSeconds`det kan vara skräp samlas in och markerar det om det har varit inaktiv för .

När en aktör används återställs dess inaktiva tid till 0. Efter detta kan aktören sopor samlas in endast `IdleTimeoutInSeconds`om det återigen förblir inaktiv för . Kom ihåg att en aktör anses ha använts om antingen en aktörsgränssnittsmetod eller en aktörs påminnelse motringning körs. En aktör anses **inte** ha använts om dess timeradback körs.

Följande diagram visar livscykeln för en enskild aktör för att illustrera dessa begrepp.

![Exempel på inaktiv tid][1]

Exemplet visar effekten av aktörsmetodanrop, påminnelser och timers på den här aktörens livstid. Följande punkter om exemplet är värda att nämna:

* ScanInterval och IdleTimeout är inställda på 5 respektive 10. (Enheter spelar ingen roll här, eftersom vårt syfte är bara att illustrera konceptet.)
* Genomsökningen för aktörer som ska sopor samlas in sker på T = 0,5,10,15,20,25, enligt definitionen av skanningsintervallet 5.
* En periodisk timer avfyras vid T=4,8,12,16,20,24 och dess motringning körs. Det påverkar inte inaktiv tid för skådespelaren.
* Ett aktörsmetodanrop på T=7 återställer inaktiv tid till 0 och fördröjer aktörens skräpsamling.
* En aktör påminnelse motringning körs på T = 14 och ytterligare förseningar sopor insamling av aktören.
* Under sophämtningen på T=25 överskrider aktörens inaktiva tid slutligen den inaktiva tidsgränsen på 10 och skådespelaren är skräp som samlas in.

En aktör kommer aldrig att sopor samlas in medan den utför en av sina metoder, oavsett hur mycket tid som spenderas på att köra den metoden. Som tidigare nämnts förhindrar körningen av aktörsgränssnittsmetoder och påminnelseåterkallelser skräpinsamling genom att återställa aktörens inaktiva tid till 0. Körningen av timeradring återställs inte inaktiv tid till 0. Men skräpsamlingen för aktören skjuts upp tills timeradrering har slutfört körningen.

## <a name="manually-deleting-actors-and-their-state"></a>Manuellt ta bort aktörer och deras tillstånd
Skräpsamling av inaktiverade aktörer rensar bara aktörsobjektet, men data som lagras i en aktörs tillståndshanterare tas inte bort. När en aktör återaktiveras görs dess data återigen tillgängliga för den via tillståndshanteraren. I de fall där aktörer lagrar data i State Manager och inaktiveras men aldrig återaktiveras kan det vara nödvändigt att rensa sina data.  Exempel på hur du tar bort aktörer läser du [ta bort aktörer och deras tillstånd](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Nästa steg
* [Aktörs timers och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
* [Skådespelare händelser](service-fabric-reliable-actors-events.md)
* [Skådespelare reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Skådespelare diagnostik och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentation för aktörs-API-referens](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkod](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
