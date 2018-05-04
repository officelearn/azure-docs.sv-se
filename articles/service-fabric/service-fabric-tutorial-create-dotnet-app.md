---
title: Skapa ett .NET-program för Service Fabric | Microsoft Docs
description: I den här självstudien får du se hur du skapar ett program med en ASP.NET Core-klientdel och en tillförlitlig serverdel med en tillståndskänslig tjänst, och hur du distribuerar programmet till ett kluster.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 7840dc86ec7753980bb2c35f932f132c50d65f9e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Självstudie: Skapa och distribuera ett program med en ASP.NET Core Web API-klientdelstjänst och en tillståndskänslig serverdelstjänst
Den här självstudien ingår i en serie.  Du får se hur du skapar ett Azure Service Fabric-program med en ASP.NET Core Web API-klientdel och en tillståndskänslig serverdelstjänst för att lagra dina data. När du är klar har du ett röstningsprogam med ASP.NET Core-webbklient som sparar röstningsresultat i en tillståndskänslig backend-tjänst i klustret. Om du inte vill skapa röstningsprogrammet manuellt kan du [ladda ned källkoden](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) för det färdiga programmet och gå vidare till [Gå igenom exempelprogrammet för röstning](#walkthrough_anchor).  Om du vill kan du visa en [videogenomgång](https://channel9.msdn.com/Events/Connect/2017/E100) av den här kursen.

![Diagram över programmet](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * Skapa en ASP.NET Core Web API-tjänst som en tillståndskänslig, tillförlitlig tjänst
> * Skapa en ASP.NET Core-webbprogramtjänst som en tillståndskänslig, tillförlitlig webbtjänst
> * Använda omvänd proxy för att kommunicera med den tillståndskänsliga tjänsten

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett .NET Service Fabric-program
> * [Distribuera programmet till ett fjärrkluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Lägga till en HTTPS-slutpunkt i en klienttjänst i ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Konfigurera CI/CD med hjälp av Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar den här självstudien:
- om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Installera Visual Studio 2017](https://www.visualstudio.com/) version 15.5 eller senare med arbetsbelastningarna **Azure Development** och **ASP.NET och webbutveckling**.
- [Installera Service Fabric SDK](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Skapa en ASP.NET Core Web API-tjänst som en tillförlitlig tjänst
Skapa först webbklientdelen i röstningsprogrammet med ASP.NET Core. ASP.NET Core är ett lätt, plattformsoberoende utvecklingsverktyg som du kan använda för att skapa moderna webbgränssnitt och webb-API: er. För att du ska förstå hur ASP.NET Core kan integreras med Service Fabric rekommenderar vi att du läser igenom artikeln [ASP.NET Core i Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md). Just nu kan du bara följa den här kursen och komma igång snabbt. Läs mer om ASP.NET Core i [ASP.NET Core-dokumentationen](https://docs.microsoft.com/aspnet/core/).

1. Starta Visual Studio som **administratör**.

2. Skapa ett projekt med **Arkiv**->**Nytt**->**Projekt**

3. Klicka på **Moln > Service Fabric-program** i dialogrutan **Nytt projekt**.

4. Ge programmet namnet **Röstning** och tryck på **OK**.

   ![Dialogrutan Nytt projekt i Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. På sidan **Ny Service Fabric-tjänst** väljer du **Tillståndslös ASP.NET Core** och ger din tjänst namnet **VotingWeb**.
   
   ![Välja ASP.NET-webbtjänst i dialogrutan Ny tjänst](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. På nästa sida finns en uppsättning ASP.NET Core-projektmallar. För den här kursen väljer du **Webbprogram (Model-View-Controller)**. 
   
   ![Välj typ av ASP.NET-projekt](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio skapar ett programprojekt och ett tjänstprojekt, och de visas i Solution Explorer.

   ![Solution Explorer när ett program med ASP.NET Core Web API-tjänst har skapats]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>Lägg till AngularJS till VotingWeb-tjänsten
Lägg till [AngularJS](http://angularjs.org/) till tjänsten med hjälp av [Bower-stöd](/aspnet/core/client-side/bower). Lägg först till en konfigurationsfil för Bower i projektet.  I Solution Explorer högerklickar du på **VotingWeb** och väljer **Lägg till->Nytt objekt**. Välj **Webb** och sedan **Bower-konfigurationsfil**.  Filen *bower.json* skapas.

Öppna *bower.json* och lägg till poster för vinkel och vinkel-bootstrap. Spara sedan ändringarna.

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "3.2.1",
    "jquery-validation": "1.16.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.8",
    "angular-bootstrap": "v1.1.0"
  }
}
```
När du sparar filen *bower.json* installeras Angular i projektets *wwwroot/lib*-mapp. Den visas också i mappen *Beroenden/Bower*.

### <a name="update-the-sitejs-file"></a>Uppdatera site.js-filen
Öppna filen *wwwroot/js/site.js*.  Ersätt innehållet med det JavaScript som används av Start-vyerna:

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Uppdatera filen Index.cshtml
Öppna filen *Vyer/Start/Index.cshtml* -fil, den vy som används för Start.kontrollanten.  Byt ut innehållet mot följande och spara sedan ändringarna.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>Uppdatera filen _Layout.cshtml
Öppna filen *Vyer/Delad/_Layout.cshtml*, som är standardlayouten för ASP.NET-programmet.  Byt ut innehållet mot följande och spara sedan ändringarna.

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="~/lib/angular/angular.js"></script>
<script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Uppdatera filen VotingWeb.cs
Öppna filen *VotingWeb.cs* som skapar ASP.NET Core WebHost i den tillståndslösa tjänsten med hjälp av WebListener-webbservern.  

Lägg till `using System.Net.Http;`-direktivet överst i filen.  

Byt ut funktionen `CreateServiceInstanceListeners()` mot följande och spara sedan ändringarna.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Lägg även till metoden `GetVotingDataServiceName`, som returnerar tjänstnamnet när den avsöks:

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>Lägga till filen VotesController.cs
Lägg till en kontrollant som definierar röstningsåtgärderna. Högerklicka på mappen **Kontrollanter** och välj sedan **Lägg till-> Nytt objekt->Klass**.  Ge filen namnet ”VotesController.cs” och klicka på **Lägg till**.  

Byt ut filens innehåll mot följande och spara sedan ändringarna.  Längre fram, i [Uppdatera filen VotesController.cs](#updatevotecontroller_anchor), ändras den här filen för att läsa och skriva röstningsdata från serverdelstjänsten.  För tillfället returneras statiska strängdata till vyn av kontrollanten.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>Konfigurera lyssningsporten
När klientdelstjänsten VotingWeb skapas väljer Visual Studio slumpmässigt en port där tjänsten ska lyssna.  VotingWeb-tjänsten fungerar som klientdel för det här programmet och godkänner extern trafik, så vi kopplar den tjänsten till en fast och välkänd port.  I [tjänstmanifestet](service-fabric-application-and-service-manifests.md) anges tjänstens slutpunkter. I Solution Explorer öppnar du *VotingWeb/PackageRoot/ServiceManifest.xml*.  Leta rätt på resursen **Slutpunkt** i avsnittet **Resurser** och ändra värdet för **Port** till 80, eller till någon annan port. Om du vill distribuera och köra programmet lokalt måste programmets lyssningsport vara öppen och tillgänglig på datorn.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

Uppdatera också egenskapsvärdet för Program-URL i projektet Voting så att en webbläsare öppnas till rätt port när du felsöker med F5.  I Solution Explorer väljer du projektet **Voting** och uppdaterar egenskapen **Program-URL**.

![Program-URL](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

### <a name="deploy-and-run-the-application-locally"></a>Distribuera och köra programmet lokalt
Du kan nu köra appen. Tryck på `F5` i Visual Studio för att distribuera programmet för felsökning. `F5` misslyckas om du inte tidigare öppnade Visual Studio som **administratör**.

> [!NOTE]
> Första gången du kör och distribuerar programmet lokalt, skapar Visual Studio ett lokalt kluster för felsökning.  Det kan ta lite tid att skapa klustret. Statusen för klustergenereringen visas i utdatafönstret i Visual Studio.

Ditt webbprogram bör nu se ut så här:

![ASP.NET Core-klientdel](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Om du vill stoppa felsökningen av programmet går du tillbaka till Visual Studio och trycker på **SKIFT + F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Lägg till en tillståndskänslig serverdelstjänst i ditt program
Nu när ett ASP.NET Web API-tjänsten körs i programmet kan du fortsätta med att lägga till en tillståndskänslig tillförlitlig tjänst för att lagra data i programmet.

I Service Fabric kan du konsekvent och tillförlitligt lagra dina data direkt i tjänsten med hjälp av tillförlitliga samlingar. En tillförlitlig samling är en uppsättning samlingsklasser med hög tillgänglighet och tillförlitlighet som är välbekanta för alla som har använt C#-samlingar.

I den här självstudiekursen skapar du en tjänst som lagrar ett räknarvärde i en tillförlitlig samling.

1. I Solution Explorer högerklickar du på **Tjänster** i programprojektet och väljer **Lägg till > Ny Service Fabric-tjänst**.
    
2. I dialogrutan **Ny Service Fabric-tjänst** väljer du **Tillståndskänslig ASP.NET Core**, ger tjänsten namnet **VotingData** och trycker på **OK**.

    ![Dialogrutan Ny tjänst i Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    När tjänstprojektet har skapats har du två tjänster i ditt program. Du kan lägga till fler tjänster på samma sätt allt eftersom du fortsätter att bygga på ditt program. Tjänsterna kan ha olika versionsnummer och uppgraderas fristående.

3. På nästa sida finns en uppsättning ASP.NET Core-projektmallar. För den här självstudiekursen väljer du **Webb-API**.

    ![Välj typ av ASP.NET-projekt](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    Visual Studio skapar ett tjänstprojekt och visar det i Solution Explorer.

    ![Solution Explorer](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Lägga till filen VotesDataController.cs

I projektet **VotingData** högerklickar du på mappen **Kontrollanter** och väljer **Lägg till->Nytt objekt->Klass**. Ge filen namnet "VoteDataController.cs" och klicka på **Lägg till**. Byt ut filens innehåll mot följande och spara sedan ändringarna.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```


## <a name="connect-the-services"></a>Ansluta tjänsterna
I nästa steg ansluter du de två tjänsterna och får webbprogrammets klientdel att hämta och ange röstningsinformation från serverdelstjänsten.

I Service Fabric har du full flexibilitet i kommunikationen med tillförlitliga tjänster. I ett och samma program kan du ha tjänster som är tillgängliga TCP. Andra tjänster som kan nås via ett HTTP-REST-API och ännu fler tjänster kan vara tillgängliga via webbsockets. Bakgrundsinformation om de tillgängliga alternativen och vilka kompromisser du kan behöva göra finns i [Kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md).

I den här självstudiekursen använder du [ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md).

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Uppdatera filen VotesController.cs
I projektet **VotingWeb** öppnar du filen *Controllers/VotesController.cs*.  Byt ut innehållet i klassdefinition `VotesController` mot följande och spara sedan ändringarna.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Gå igenom exempelprogrammet för röstning
Röstningsprogrammet består av två tjänster:
- Webbklienttjänst (VotingWeb) – En webbklienttjänst för ASP.NET Core som används av webbsidan och visar webb-API:er för att kommunicera med serverdelstjänsten.
- Serverdelstjänst (VotingData) – En webbtjänst för ASP.NET Core som visar en API för att lagra röstningsresultat i en tillförlitlig ordlista på disken.

![Diagram över programmet](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

När du röstar i programmet händer följande:
1. Ett JavaScript skickar röstningsbegäran till webb-API i webbklienttjänsten som en HTTP PUT-begäran.

2. Webbklienttjänsten använder en proxy för att hitta och vidarebefordra en HTTP PUT-begäran till serverdelstjänsten.

3. Serverdelstjänsten tar den inkommande begäran och lagrar det uppdaterade resultatet i en tillförlitlig ordlista, som replikeras till flera noder i klustret och sparas på disken. Alla programdata lagras i klustret, så det behövs ingen databas.

## <a name="debug-in-visual-studio"></a>Felsökning i Visual Studio
När du felsöker programmet i Visual Studio använder du ett lokalt utvecklingskluster för Service Fabric. Du kan välja att anpassa felsökningen så att det passar ditt scenario. I det här programmet lagras data i serverdelstjänsten med hjälp av en tillförlitlig ordlista. Visual Studio tar som standard bort programmet när du stoppar felsökningsprogrammet. När programmet tas bort kommer även data i serverdelstjänsten att tas bort. Om du vill spara data mellan felsökningssessionerna kan du ändra **programmets felsökningsläge** som en egenskap i projektet **Voting** i Visual Studio.

Gör så här om du vill se vad som händer i koden:
1. Öppna filen **VotesController.cs** och konfigurera en brytpunkt i webb-API:ns metod **Put** (rad 63). Du kan söka efter filen i Solution Explorer i Visual Studio.

2. Öppna filen **VoteDataController.cs** och konfigurera en brytpunkt i denna webb-API:s **Put**-metod (rad 53).

3. Gå tillbaka till webbläsaren och klicka på ett röstningsalternativ eller lägg till ett nytt röstningsalternativ. Du kommer till den första brytpunkten i webbklientens api-kontroll.
    
    1. Här skickar JavaScript i webbläsaren en begäran till webb-API-kontrollen i frontwebbtjänsten.
    
    ![Lägg till röst för frontwebbtjänst](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. Skapa först URL:en till ReverseProxy för serverdelstjänsten **(1)**.
    3. Skicka sedan HTTP PUT-begäran till ReverseProxy **(2)**.
    4. Till sist returneras svaret från serverdelstjänsten till klienten **(3)**.

4. Tryck på **F5** för att fortsätta
    1. Du befinner dig nu på brytpunkten i serverdelstjänsten.
    
    ![Lägg till röst för serverdelstjänst](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. På den första raden i metoden **(1)** använder du `StateManager` för att hämta eller lägga till en tillförlitlig ordlista med namnet `counts`.
    3. All interaktion med värden i en tillförlitlig ordlista kräver en transaktion, den här använder instruktionen **(2)** som skapar den transaktionen.
    4. I transaktionen uppdaterar du värdet för den relevanta nyckeln för röstningsalternativet och utför åtgärden **(3)**. När utförandemetoden returneras uppdateras data i ordlistan och replikeras till andra noder i klustret. Data har nu lagrats i klustret och serverdelstjänsten kan redundansväxla till andra noder och fortfarande ha data tillgängliga.
5. Tryck på **F5** för att fortsätta

Stoppa felsökningssessionen genom att trycka på **Skift + F5**.


## <a name="next-steps"></a>Nästa steg
I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skapa en ASP.NET Core Web API-tjänst som en tillståndskänslig, tillförlitlig tjänst
> * Skapa en ASP.NET Core-webbprogramtjänst som en tillståndskänslig, tillförlitlig webbtjänst
> * Använda omvänd proxy för att kommunicera med den tillståndskänsliga tjänsten

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Distribuera programmet till Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
