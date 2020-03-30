---
title: Skapa en aktörsbaserad tjänst på Azure Service Fabric
description: Lär dig hur du skapar, felsöker och distribuerar din första aktörsbaserade tjänst i C# med hjälp av Service Fabric Reliable Actors.
author: vturecek
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: a6e4fb48653572139463738c82de632ff7d55074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466247"
---
# <a name="getting-started-with-reliable-actors"></a>Komma igång med Reliable Actors
> [!div class="op_single_selector"]
> * [C# på Windows](service-fabric-reliable-actors-get-started.md)
> * [Java i Linux](service-fabric-reliable-actors-get-started-java.md)

Den här artikeln går igenom att skapa och felsöka ett enkelt reliable actor-program i Visual Studio. Mer information om reliable actors finns i [Introduktion till Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Krav

Innan du börjar bör du se till att service fabric-utvecklingsmiljön, inklusive Visual Studio, har konfigurerats på din dator. Mer information finns i [hur du ställer in utvecklingsmiljön](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Skapa ett nytt projekt i Visual Studio

Starta Visual Studio 2019 eller senare som administratör och skapa sedan ett nytt **serviceprogramprogram:**

![Service Fabric-verktyg för Visual Studio - nytt projekt][1]

I nästa dialogruta väljer du **Aktörstjänst** under **.NET Core 2.0** och anger ett namn för tjänsten.

![Projektmallar för Service Fabric][5]

Det skapade projektet visar följande struktur:

![Projektstruktur för Service Fabric][2]

## <a name="examine-the-solution"></a>Undersök lösningen

Lösningen innehåller tre projekt:

* **Ansökningsprojektet (MyApplication)**. Det här projektet paketerar alla tjänster tillsammans för distribution. Den innehåller *ApplicationManifest.xml-* och PowerShell-skripten för att hantera programmet.

* **Gränssnittet projektet (HelloWorld.Interfaces)**. Det här projektet innehåller gränssnittsdefinitionen för aktören. Aktörsgränssnitt kan definieras i alla projekt med valfritt namn.  Gränssnittet definierar aktörskontraktet som delas av aktörsimplementeringen och klienterna som anropar aktören.  Eftersom klientprojekt kan vara beroende av det, är det vanligtvis meningsfullt att definiera det i en sammansättning som är skild från aktörsimplementeringen.

* **Aktören serviceprojekt (HelloWorld)**. Det här projektet definierar tjänsten Service Fabric som ska vara värd för aktören. Den innehåller genomförandet av skådespelaren, *HelloWorld.cs*. En aktörsimplementering är en klass `Actor` som härstammar från bastypen och implementerar de gränssnitt som definierats i projektet *MyActor.Interfaces.* En aktörsklass måste också implementera en `ActorService` konstruktör som accepterar en instans och en `ActorId` och skickar dem till basklassen. `Actor`
    
    Det här projektet innehåller också *Program.cs*, som registrerar aktörsklasser `ActorRuntime.RegisterActorAsync<T>()`med Service Fabric-körningen med . Klassen `HelloWorld` är redan registrerad. Eventuella ytterligare aktörsimplementeringar som läggs `Main()` till i projektet måste också registreras i metoden.

## <a name="customize-the-helloworld-actor"></a>Anpassa HelloWorld-skådespelaren

Projektmallen definierar vissa `IHelloWorld` metoder i gränssnittet och `HelloWorld` implementerar dem i aktörsimplementeringen.  Byt ut dessa metoder så att aktörstjänsten returnerar en enkel "Hello World"-sträng.

I *HelloWorld.Interfaces-projektet,* i *IHelloWorld.cs-filen,* ersätter gränssnittsdefinitionen enligt följande:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

I **HelloWorld-projektet,** **i HelloWorld.cs**, ersätter du hela klassdefinitionen enligt följande:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Tryck på **Ctrl-Shift-B** för att bygga projektet och se till att allt kompileras.

## <a name="add-a-client"></a>Lägga till en klient

Skapa ett enkelt konsolprogram för att anropa aktörstjänsten.

1. Högerklicka på lösningen i Solution Explorer > **Lägg till** > **nytt projekt...**.

2. Under **projecttyperna .NET Core** väljer du **Console App (.NET Core).**  Namnge projektet *ActorClient*.
    
    ![Dialogrutan Lägg till nytt projekt][6]    
    
    > [!NOTE]
    > Ett konsolprogram är inte den typ av app som du normalt använder som klient i Service Fabric, men det är ett praktiskt exempel för felsökning och testning med hjälp av det lokala Service Fabric-klustret.

3. Konsolprogrammet måste vara ett 64-bitarsprogram för att upprätthålla kompatibiliteten med gränssnittsprojektet och andra beroenden.  Högerklicka på **ActorClient-projektet** i Solution Explorer och klicka sedan på **Egenskaper**.  Ange **plattformsmål** till **x64**på fliken **Bygg** .
    
    ![Skapa egenskaper][8]

4. Kundprojektet kräver pålitliga aktörer NuGet paketet.  Klicka på **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.  Ange följande kommando i Package Manager Console:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    NuGet-paketet och alla dess beroenden är installerade i ActorClient-projektet.

5. Klientprojektet kräver också en referens till gränssnittsprojektet.  Högerklicka på **Beroenden** i projektet ActorClient och klicka sedan på **Lägg till referens...**.  Välj **Projekt > lösning** (om den inte redan är markerad) och markera kryssrutan bredvid **HelloWorld.Interfaces**.  Klicka på **OK**.
    
    ![Dialogrutan Lägg till referens][7]

6. I ActorClient-projektet ersätter du hela innehållet i *Program.cs* med följande kod:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Köra och felsöka

Tryck på **F5** för att skapa, distribuera och köra programmet lokalt i utvecklingsklustret för serviceinfrastruktur.  Under distributionsprocessen kan du se förloppet i **utdatafönstret.**

![Fönstret felsökning av tjänst fabric][3]

När utdata innehåller texten *är programmet klart*, det är möjligt att testa tjänsten med hjälp av ActorClient-programmet.  Högerklicka på **ActorClient-projektet** i Solution Explorer och klicka sedan på **Felsök** > **Starta ny instans**.  Kommandoradsprogrammet ska visa utdata från aktörstjänsten.

![Programutdata][9]

> [!TIP]
> Service Fabric Actors-körningen avger vissa [händelser och prestandaräknare relaterade till aktörsmetoder](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). De är användbara i diagnostik och prestandaövervakning.

## <a name="next-steps"></a>Nästa steg
Läs mer om [hur Reliable Actors använder plattformen Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png