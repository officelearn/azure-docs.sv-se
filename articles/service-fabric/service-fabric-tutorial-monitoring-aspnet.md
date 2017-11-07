---
title: "Övervaknings- och diagnostikfunktionerna för ASP.NET Core services i Azure Service Fabric | Microsoft Docs"
description: "Lär dig hur du konfigurerar övervakning och diagnostik för ett program med Azure Service Fabric ASP.NET Core."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/14/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 68788efffd27edf2813cf455490b651c2c7106a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-diagnose-an-aspnet-core-application-on-service-fabric"></a>Övervaka och diagnostisera programmet ASP.NET Core på Service Fabric
Den här kursen ingår fyra i en serie. Den går igenom stegen för att konfigurera övervakning och diagnostik för ett ASP.NET Core-program som körs på ett Service Fabric-kluster med Application Insights. Vi samlar in telemetri från program som utvecklats i den första delen av kursen, [skapar ett .NET Service Fabric-program](service-fabric-tutorial-create-dotnet-app.md). 

I steg fyra i självstudiekursen serien lär du dig hur du:
> [!div class="checklist"]
> * Konfigurera Application Insights för ditt program
> * Samla in telemetri svar att spåra HTTP-baserad kommunikation mellan tjänster
> * Funktionen App kartan i Application Insights
> * Lägg till anpassade händelser med hjälp av Application Insights API

