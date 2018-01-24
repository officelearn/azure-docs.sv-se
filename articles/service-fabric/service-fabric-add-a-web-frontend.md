---
title: "Skapa en frontwebb för din Azure Service Fabric-app med ASP.NET Core | Microsoft Docs"
description: "Visa ditt Service Fabric-program på webben med hjälp av en ASP.NET Core projektet och mellan tjänsten kommunikation via tjänsten fjärrkommunikation."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: d4f78c63117e5c54eb855178c75d6c294957f2a1
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Skapa en tjänst Frontwebb för ditt program med hjälp av ASP.NET Core
Som standard tillhandahåller Azure Service Fabric-tjänster inte ett offentligt gränssnitt på webben. Du måste skapa ett webbprojekt för att fungera som en startpunkt och sedan kommunicera därifrån till enskilda tjänster för att exponera programfunktioner till http-klienter.

I den här självstudiekursen kommer vi ta vid där vi slutade i den [skapa ditt första program i Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) självstudier och Lägg till en webbtjänst för ASP.NET Core framför tjänsten tillståndskänslig räknaren. Om du inte redan har gjort det, bör du gå tillbaka och steg för steg som kursen först.

## <a name="set-up-your-environment-for-aspnet-core"></a>Konfigurera din miljö för ASP.NET Core

