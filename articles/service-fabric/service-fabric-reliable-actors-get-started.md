---
title: Skapa en aktör baserad tjänst på Azure Service Fabric | Microsoft Docs
description: Lär dig hur du skapar, felsöker och distribuerar din första aktörbaserade tjänst i C# använda Service Fabric Reliable Actors.
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
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: d870690416f96a2e1c24e6de16bdc8faa060f6bd
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68225180"
---
# <a name="getting-started-with-reliable-actors"></a>Komma igång med Reliable Actors
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-actors-get-started.md)
> * [Java i Linux](service-fabric-reliable-actors-get-started-java.md)

Den här artikeln beskriver hur du skapar och felsöker ett enkelt tillförlitligt aktörs program i Visual Studio. Mer information om Reliable Actors finns i [Introduktion till Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att du har Service Fabric utvecklings miljö, inklusive Visual Studio, konfigurerat på din dator. Mer information finns i [så här konfigurerar du utvecklings miljön](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Skapa ett nytt projekt i Visual Studio

Starta Visual Studio 2019 eller senare som administratör och skapa sedan ett nytt **Service Fabric program** projekt:

![Service Fabric verktyg för Visual Studio – nytt projekt][1]

I nästa dialog ruta väljer du **aktörs tjänst** under **.net Core 2,0** och anger ett namn för tjänsten.

![Service Fabric-projektmallar][5]

Det skapade projektet visar följande struktur:

![Service Fabric projekt struktur][2]

## <a name="examine-the-solution"></a>Undersök lösningen

Lösningen innehåller tre projekt:

* **Programprojektet (programmet)** . Det här projektet paketerar alla tjänsterna tillsammans för distribution. Den innehåller *ApplicationManifest. XML-* och PowerShell-skript för att hantera programmet.

* **Gränssnitts projektet (HelloWorld. Interfaces)** . Det här projektet innehåller en gränssnitts definition för aktören. Aktörs gränssnitt kan definieras i alla projekt med namn.  Gränssnittet definierar det aktörs avtal som delas av aktörs implementeringen och klienter som anropar aktören.  Eftersom klient projekt kan vara beroende av det, är det vanligt vis klokt att definiera det i en sammansättning som är separat från aktörs implementeringen.

* **Aktörs tjänst projektet (HelloWorld)** . Det här projektet definierar den Service Fabric tjänst som ska vara värd för aktören. Den innehåller implementeringen av aktören *HelloWorld.cs*. En aktörs implementering är en klass som härleds från bastypen `Actor` och implementerar de gränssnitt som definierats i projektet för teleaktör *. gränssnitt* . En aktörs klass måste också implementera en konstruktor som accepterar `ActorService` en instans och `ActorId` en och skickar dem till Bask `Actor` Lassen.
    
    Det här projektet innehåller också *program.cs*, som registrerar aktörs klasser med Service Fabric runtime `ActorRuntime.RegisterActorAsync<T>()`med hjälp av. `HelloWorld` Klassen är redan registrerad. Eventuella ytterligare aktörs implementeringar som läggs till projektet måste också registreras i `Main()` -metoden.

## <a name="customize-the-helloworld-actor"></a>Anpassa din HelloWorld-aktör

Projekt mal len definierar vissa metoder i `IHelloWorld` gränssnittet och implementerar dem `HelloWorld` i aktörs implementeringen.  Ersätt dessa metoder så att aktörs tjänsten returnerar en enkel "Hello World"-sträng.

I projektet *HelloWorld. Interfaces* , i *IHelloWorld.cs* -filen, ersätter du gränssnitts definitionen enligt följande:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

Ersätt hela klass definitionen i **HelloWorld.cs**i projektet **HelloWorld** enligt följande:

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

Tryck på **CTRL-SHIFT-B** för att skapa projektet och se till att allt kompileras.

## <a name="add-a-client"></a>Lägg till en klient

Skapa ett enkelt konsol program för att anropa aktörs tjänsten.

1. Högerklicka på lösningen i Solution Explorer > **Lägg till** > **nytt projekt.** ...

2. Under **.net Core** -projekt typer väljer du **konsol program (.net Core)** .  Ge projektet namnet *ActorClient*.
    
    ![Dialog rutan Lägg till nytt projekt][6]    
    
    > [!NOTE]
    > Ett konsol program är inte den typ av app som du normalt använder som en klient i Service Fabric, men det är ett bekvämt exempel för fel sökning och testning med det lokala Service Fabric klustret.

3. Konsol programmet måste vara ett 64-bitars program för att upprätthålla kompatibiliteten med gränssnitts projektet och andra beroenden.  I Solution Explorer högerklickar du på projektet **ActorClient** och klickar sedan på **Egenskaper**.  På fliken **skapa** ställer du in **plattforms mål** till **x64**.
    
    ![Bygg egenskaper][8]

4. Klient projektet kräver det pålitliga NuGet-paketet för skådespelare.  Klicka på **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.  I Package Manager-konsolen anger du följande kommando:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    NuGet-paketet och alla dess beroenden installeras i ActorClient-projektet.

5. Klient projektet kräver också en referens till gränssnitts projektet.  I ActorClient-projektet högerklickar du på **beroenden** och klickar sedan på **Lägg till referens...** .  Välj **projekt > lösning** (om du inte redan har gjort det) och kryssa sedan i kryss rutan bredvid **HelloWorld. Interfaces**.  Klicka på **OK**.
    
    ![Dialog rutan Lägg till referens][7]

6. I ActorClient-projektet ersätter du hela innehållet i *program.cs* med följande kod:
    
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

Tryck på **F5** för att skapa, distribuera och köra programmet lokalt i Service Fabric utvecklings klustret.  Under distributions processen kan du se förloppet i fönstret **utdata** .

![Service Fabric fel sökning av utdata-fönster][3]

När utdata innehåller texten *är programmet klart*, det är möjligt att testa tjänsten med ActorClient-programmet.  I Solution Explorer högerklickar du på projektet **ActorClient** och klickar sedan på **Felsök** > **Starta ny instans**.  Kommando rads programmet ska visa utdata från aktörs tjänsten.

![Programutdata][9]

> [!TIP]
> Service Fabric aktörs körningar genererar vissa [händelser och prestanda räknare som är relaterade till aktörs metoder](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). De är användbara i diagnostik-och prestanda övervakning.

## <a name="next-steps"></a>Nästa steg
Läs mer om [hur Reliable Actors använder service Fabrics plattformen](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png