I den här självstudiekursen serien lär du dig hur du:
> [!div class="checklist"]
> * [Skapa ett .NET Service Fabric-program](service-fabric-tutorial-create-dotnet-app.md)
> * [Distribuera programmet till ett kluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Konfigurera CI/CD: N med hjälp av Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Konfigurera övervakning och diagnostik för programmet

## <a name="prerequisites"></a>Krav
Innan du börjar den här kursen:
- Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Installera Visual Studio 2017](https://www.visualstudio.com/) och installera den **Azure-utveckling** och **ASP.NET och web development** arbetsbelastningar.
- [Installera Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet röst
Om du inte att skapa exempelprogrammet röst [ingår i den här självstudiekursen serie](service-fabric-tutorial-create-dotnet-app.md), du kan ladda ned den. Kör följande kommando för att klona exempel app lagringsplatsen till den lokala datorn i ett kommandofönster eller terminal.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Ställa in en Application Insights-resurs
Application Insights är Azures programmet prestanda plattform och Service Fabric rekommenderade plattform för övervakning av program och diagnostik. Om du vill skapa en Application Insights-resurs, gå till [Azure-portalen](https://portal.azure.com). Klicka på **ny** på den vänstra navigeringsmenyn för att öppna Azure Marketplace. Klicka på **övervakning + Management** och sedan **Programinsikter**.

![Skapa ny AI-resurs](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Nu behöver du fylla i nödvändig information om attribut av resursen ska skapas. Ange en lämplig *namn*, *resursgruppen*, och *prenumeration*. Ange den *plats* till där du distribuerar Service Fabric-kluster i framtiden. I den här självstudiekursen kommer vi distribuera appen till det lokala klustret därför *plats* fältet är irrelevanta. Den *programtyp* ska lämnas som ”ASP.NET-webbprogram”. 

![AI resursattribut](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

När du har fyllt i informationen som krävs, klickar du på **skapa** för att etablera resurs - bör ta ungefär en minut. 
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Lägga till Application Insights i programmets services

Starta Visual Studio 2017 med förhöjda privilegier. Du kan göra detta genom att högerklicka på ikonen Visual Studio i Start-menyn och välja **kör som administratör**. Klicka på **filen** > **öppna** > **projekt/lösning** och navigera till programmet röst (antingen skapas i delen av kursen eller git Klona). Öppna *Voting.sln* och om du uppmanas att återställa programmets NuGet-paket på **Ja**.

Följ dessa steg för att konfigurera Application Insights för både VotingWeb och VotingData tjänster:
1. Högerklicka på namnet på tjänsten och på **konfigurera Application Insights...** .

    ![Konfigurera AI](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)

2. Klicka på **starta ledigt**.
3. Logga in på ditt konto (som du också ställa in din Azure-prenumeration) och välj den prenumeration där du skapade Application Insights-resursen. Hitta resurs under *befintliga Application Insights-resurs* i listrutan ”resurser”. Klicka på **registrera** att lägga till Application Insights i din tjänst.

    ![Registrera AI](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Klicka på **Slutför** när åtgärden är klar med dialogrutan som öppnas.
    
Se till att du har utfört stegen ovan **både** tjänster i programmet för att konfigurera Application Insights för programmet. Samma Application Insights-resursen används för både tjänster för att visa inkommande och utgående förfrågningar och kommunikation mellan tjänsterna.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Lägg till Microsoft.ApplicationInsights.ServiceFabric.Native NuGet till tjänsterna

Application Insights har två specifika Service Fabric-NuGets som kan användas, beroende på scenario. En används med Service Fabric inbyggda tjänster och andra med behållare och Gäst körbara filer. I det här fallet ska vi använda Microsoft.ApplicationInsights.ServiceFabric.Native NuGet utnyttja förståelsen av tjänstkontext som medför det. Du kan läsa mer om Application Insights SDK och Service Fabric specifika NuGets finns [Microsoft Application Insights för Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/develop/README.md). 

Här följer stegen för att ställa in NuGet:
1. Högerklicka på den **lösning 'Röstning'** längst upp i din Solution Explorer och klicka på **hantera NuGet-paket för lösningen...** .
2. Klicka på **Bläddra** på menyn övre navigeringsfältet av ”NuGet--postlösning” fönster och markera den **inkludera förhandsversion** kryssrutan bredvid sökfältet.
3. Sök efter `Microsoft.ApplicationInsights.ServiceFabric.Native` och klicka på lämplig NuGet-paketet.
4. Till höger klickar du på kryssrutorna bredvid de två tjänsterna i programmet **VotingWeb** och **VotingData** och på **installera**.
    ![AI registreringen har slutförts](./media/service-fabric-tutorial-monitoring-aspnet/aisdk-sf-nuget.png)
5. Klicka på **OK** på den *granska ändringar* dialogrutan som öppnas och godkänner den *godkännande av licens*. Lägga till NuGet till tjänsterna slutförs.
6. Nu måste du ställa in telemetri initierare i de två tjänsterna. Här öppna *VotingWeb.cs* och *VotingData.cs*. För båda, gör du följande två steg:
    1. Lägga till dessa två *med* uttryck högst upp i varje  *\<ServiceName > .cs*:
    
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```
    
    2. I den kapslade *returnera* hälsodeklarationen *CreateServiceInstanceListeners()* eller *CreateServiceReplicaListeners()*under *ConfigureServices*  >  *services*, mellan de två Singleton-tjänsterna som deklarerats, lägga till: `.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))`.
    Detta lägger till den *Service kontexten* till din telemetri tillåter dig att bättre förstå källan för dina telemetri i Application Insights. Den kapslade *returnera* instruktionen i *VotingWeb.cs* bör se ut så här:
    
    ```csharp
    return new WebHostBuilder().UseWebListener()
        .ConfigureServices(
            services => services
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<HttpClient>())
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    På samma sätt i *VotingData.cs*, bör du ha:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<IReliableStateManager>(this.StateManager))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

Nu är du redo att distribuera programmet. Klicka på **starta** överst (eller **F5**), och Visual Studio skapar och paketera programmet, konfigurera dina lokala kluster, och distribuera programmet till den. 

När programmet distribueras, öppna över [localhost:8080](localhost:8080), där du ska kunna se röstning sida exempelprogrammet. Rösta för exempel på några olika objekt du väljer att skapa några data och telemetri – jag gick för desserter!

![Rösterna för AI-exempel](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Passa på att *ta bort* röstning alternativen samt när du är klar att lägga till några röster.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Visa telemetri och kartan App i Application Insights 

HEAD över till Application Insights-resurs i Azure-portalen och i det vänstra navigeringsfältet för resursen, klicka på **förhandsgranskningar** under *konfigurera*. Aktivera **på** den *flera rollen programavbildningen* i listan över tillgängliga förhandsgranskningar.

![AI aktivera AppMap](./media/service-fabric-tutorial-monitoring-aspnet/ai-appmap-enable.png)

Klicka på **översikt** att gå tillbaka till Landningssida för din resurs. Klicka på **Sök** i upp till finns i spåren kommer in. Det tar några minuter för spårningarna ska visas i Application Insights. I det fall att du inte ser några, vänta en minut och tryck på **uppdatera** längst upp.
![AI Se spårningar](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Rulla ned på den *Sök* fönstret visa all inkommande telemetri som du får ut rutan med Application Insights. För varje åtgärd som du har gjort i programmet Röstningsdatabasen det finnas en utgående PUT-förfrågan från *VotingWeb* (PLACERA röster/Put [name]), en inkommande PUT-begäran från *VotingData* (PLACERA VoteData/Put [namn []), följt av ett par med GET-begäranden för att uppdatera data som visas. Det kommer också att en beroende-spårning för HTTP på localhost, eftersom dessa HTTP-begäranden. Här är ett exempel på vad som ska visas för hur en röst har lagts till: ![AI exempel spårning](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

Du kan klicka på en av spåren visa mer information om den. Det är användbar information om begäran som tillhandahålls av Application Insights, inklusive den *svarstid* och *URL-begäran*. Dessutom, eftersom du har lagt till Service Fabric specifika NuGet också visas data om ditt program i kontexten för ett Service Fabric-kluster i den *anpassade Data* nedan. Detta inkluderar tjänstkontext så att du kan se den *PartitionID* och *ReplicaId* källan för begäran, och bättre localize problem vid diagnostisering av fel i programmet.

![AI spåra information](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Dessutom eftersom vi aktiverat kartan App på den *översikt* sidan, klicka på den **App kartan** ikonen Visa både dina tjänster ansluten.

![AI spåra information](./media/service-fabric-tutorial-monitoring-aspnet/app-map.png)

Kartan App kan hjälpa dig att förstå din Programtopologi, särskilt när du börjar att lägga till flera olika tjänster som arbetar tillsammans. Den även ger dig grundläggande data på begäran slutförandefrekvenser och kan hjälpa dig att felsöka misslyckade begäranden för att förstå där saker kan ha blivit fel. Mer information om hur du använder App mappningen finns [programavbildningen i Application Insights](../application-insights/app-insights-app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Lägg till anpassade instrumentation i ditt program

Även om Application Insights erbjuder en mängd telemetri direkt, kan du vill lägga till ytterligare anpassade instrumentation. Detta kan baseras på dina behov eller för att förbättra diagnostik om något går fel i ditt program. Application Insights har en API för att mata in anpassade händelser och mått som du kan läsa mer om [här](../application-insights/app-insights-api-custom-events-metrics.md).

Ska vi lägga till vissa anpassade händelser till *VoteDataController.cs* (under *VotingData* > *domänkontrollanter*) till att spåra när rösterna som ska läggas till och tas bort från den underliggande *votesDictionary*. 
1. Lägg till `using Microsoft.ApplicationInsights;` i slutet av den andra using-instruktioner.
2. Deklarera en ny *TelemetryClient* i början av klassen under skapandet av den *IReliableStateManager*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. I den *Put()* fungera, lägga till en händelse som bekräftar en röst har lagts till. Lägg till `telemetry.TrackEvent($"Added a vote for {name}");` när transaktionen har slutförts, högerklicka innan tillbaka *OkResult* instruktionen.
4. I *Delete()*, det finns en ”om/annars” baserat på villkor att den *votesDictionary* innehåller röster för en given röstning alternativet. 
    1. Lägga till en händelse som bekräftar borttagningen av en röst i den *om* uttryck, efter den *await tx.CommitAsync()*:`telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Lägga till en händelse för att visa att borttagningen inte äger rum i den *annan* sats innan return-instruktion:`telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Här är ett exempel på vad din *Put()* och *Delete()* funktioner kan se ut när du lägger till händelser:

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

När du har gjort dessa ändringar **starta** programmet så att den skapar och distribuerar den senaste versionen av den. När programmet distribueras, öppna över [localhost:8080](localhost:8080), och lägga till och ta bort vissa röstning alternativ. Gå sedan tillbaka till Application Insights-resurs för att se spårningar för den senaste körningen (som tidigare, kan det ta 1 – 2 min visas i Application Insights spårningar). För alla rösterna du lagt till och ta bort, bör du nu se en ”anpassad händelse * tillsammans med alla svar telemetri. 

![anpassade händelser](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
> * Konfigurera Application Insights för ditt program
> * Samla in telemetri svar att spåra HTTP-baserad kommunikation mellan tjänster
> * Funktionen App kartan i Application Insights
> * Lägg till anpassade händelser med hjälp av Application Insights API

Nu när du har slutfört Konfigurera övervakning och diagnostik för ASP.NET-program kan du testa följande:
- [Utforska övervakning och diagnostik i Service Fabric](service-fabric-diagnostics-overview.md)
- [Service Fabric-händelseanalys med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
- Mer information om Application Insights finns [Application Insights-dokumentation](https://docs.microsoft.com/en-us/azure/application-insights/)
