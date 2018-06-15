---
title: Skapa din första Azure aktören-baserade-mikrotjänster i C# | Microsoft Docs
description: Den här självstudiekursen vägleder dig genom stegen för att skapa, felsöka och distribuera en enkel aktören-baserad tjänst med hjälp av Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: 32d3fa09c863c47753267e97e7c4730dff869887
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211413"
---
# <a name="getting-started-with-reliable-actors"></a>Komma igång med Reliable Actors
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-actors-get-started.md)
> * [Java i Linux](service-fabric-reliable-actors-get-started-java.md)

Den här artikeln beskrivs hur du skapar och felsökning av ett enkelt tillförlitliga aktören program i Visual Studio. Läs mer på Reliable Actors [introduktion till Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du har utvecklingsmiljön Service Fabric, inklusive Visual Studio, Ställ in på din dator. Mer information finns i [hur du ställer in utvecklingsmiljön](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Skapa ett nytt projekt i Visual Studio

Starta Visual Studio 2015 eller senare som en administratör och sedan skapa en ny **Fabric tjänstprogrammet** projektet:

![Service Fabric-verktyg för Visual Studio – nytt projekt][1]

Välj i dialogrutan nästa **aktören Service** under **.Net Core 2.0** och ange ett namn för tjänsten.

![Service Fabric-projektmallar][5]

Skapade projektet visas följande struktur:

![Struktur för Service Fabric-projekt][2]

## <a name="examine-the-solution"></a>Undersök lösningen

Lösningen innehåller tre projekt:

* **Projektet för konsolprogrammet (MyApplication)**. Det här projektet paket för alla tjänster tillsammans för distribution. Den innehåller den *ApplicationManifest.xml* och PowerShell-skript för att hantera programmet.

* **Gränssnittet projektet (HelloWorld.Interfaces)**. Projektet innehåller gränssnittsdefinition för aktören. Aktören gränssnitt kan definieras i ett projekt med ett namn.  Gränssnittet definierar kontraktet aktören som delas av aktören implementering och klienterna som anropar aktören.  Eftersom klienten projekt kan bero på den praktiskt det vanligtvis att definiera den i en sammansättning som skiljer sig från aktören implementeringen.

* **Aktören service-projekt (HelloWorld)**. Det här projektet definierar Service Fabric-tjänsten som ska vara värd för aktören. Den innehåller implementeringen av aktören, *HellowWorld.cs*. En implementering av aktören är en klass som härleds från bastypen `Actor` och implementerar gränssnitt som definierats i den *MyActor.Interfaces* projekt. En aktörklass måste också implementera en konstruktor som accepterar ett `ActorService` instans och en `ActorId` och skickar dem till grundläggande `Actor` klass.
    
    Det här projektet innehåller också *Program.cs*, som registrerar aktören klasser med Service Fabric runtime med `ActorRuntime.RegisterActorAsync<T>()`. Den `HelloWorld` klass har redan registrerats. Ytterligare aktören implementeringar läggs till i projektet måste också registreras i den `Main()` metoden.

## <a name="customize-the-helloworld-actor"></a>Anpassa HelloWorld aktören

Projektmallen definierar vissa metoder i den `IHelloWorld` gränssnitt och implementerar dem i den `HelloWorld` aktören implementering.  Ersätt metoderna så att tjänsten aktören returnerar en sträng i ”Hello World”.

I den *HelloWorld.Interfaces* projektet i den *IHelloWorld.cs* fil, Ersätt gränssnittsdefinitionen för på följande sätt:

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

Tryck på **Ctrl-Skift-B** att bygga projektet och kontrollera att allt kompileras.

## <a name="add-a-client"></a>Lägga till en klient

Skapa ett enkelt konsolprogram att anropa tjänsten aktören.

1. Högerklicka på lösningen i Solution Explorer > **Lägg till** > **nytt projekt...** .

2. Under den **.NET Core** projekttyper, Välj **Konsolapp (.NET Core)**.  Namnge projektet *ActorClient*.
    
    ![Lägg till dialogrutan Nytt projekt][6]    
    
    > [!NOTE]
    > Ett konsolprogram är inte typ av app som du använder normalt som en klient i Service Fabric, men det är ett enkelt exempel för felsökning och testning med det lokala Service Fabric-klustret.

3. Konsolprogrammet måste vara ett 64-bitarsprogram att bibehålla kompatibilitet med gränssnittet projektet och andra beroenden.  I Solution Explorer högerklickar du på den **ActorClient** projektet och klicka sedan på **egenskaper**.  På den **skapa** ställer du in **plattform mål** till **x64**.
    
    ![Skapa egenskaper][8]

4. Klientprojektet kräver tillförlitliga aktörer NuGet-paketet.  Klicka på **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.  Ange följande kommando i Package Manager-konsolen:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    NuGet-paketet och dess beroenden är installerade i ActorClient-projektet.

5. Klientprojektet kräver också en referens till projektet gränssnitt.  Högerklicka i projektet ActorClient **beroenden** och klicka sedan på **Lägg till referens...** .  Välj **projekt > lösning** (om du inte redan är markerat), och markera kryssrutan bredvid **HelloWorld.Interfaces**.  Klicka på **OK**.
    
    ![Lägg till referens dialogrutan][7]

6. Ersätt hela innehållet i i projektet ActorClient *Program.cs* med följande kod:
    
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

Tryck på **F5** för att skapa, distribuera och köra programmet lokalt i development Service Fabric-klustret.  Under distributionsprocessen, kan du se förloppet på den **utdata** fönster.

![Service Fabric felsökning utdatafönstret][3]

När utdata innehåller text, *att programmet är redo*, är det möjligt att testa tjänsten med hjälp av ActorClient-programmet.  I Solution Explorer högerklickar du på den **ActorClient** projektet och klicka sedan på **felsöka** > **Starta ny instans**.  Programmet kommandoraden ska visa utdata från tjänsten aktören.

![programutdata][9]

> [!TIP]
> Service Fabric aktörer runtime skickar vissa [händelser och prestandaräknare som rör aktören metoder](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). De är användbara i diagnostik- och prestandaövervakning.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [hur Reliable Actors använda Service Fabric-plattformen](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png