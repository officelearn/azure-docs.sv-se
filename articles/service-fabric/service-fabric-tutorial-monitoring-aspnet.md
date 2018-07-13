---
title: Övervaka och diagnostisera ASP.NET Core-tjänster för Service Fabric i Azure | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du konfigurerar övervakning och diagnostik för ett program med Azure Service Fabric ASP.NET Core.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/14/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 8a98b12a42dff186c9226df39ce02c71cbc40c7e
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113331"
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric-using-application-insights"></a>Självstudie: Övervaka och diagnostisera en ASP.NET Core-app för Service Fabric med Application Insights

Den här självstudien är del fem i en serie. Den går igenom stegen för att konfigurera övervakning och diagnostik för ett ASP.NET Core-program som körs på ett Service Fabric-kluster med Application Insights. Vi samlar in telemetri från program som utvecklats i den första delen av självstudien [Skapa ett .NET Service Fabric-program](service-fabric-tutorial-create-dotnet-app.md).

I del fyra av den här självstudieserien får du lära dig att:
> [!div class="checklist"]
> * Konfigurera Application Insights för ditt program
> * Samla in svarstelemetri som spårar HTTP-baserad kommunikation mellan tjänster
> * Använda programkartefunktionen i Application Insights
> * Lägga till anpassade händelser med hjälp av Application Insights API

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett .NET Service Fabric-program](service-fabric-tutorial-create-dotnet-app.md)
> * [Distribuera programmet till ett fjärrkluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Lägga till en HTTPS-slutpunkt i en klienttjänst i ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Konfigurera CI/CD med hjälp av Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Konfigurera övervakning och diagnostik för programmet

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Installera Visual Studio 2017](https://www.visualstudio.com/) och installera **Azure Development** och arbetsbelastningarna **ASP.NET och webbutveckling**.
* [Installera Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet Röstning

Om du inte byggde exempelprogrammet Röstning i [del ett av självstudiekursen](service-fabric-tutorial-create-dotnet-app.md) kan du ladda ned det. Kör följande kommando i ett kommandofönster eller en terminal för att klona lagringsplatsen för exempelappen till den lokala datorn.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Konfigurera en Application Insights-resurs

Application Insights är Azures hanteringsplattform för programprestanda och Service Fabrics rekommenderade plattform för övervakning av program och diagnostik. Skapa en Application Insights-resurs genom att gå till [Azure Portal](https://portal.azure.com). Klicka på **Skapa en resurs** i den vänstra navigeringsmenyn för att öppna Microsoft Azure Marketplace. Klicka på **Övervakning och hantering** och sedan **Application Insights**.

![Skapa en ny AI-resurs](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Nu behöver du fylla i nödvändig information om attributen för den resurs som ska skapas. Ange ett lämpligt *Namn*, *Resursgrupp* och *Prenumeration*. Ange den *Plats* som du vill distribuera Service Fabric-klustret till i framtiden. I den här självstudien distribuerar vi appen till det lokala klustret, så fältet *Plats* är inte relevant. *Programtyp* ska fortsätta vara ”ASP.NET-webbprogram”.

![AI-resursattribut](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

När du har fyllt i informationen som krävs, klickar du på **Skapa** för att etablera resursen. Detta bör ta ungefär en minut.
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Lägga till Application Insights i tillämpningstjänsterna

Starta Visual Studio 2017 med utökade privilegier. Gör detta genom att högerklicka på Visual Studio-ikonen på startmenyn och välja **Kör som administratör**. Klicka på **Arkiv** > **Öppna** > **Projekt/lösning** och gå till programmet Röstning (antingen skapat i del ett av självstudien eller Git-klonad). Öppna *Voting.sln*. Om du uppmanas att återställa programmets NuGet-paket klickar du på **Ja**.

Följ dessa steg för att konfigurera Application Insights för både VotingWeb- och VotingData-tjänster:

1. Högerklicka på tjänstnamnet och klicka på **Konfigurera Application Insights...**.

    ![Konfigurera AI](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)

2. Klicka på **Start Free** (Starta kostnadsfritt).
3. Logga in på ditt konto (som du använde vid konfigurationen av din Azure-prenumeration) och välj den prenumeration där du skapade Application Insights-resursen. Resursen finns under *Befintlig Application Insights-resurs* i listrutan ”Resurs”. Klicka på **Registrera** för att lägga till Application Insights i din tjänst.

    ![Registrera AI](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Klicka på **Slutför** när dialogrutan öppnas och visar att åtgärden är klar.

Gör stegen ovan för **båda** tjänsterna i programmet för att slutföra konfigurationen av Application Insights för programmet. Samma Application Insights-resurs används för båda tjänsterna för att visa inkommande och utgående förfrågningar och kommunikation mellan tjänsterna.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Lägg till Microsoft.ApplicationInsights.ServiceFabric.Native NuGet till tjänsterna

Application Insights har två Service Fabric-specifika NuGets som kan användas beroende på scenario. Den ena används med Service Fabrics ursprungliga tjänster och andra med behållare och körbara gästfiler. I det här fallet ska vi använda Microsoft.ApplicationInsights.ServiceFabric.Native NuGet till att förstå vilken tjänstkontext den har. Du kan läsa mer om Application Insights SDK och Service Fabric-specifika NuGets i [Microsoft Application Insights för Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md).

Gör följande för att konfigurera NuGet:

1. Högerklicka på **lösningen Röstning** överst i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen...**.
2. Klicka på **Bläddra** på den översta navigeringsmenyn i fönstret ”NuGet - Solution” (NuGet – Lösning) och markera rutan för att **ta med förhandsversionen** bredvid sökfältet.
3. Sök efter `Microsoft.ApplicationInsights.ServiceFabric.Native` och klicka på lämpligt NuGet-paket.

>[!NOTE]
>Du kan behöva installera paketet Microsoft.ServiceFabric.Diagnistics.Internal på liknande sätt, om det inte finns förinstallerat, innan du installerar Application Insights-paketet

4. Till höger klickar du på kryssrutorna bredvid de två tjänsterna i programmet, **VotingWeb** och **VotingData**, samt på **Installera**.
    ![AI sdk Nuget](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. Klicka på **OK** i dialogrutan *Granska ändringar* som öppnas och *godkänn licensen*. Du är nu klar med att lägga till NuGet till tjänsterna.
6. Nu måste du konfigurera telemetriinitieraren i de två tjänsterna. Gör det genom att öppna *VotingWeb.cs* och *VotingData.cs*. Gör följande i båda två:
    1. Lägg till följande två *using*-instruktioner högst upp i varje *\<ServiceName >.cs*:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```

    2. I den kapslade *return*-instruktionen i *CreateServiceInstanceListeners()* eller *CreateServiceReplicaListeners()* under *ConfigureServices* > *services*, mellan de två deklarerade Singleton-tjänsterna, lägger du till följande: `.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))` Det här gör att *tjänstkontext* tas med i telemetrin, så att du får bättre förståelse för var telemetrin kommer ifrån i Application Insights. Den kapslade *return*-instruktionen i *VotingWeb.cs* bör se ut så här:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<HttpClient>(new HttpClient())
                .AddSingleton<FabricClient>(new FabricClient())
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    I *VotingData.cs* bör du ha:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<IReliableStateManager>(this.StateManager)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

Kontrollera att metoden `UseApplicationInsights()` anropas i båda filerna, som visas ovan.

>[!NOTE]
>Det här exempelprogrammet använder http för tjänster för att kommunicera. Om du utvecklar ett program med Service Remoting V2 behöver du även lägga till följande kodrader på samma plats som ovan

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

Nu är du redo att distribuera programmet. Klicka på **Start** överst (eller **F5**). Visual Studio skapar och paketerar programmet, konfigurerar ditt lokala kluster och distribuerar programmet till det.

När programmet har distribuerats kan du gå till [localhost:8080](localhost:8080), där du ska kunna se sidan Röstning i exempelprogrammet. Rösta på några olika objekt för att skapa exempeldata och telemetri – jag valde efterrätter!

![Röstning i AI-exempel](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Du kan *ta bort* några av röstningsalternativen eller lägga till ett par röster när du är klar.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Visa telemetri och programkarta i Application Insights

Navigera till Application Insights-resursen i Azure-portalen.

Klicka på **Översikt** för att gå tillbaka till startsidan för din resurs. Klicka sedan på **Sök** överst för att se spårningen. Det tar några minuter innan spårningarna visas i Application Insights. Om du inte ser några kan du vänta en minut och sedan trycka på knappen **Uppdatera** längst upp.
![AI ser spårningar](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Rulla nedåt i fönstret *Sök* för att se all inkommande telemetri som medföljer Application Insights. För varje åtgärd som du har gjort i programmet Röstning ska det finnas en utgående PUT-förfrågan från *VotingWeb* (PUT Votes/Put [namn]), en inkommande PUT-begäran från *VotingData* (PUT VoteData/Put [namn]), följt av ett par GET-begäranden om att uppdatera de data som visas. Det finns också en beroendespårning för HTTP på localhost, eftersom det är HTTP-begäranden. Här är ett exempel på vad som ska visas när en röst har lagts till: ![AI-exempel på spårningsbegäran](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

Du kan klicka på en av spårningarna för att se mer information om den. Det är användbar information om den begäran som kommit från Application Insights, inklusive *Svarstid* och *Fråge-URL*. Eftersom du dessutom lade till den Service Fabric-specifika NuGet, visas också data om ditt program i kontexten för ett Service Fabric-kluster i avsnittet *Anpassade data*. Detta inkluderar tjänstkontexten, så du kan se *PartitionID* och *ReplicaId* från källan för begäran, vilket gör det lättare att hitta problem vid diagnostisering av fel i programmet.

![AI spårar information](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Du kan också klicka på den vänstra menyn på översiktssidan för att visa *programkartan*, eller klicka på  **programkarteikonen**. Nu visas programkartan med de två kopplade tjänsterna.

![AI spårar information](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

Med programkartan blir det enklare att förstå programtopologin, särskilt när du börjar att lägga till flera olika tjänster som samverkar. Den ger dig även grundläggande data på begäran och kan hjälpa dig att felsöka misslyckade begäranden för att förstå vad som blev fel. Mer information om hur du använder programkartan finns i [Programkarta i Application Insights](../application-insights/app-insights-app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Lägga till anpassad instrumentation i ditt program

Även om Application Insights innehåller en mängd telemetri direkt, kanske du vill lägga till ytterligare anpassad instrumentation. Detta kan baseras på dina verksamhetsbehov eller förbättra diagnostiken om något fel uppstår i ditt program. Application Insights innehåller en API som matar in anpassade händelser och mått. Du kan läsa mer om den [här](../application-insights/app-insights-api-custom-events-metrics.md).

Låt oss lägga till vissa anpassade händelser i *VoteDataController.cs* (under *VotingData* > *Kontrollanter*) för att kunna spåra när röster läggs till och tas bort från underliggande *votesDictionary*.

1. Lägg till `using Microsoft.ApplicationInsights;` i slutet av de andra using-instruktionerna.
2. Deklarera en ny *TelemetryClient* i början av klassen under skapandet av *IReliableStateManager*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. I funktionen *Put()* lägger du till en händelse som bekräftar en röst har lagts till. Lägg till `telemetry.TrackEvent($"Added a vote for {name}");` när transaktionen har slutförts, direkt före return-instruktionen *OkResult*.
4. I *Delete()* finns det ett ”if/else” som baseras på villkoret att *votesDictionary* innehåller röster för ett specifikt röstningsalternativ.
    1. Lägg till en händelse som bekräftar borttagningen av en röst i *if*-instruktionen, efter *await tx.CommitAsync()*: `telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Lägg till en händelse som visar att borttagningen inte ägde rum i *else*-instruktionen före return-instruktionen: `telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Här är ett exempel på hur dina *Put()*- och *Delete()*-funktioner kan se ut när du har lagt till händelserna:

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

När du har gjort dessa ändringar **startar** du programmet så att den senaste versionen skapas och distribueras. När programmet har distribuerats går du till [localhost:8080](localhost:8080). Där lägger du till och tar bort vissa röstningsalternativ. Gå sedan tillbaka till Application Insights-resursen för att se spårningarna för den senaste körningen (det kan som sagt ta 1–2 minuter innan spårningar visas i Application Insights). För alla röster som du har lagt till och tagit bort, bör du nu se en ”Anpassad händelse” samt all svarstelemetri.

![anpassade händelser](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
> * Konfigurera Application Insights för ditt program
> * Samla in svarstelemetri som spårar HTTP-baserad kommunikation mellan tjänster
> * Använda programkartefunktionen i Application Insights
> * Lägga till anpassade händelser med hjälp av Application Insights API

Nu när du har slutfört konfigurationen av övervakning och diagnostik för ditt ASP.NET-program kan du testa följande:

* [Utforska övervakning och diagnostik ytterligare i Service Fabric](service-fabric-diagnostics-overview.md)
* [Service Fabric-händelseanalys med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* Mer information om Application Insights finns [Application Insights-dokumentationen](https://docs.microsoft.com/azure/application-insights/)
