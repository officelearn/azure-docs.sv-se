---
title: Skapa en aktör-baserad tjänst på Azure Service Fabric | Microsoft Docs
description: Lär dig mer om att skapa, felsöka och distribuera din första aktörbaserade tjänst i C# med Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: b6ca4810d86bb3c8413f0a740ac4483a848b8e10
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661096"
---
# <a name="getting-started-with-reliable-actors"></a>Komma igång med Reliable Actors
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-actors-get-started.md)
> * [Java i Linux](service-fabric-reliable-actors-get-started-java.md)

Den här artikeln visar hur du skapar och felsöker ett enkelt Reliable Actor-program i Visual Studio. Mer information om Reliable Actors finns [introduktion till Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du har Service Fabric-utvecklingsmiljö, inklusive Visual Studio, ställa in på din dator. Mer information finns i [hur du ställer in utvecklingsmiljön](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Skapa ett nytt projekt i Visual Studio

Starta Visual Studio 2015 eller senare som en administratör och sedan skapa en ny **Service Fabric-program** projekt:

![Service Fabric-verktyg för Visual Studio – nytt projekt][1]

I dialogrutan nästa väljer **Aktörstjänsten** under **.NET Core 2.0** och ange ett namn för tjänsten.

![Service Fabric-projektmallar][5]

Projektet som du har skapat visas följande struktur:

![Service Fabric projektstruktur][2]

## <a name="examine-the-solution"></a>Undersök lösningen

Lösningen innehåller tre projekt:

* **Programprojektet (MyApplication)**. Det här projektet paketerar alla tjänster tillsammans för distribution. Den innehåller den *ApplicationManifest.xml* och PowerShell-skript för att hantera programmet.

* **Gränssnittsprojektet (HelloWorld.Interfaces)**. Det här projektet innehåller gränssnittsdefinitionen för aktören. Aktörsgränssnitt kan definieras i alla projekt med ett namn.  Gränssnittet definierar aktörskontraktet som delas av aktörsimplementeringen och klienterna som anropar aktören.  Eftersom klienten projekt kan beror på den, är det vanligtvis meningsfullt att definiera den i en sammansättning som är separat från aktörsimplementeringen.

* **Aktörtjänstprojektet (HelloWorld)**. Det här projektet definierar Service Fabric-tjänst som kommer att vara värd för aktören. Den innehåller implementeringen av aktören, *HelloWorld.cs*. En aktörsimplementeringen är en klass som härleds från bastypen `Actor` och implementerar de gränssnitt som definierats i den *MyActor.Interfaces* projekt. En aktörsklassen måste även implementera en konstruktor som accepterar en `ActorService` instans och en `ActorId` och skickar dem till grundläggande `Actor` klass.
    
    Det här projektet innehåller också *Program.cs*, som registrerar aktören klasser med Service Fabric runtime med `ActorRuntime.RegisterActorAsync<T>()`. Den `HelloWorld` klassen är redan registrerad. Eventuella ytterligare aktören implementeringar som har lagts till i projektet måste också vara registrerade i den `Main()` metoden.

## <a name="customize-the-helloworld-actor"></a>Anpassa HelloWorld-aktör

Projektmallen definierar vissa metoder i den `IHelloWorld` gränssnitt och implementerar dem i den `HelloWorld` aktörsimplementeringen.  Ersätt dessa metoder så att aktörstjänsten returnerar en enkel ”Hello World”-sträng.

I den *HelloWorld.Interfaces* projektet i den *IHelloWorld.cs* filen och Ersätt gränssnittsdefinitionen på följande sätt:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

I den **HelloWorld** projektet i **HelloWorld.cs**, Ersätt hela klassdefinitionen på följande sätt:

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

Tryck på **Ctrl-Shift-B** skapa projektet och kontrollera att allt kompilerar.

## <a name="add-a-client"></a>Lägga till en klient

Skapa ett enkelt konsolprogram för att anropa aktörstjänsten.

1. Högerklicka på lösningen i Solution Explorer > **Lägg till** > **nytt projekt...** .

2. Under den **.NET Core** projekttyper, Välj **Konsolapp (.NET Core)**.  Ge projektet namnet *ActorClient*.
    
    ![Lägg till dialogrutan Nytt projekt][6]    
    
    > [!NOTE]
    > Ett konsolprogram är inte typ av app som du använder vanligtvis som en klient i Service Fabric, men det gör ett enkelt exempel för felsökning och testning med det lokala Service Fabric-klustret.

3. Konsolprogrammet måste vara ett 64-bitars program att bibehålla kompatibilitet med gränssnittsprojektet och andra beroenden.  I Solution Explorer högerklickar du på den **ActorClient** projektet och klicka sedan på **egenskaper**.  På den **skapa** fliken genom att ange **plattform target** till **x64**.
    
    ![Skapa egenskaper][8]

4. Klientprojektet kräver tillförlitliga aktörer NuGet-paketet.  Klicka på **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.  Ange följande kommando i Package Manager-konsolen:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    NuGet-paketet och alla dess beroenden är installerade i ActorClient-projektet.

5. Klientprojektet kräver också en referens till projektet gränssnitt.  Högerklicka i projektet ActorClient **beroenden** och klicka sedan på **Lägg till referens...** .  Välj **projekt > lösning** (om det inte redan är markerat), och markera sedan kryssrutan bredvid **HelloWorld.Interfaces**.  Klicka på **OK**.
    
    ![Lägg till referens dialogrutan][7]

6. Ersätt hela innehållet i projektu ActorClient *Program.cs* med följande kod:
    
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

Tryck på **F5** för att bygga, distribuera och köra programmet lokalt i klustret för Service Fabric-utveckling.  Under distributionsprocessen, kan du följa förloppet i den **utdata** fönster.

![Service Fabric felsökning utdatafönstret][3]

När utdata innehåller texten, *programmet är redo*, är det möjligt att testa tjänsten med hjälp av ActorClient-programmet.  I Solution Explorer högerklickar du på den **ActorClient** projektet och klicka sedan på **felsöka** > **Starta ny instans**.  Kommandoraden programmet ska visa utdata från aktörstjänsten.

![Programutdata][9]

> [!TIP]
> Service Fabric Actors runtime genererar några [händelser och prestandaräknare som rör aktören metoder](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). De är användbara i diagnostik och övervakning av programprestanda.

## <a name="next-steps"></a>Nästa steg
Läs mer om [hur Reliable Actors använder Service Fabric-plattformen](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png