Du måste Visual Studio 2017 följa tillsammans med den här kursen. Gör en utgåva. 

 - [Installera Visual Studio 2017](https://www.visualstudio.com/)

Du bör ha följande arbetsbelastningarna för att utveckla ASP.NET Core Service Fabric-program:
 - **Azure-utveckling** (under *webb & moln*)
 - **ASP.NET och web development** (under *webb & moln*)
 - **.NET core plattformsoberoende development** (under *andra verktygsuppsättning*)

>[!Note] 
>.NET Core-verktyg för Visual Studio 2015 inte längre uppdateras, men om du fortfarande använder Visual Studio 2015, måste du ha [.NET Core VS 2015 Tooling Preview 2](https://www.microsoft.com/net/download/core) installerad.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Lägg till ett ASP.NET Core-tjänst i ditt program
ASP.NET Core är ett lätt, plattformsoberoende utveckling som du kan använda för att skapa moderna webbgränssnittet och webb-API: er. 

Lägg till ett ASP.NET Web API-projekt i vår befintliga programmet.

1. I Solution Explorer högerklickar du på **Services** i programmet projektet och välj **Lägg till > nya Service Fabric Service**.
   
    ![Lägga till en ny tjänst till ett befintligt program][vs-add-new-service]
2. På den **skapar du en tjänst** väljer **ASP.NET Core** och ge det ett namn.
   
    ![Välja ASP.NET-webbtjänst i dialogrutan Ny tjänst][vs-new-service-dialog]

3. Nästa sida innehåller en uppsättning av ASP.NET Core projektmallar. Observera att de är samma alternativ som du vill se om du har skapat ett ASP.NET Core projekt utanför ett Service Fabric-program med en liten mängd ytterligare kod för att registrera service med Service Fabric runtime. Den här kursen väljer **Web API**. Du kan dock använda samma begrepp för att skapa en fullständig webbprogram.
   
    ![Om du väljer projekttypen ASP.NET][vs-new-aspnet-project-dialog]
   
    När Web API-projekt har skapats bör du ha två tjänster i ditt program. Du kan lägga till fler tjänster på exakt samma sätt som du fortsätta att skapa ditt program. Båda kan vara oberoende versionsnummer och uppgraderade.

## <a name="run-the-application"></a>Köra programmet
För att få en uppfattning om vad vi har gjort kan vi distribuera det nya programmet och ta en titt på standardbeteendet ASP.NET Core Web API-mallen innehåller.

1. Tryck på F5 i Visual Studio för att felsöka appen.
2. När installationen är klar startar Visual Studio en webbläsare till roten i ASP.NET Web API-tjänsten. Mallen ASP.NET Core Web API Ange inte standardbeteendet för rot, så du bör se ett 404-fel i webbläsaren.
3. Lägg till `/api/values` till platsen i webbläsaren. Detta startar den `Get` metoden på ValuesController i Web API-mallen. Standardsvar som tillhandahålls av mallen--en JSON-matris som innehåller två strängar returnerar:
   
    ![Standardvärden som returneras från ASP.NET Core Web API-mall][browser-aspnet-template-values]
   
    Den här sidan visas i slutet av kursen för senaste räknarvärdet från våra tillståndskänslig service i stället för standard-strängar.

## <a name="connect-the-services"></a>Ansluta tjänsterna
Service Fabric ger fullständig flexibilitet i hur du kommunicerar med tillförlitlig tjänster. Du kan ha tjänster som är tillgänglig via TCP, andra tjänster som är tillgängliga via en HTTP-REST-API och fortfarande andra tjänster som är tillgängliga via webben sockets inom ett enda program. Bakgrund alternativen och kompromisser ingår, se [kommunicerar med tjänster](service-fabric-connect-and-communicate-with-services.md). I den här självstudiekursen kommer vi använda [Service Fabric-tjänsten fjärrkommunikation](service-fabric-reliable-services-communication-remoting.md), som ingår i SDK.

I metoden Remoting Service (modelleras på RPC-anrop eller RPC-anrop) definierar ett gränssnitt för att fungera som det offentliga kontraktet för tjänsten. Sedan kan du använda gränssnittet för att generera en proxyklass för att interagera med tjänsten.

### <a name="create-the-remoting-interface"></a>Skapa gränssnitt för fjärrkommunikation
Börja med att skapa gränssnittet som ska fungera som avtal mellan tillståndskänslig service och andra tjänster, web project i det här fallet ASP.NET Core. Det här gränssnittet måste delas av alla tjänster som använder den för att göra RPC-anrop, så att vi ska skapa i sin egen klassbiblioteket projekt.

1. Högerklicka på lösningen i Solution Explorer och välj **Lägg till** > **nytt projekt**.

2. Välj den **Visual C#** post i det vänstra navigeringsfönstret och välj sedan den **klassbiblioteket** mall. Se till att .NET Framework-version har angetts till **4.5.2**.
   
    ![Skapa ett projekt för gränssnittet för tillståndskänsliga tjänsten][vs-add-class-library-project]

3. Installera den **Microsoft.ServiceFabric.Services.Remoting** NuGet-paketet. Sök efter **Microsoft.ServiceFabric.Services.Remoting** i NuGet-paketet manager och installera det för alla projekt i lösningen som använder tjänsten Remoting, inklusive:
   - -Klassbiblioteket projektet som innehåller servicegränssnitt
   - Tillståndskänslig Service-projekt
   - ASP.NET Core web service-projekt
   
    ![Lägga till Services NuGet-paketet][vs-services-nuget-package]

4. Skapa ett gränssnitt med en enda metod i klassbiblioteket i `GetCountAsync`, och utökar gränssnittet från `Microsoft.ServiceFabric.Services.Remoting.IService`. Gränssnittet fjärrkommunikation måste härledas från det här gränssnittet för att ange att det är ett gränssnitt för tjänsten fjärrkommunikation.
   
    ```csharp
    using Microsoft.ServiceFabric.Services.Remoting;
    using System.Threading.Tasks;
        
    ...

    namespace MyStatefulService.Interface
    {
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```

### <a name="implement-the-interface-in-your-stateful-service"></a>Implementera gränssnittet i din tillståndskänslig service
Nu när vi har definierat i gränssnittet, som vi behöver implementera i tillståndskänslig service.

1. Lägg till en referens till klassbiblioteksprojektet som innehåller gränssnittet i din tillståndskänslig service.
   
    ![Lägga till en referens till klassbiblioteksprojektet i tillståndskänslig service][vs-add-class-library-reference]
2. Leta upp den klass som ärver från `StatefulService`, som `MyStatefulService`, och utvidga den att implementera den `ICounter` gränssnitt.
   
    ```csharp
    using MyStatefulService.Interface;
   
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
         ...
    }
    ```
3. Implementera nu den enda metod som har definierats i den `ICounter` gränssnittet `GetCountAsync`.
   
    ```csharp
    public async Task<long> GetCountAsync()
    {
        var myDictionary = 
            await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Exponera tjänsten tillståndskänslig med hjälp av en lyssnare för remoting service
Med den `ICounter` gränssnitt som implementerats det sista steget är att öppna tjänsten fjärrkommunikation kommunikationskanalen. För tillståndskänsliga tjänster, Service Fabric ger en åsidosättningsbar metod som kallas `CreateServiceReplicaListeners`. Med den här metoden kan du ange en eller flera kommunikationslyssnarna, beroende på vilken typ av kommunikation som du vill aktivera för din tjänst.

> [!NOTE]
> Den motsvarande metoden för att öppna en kommunikationskanal till tillståndslösa tjänster kallas `CreateServiceInstanceListeners`.

I det här fallet vi ersätta den befintliga `CreateServiceReplicaListeners` metod och ange en instans av `ServiceRemotingListener`, vilket skapar en RPC-slutpunkt som är callable från klienter via `ServiceProxy`.  

Den `CreateServiceRemotingListener` metod på den `IService` gränssnittet kan du enkelt skapa en `ServiceRemotingListener` med alla standardinställningar. Om du vill använda denna metod, se till att du har den `Microsoft.ServiceFabric.Services.Remoting.Runtime` namnområde som importeras. 

```csharp
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Använd klassen ServiceProxy ska interagera med tjänsten
Vår tillståndskänslig service är nu redo att ta emot trafik från andra tjänster via RPC. Så allt som fortfarande är att lägga till kod för att kommunicera med den från ASP.NET-webbtjänst.

1. ASP.NET-projektet, lägga till en referens till klassbiblioteket i som innehåller den `ICounter` gränssnitt.

2. Tidigare du har lagt till den **Microsoft.ServiceFabric.Services.Remoting** NuGet-paket i ASP.NET-projekt. Det här paketet ger den `ServiceProxy` klass som du använder för att skapa RPC-anrop till tillståndskänslig service. Kontrollera NuGet-paketet har installerats i ASP.NET Core web service-projekt.

4. I den **domänkontrollanter** mapp, öppna den `ValuesController` klass. Observera att den `Get` metoden returnerar för närvarande bara en hårdkodad strängmatris ”value1” och ”value2”--som matchar vad vi såg tidigare i webbläsaren. Ersätt den här implementeringen med följande kod:
   
    ```csharp
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
   
    ...

    [HttpGet]
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
   
    Den första raden i koden är den nyckel. Om du vill skapa ICounter proxy till tillståndskänslig tjänsten måste du ange två typer av information: partitions-ID och namnet på tjänsten.
   
    Du kan använda partitionering skala tillståndskänsliga tjänster genom att bryta deras tillstånd i olika buckets, baserat på en nyckel som du definierar, till exempel en kund-ID eller postnummer. I vårt trivial program har tjänsten tillståndskänslig endast en partition så att nyckeln ingen spelar roll. En nyckel som du anger kommer att leda till samma partition. Mer information om partitionering tjänster finns [så partition Service Fabric Reliable Services](service-fabric-concepts-partitioning.md).
   
    Tjänstnamnet är en URI för formuläret fabric: /&lt;$application_name&gt;/&lt;service_name&gt;.
   
    Med dessa två typer av information identifiera Service Fabric den dator som ska skickas till. Den `ServiceProxy` klassen hanterar också sömlöst om datorn som är värd för partitionen tillståndskänslig service växlar och en annan dator måste uppgraderas för att byta plats. Denna framställning kan skriva klientkod hantera andra tjänster som är betydligt enklare.
   
    När vi har proxyn kan vi helt enkelt anropa den `GetCountAsync` metoden och returnerar resultatet.

5. Tryck på F5 igen för att köra det ändrade programmet. Som startar tidigare, Visual Studio automatiskt webbläsaren till roten i webbprojektet. Lägg till sökvägen ”api/värdena” och du bör se det aktuella räknarvärdet returneras.
   
    ![Tillståndskänsliga räknarens värde visas i webbläsaren][browser-aspnet-counter-value]
   
    Uppdatera webbläsaren regelbundet för att se räknarvärdet uppdatera.

## <a name="connecting-to-a-reliable-actor-service"></a>Ansluta till en tillförlitlig aktören-tjänst
Den här självstudiekursen fokuserar på att lägga till en frontwebb som kommunicerat med en tillståndskänslig tjänst. Du kan dock följa en mycket lik modell tala med aktörer. När du skapar en tillförlitlig aktören-projekt genererar automatiskt ett projekt för gränssnittet för Visual Studio. Du kan använda gränssnittet för att skapa en proxy aktören webbprojektet att kommunicera med den. Kommunikationskanalen tillhandahålls automatiskt. Så du inte behöver göra något som motsvarar att upprätta en `ServiceRemotingListener` som du gjorde för tillståndskänsliga tjänsten i den här självstudiekursen.

## <a name="how-web-services-work-on-your-local-cluster"></a>Hur webbtjänster fungerar i din lokala klustret
I allmänhet kan du distribuera exakt samma Service Fabric program till ett kluster för flera datorer som du har distribuerat på din lokala klustret och vara mycket säker på att den fungerar som förväntat. Detta beror på att din lokala klustret är helt enkelt en fem-nodkonfiguration som döljs till en enda dator.

När det gäller webbtjänster, men är det en nyckel nuance. När klustret är placerad bakom en belastningsutjämnare i Azure, måste du se till att webbtjänster distribueras på varje dator eftersom belastningsutjämnaren bara round-resursallokering (robin) trafik på alla datorer. Du kan göra detta genom att ange den `InstanceCount` för tjänsten till särskilda värdet ”1”.

Däremot när du kör en webbtjänst lokalt, måste du kontrollera att endast en instans av körs tjänsten. Annars kan köra du i konflikt från flera processer som lyssnar på samma sökväg och port. Därför ska instansantalet web service anges till ”1” för lokala distributioner.

Information om hur du konfigurerar olika värden för olika miljöer finns [hantera programparametrar för miljöer med flera](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Nästa steg
Nu när du har en web front hamna set för ditt program med ASP.NET Core, Lär dig mer om [ASP.NET Core i Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md) för en djupgående förståelse av hur ASP.NET Core fungerar med Service Fabric.

Nästa [Lär dig mer om hur du kommunicerar med services](service-fabric-connect-and-communicate-with-services.md) i allmänhet att få en komplett bild av hur tjänsten kommunikation fungerar i Service Fabric.

När du har en god förståelse av hur tjänstkommunikation fungerar [skapa ett kluster i Azure och distribuera programmet till molnet](service-fabric-cluster-creation-via-portal.md).

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
