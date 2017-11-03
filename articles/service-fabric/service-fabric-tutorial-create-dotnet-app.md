---
title: "Skapa ett .NET-program för Service Fabric | Microsoft Docs"
description: "Lär dig hur du skapar ett program med en ASP.NET Core frontend och en tillförlitlig tjänst tillståndskänslig backend- och distribuera programmet till ett kluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 5a095663b7e716fd63322c9f89f67a1f3187638b
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Skapa och distribuera ett program med en webb-API för ASP.NET Core frontend-tjänst och en tillståndskänslig backend-tjänst
Den här kursen ingår i en serie.  Du får lära dig att skapa ett Azure Service Fabric-program med en ASP.NET Core Web API-klient och en tillståndskänslig backend-tjänst för att lagra data. När du är klar har du röstningsapp med en ASP.NET Core webbklientdelen som sparar röstning resultat i en tillståndskänslig backend-tjänst i klustret. Om du inte vill skapa röstning programmet manuellt, kan du [ladda ned källkoden](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) för det färdiga programmet och gå vidare till [igenom röstning exempelprogrammet](#walkthrough_anchor).

![Diagram över](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

I delen en av serierna kan du lära dig hur du:

> [!div class="checklist"]
> * Skapa en ASP.NET Core Web API-tjänst som en tillståndskänslig tillförlitlig tjänst
> * Skapa ett webbprogram för ASP.NET Core-tjänst som en tillståndslös webbtjänst
> * Använd omvänd proxy för att kommunicera med tjänsten tillståndskänslig

I den här självstudiekursen serien lär du dig hur du:
> [!div class="checklist"]
> * Skapa ett .NET Service Fabric-program
> * [Distribuera programmet till ett kluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Konfigurera CI/CD: N med hjälp av Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Krav
Innan du börjar den här kursen:
- Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Installera Visual Studio 2017](https://www.visualstudio.com/) och installera den **Azure-utveckling** och **ASP.NET och web development** arbetsbelastningar.
- [Installera Service Fabric SDK](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Skapa en ASP.NET Web API-tjänst som en tillförlitlig tjänst
Först skapa webben frontend röstning programkonfigurationen med ASP.NET Core. ASP.NET Core är ett lätt, plattformsoberoende utveckling som du kan använda för att skapa moderna webbgränssnittet och webb-API: er. För att få en förståelse av hur ASP.NET Core kan integreras med Service Fabric, rekommenderar vi starkt läsa igenom den [ASP.NET Core i Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md) artikel. Nu kan du följa den här kursen och kom igång snabbt. Läs mer om ASP.NET Core i den [ASP.NET Core-dokumentation](https://docs.microsoft.com/aspnet/core/).

> [!NOTE]
> Den här kursen är baserad på den [ASP.NET Core tools för Visual Studio 2017](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc). .NET Core-verktyg för Visual Studio 2015 uppdateras inte längre.

1. Starta Visual Studio som **administratör**.

2. Skapa ett projekt med **filen**->**ny**->**projekt**

3. Klicka på **Moln > Service Fabric-program** i dialogrutan **Nytt projekt**.

4. Ge programmet namnet **Röstningsdatabasen** och tryck på **OK**.

   ![Dialogrutan Nytt projekt i Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. På den **nya Service Fabric Service** väljer **tillståndslös ASP.NET Core**, och namnge din tjänst **VotingWeb**.
   
   ![Välja ASP.NET-webbtjänst i dialogrutan Ny tjänst](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. Nästa sida innehåller en uppsättning av ASP.NET Core projektmallar. Den här kursen väljer **webbprogram**. 
   
   ![Välj typ av ASP.NET-projekt](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio skapar ett program och ett tjänstprojekt och visar dem i Solution Explorer.

   ![Solution Explorer efter skapandet av program med ASP.NET core Web API-tjänsten]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>Lägg till AngularJS i VotingWeb-tjänst
Lägg till [AngularJS](http://angularjs.org/) till tjänsten med hjälp av inbyggt [Bower support](/aspnet/core/client-side/bower). Öppna *bower.json* och lägga till poster för vinkel och vinkel bootstrap och sedan spara ändringarna.

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "2.2.0",
    "jquery-validation": "1.14.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.5",
    "angular-bootstrap": "v1.1.0"
  }
}
```
När det sparas den *bower.json* filen Angular har installerats i ditt projekt *wwwroot/lib* mapp. Dessutom visas inom den *beroenden/Bower* mapp.

### <a name="update-the-sitejs-file"></a>Uppdatera site.js-filen
Öppna den *wwwroot/js/site.js* fil.  Ersätt innehållet med JavaScript som används av hem-vyer:

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

### <a name="update-the-indexcshtml-file"></a>Uppdatera Index.cshtml-filen
Öppna den *Views/Home/Index.cshtml* -fil, vyn specifika för Home-styrenhet.  Ersätt innehållet med följande och sedan spara ändringarna.

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
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

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

### <a name="update-the-layoutcshtml-file"></a>Uppdatera _Layout.cshtml-filen
Öppna den *Views/Shared/_Layout.cshtml* -fil, standardlayout för ASP.NET-app.  Ersätt innehållet med följande och sedan spara ändringarna.

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="~/css/site.css" rel="stylesheet" />

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

### <a name="update-the-votingwebcs-file"></a>Uppdatera VotingWeb.cs-filen
Öppna den *VotingWeb.cs* filen, vilket skapar ASP.NET Core Webbvärden inuti tillståndslösa tjänsten WebListener webbservern.  Lägg till den `using System.Net.Http;` direktivet överst i filen.  Ersätt den `CreateServiceInstanceListeners()` fungerar med följande och sedan spara ändringarna.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
            {
                ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting WebListener on {url}");

                return new WebHostBuilder().UseWebListener()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<StatelessServiceContext>(serviceContext)
                                    .AddSingleton<HttpClient>())
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseApplicationInsights()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
            }))
    };
}
```

### <a name="add-the-votescontrollercs-file"></a>Lägga till filen VotesController.cs
Lägg till en domänkontrollant som definierar röstning åtgärder. Högerklickar du på den **domänkontrollanter** mapp, välj sedan **Lägg till -> Ny-objekt > klassen**.  Namn på filen ”VotesController.cs” och klicka på **Lägg till**.  Ersätt filens innehåll med följande och sedan spara ändringarna.  Senare i [uppdateringsfil VotesController.cs](#updatevotecontroller_anchor), den här filen kommer att ändras för att läsa och skriva röstning data från backend-tjänst.  För tillfället returnerar styrenheten statiska strängdata till vyn.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using Newtonsoft.Json;
using System.Text;
using System.Net.Http;
using System.Net.Http.Headers;

namespace VotingWeb.Controllers
{
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



### <a name="deploy-and-run-the-application-locally"></a>Distribuera och köra programmet lokalt
Du kan nu gå vidare och köra programmet. Tryck på `F5` i Visual Studio för att distribuera programmet för felsökning. `F5`misslyckas om du inte tidigare öppna Visual Studio som **administratör**.

> [!NOTE]
> Första gången du kör och distribuerar programmet lokalt, skapar Visual Studio ett lokalt kluster för felsökning.  Skapa ett kluster kan ta en stund. Statusen för klustergenereringen visas i utdatafönstret i Visual Studio.

Ditt webbprogram bör nu se ut så här:

![ASP.NET Core frontend](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Om du vill stoppa felsökningen av programmet, gå tillbaka till Visual Studio och tryck på **SKIFT + F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Lägg till en tillståndskänslig backend-tjänst i ditt program
Nu när vi har ett ASP.NET Web API-tjänsten som körs i vårt program kan vi gå vidare och Lägg till en tillståndskänslig tillförlitlig tjänst för att lagra vissa data i appen.

Service Fabric kan du konsekvent och tillförlitligt sätt lagra dina data direkt i din tjänst med hjälp av tillförlitliga samlingar. Tillförlitliga samlingar är en uppsättning med hög tillgänglighet och tillförlitlig Samlingsklasser som känner till alla som har använt C#-samlingar.

I den här självstudiekursen skapar du en tjänst som lagrar counter-värdet i en tillförlitlig samling.

1. I Solution Explorer högerklickar du på **Services** i programmet projektet och välj **Lägg till > nya Service Fabric Service**.
   
    ![Lägga till en ny tjänst till ett befintligt program](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. I den **nya Service Fabric Service** dialogrutan Välj **Stateful ASP.NET Core**, och ger den namnet tjänsten **VotingData** och tryck på **OK**.

    ![Dialogrutan Ny tjänst i Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    När service-projekt har skapats har du två tjänster i ditt program. Du kan lägga till fler tjänster på samma sätt som du fortsätta att skapa ditt program. Båda kan vara oberoende versionsnummer och uppgraderade.

3. Nästa sida innehåller en uppsättning av ASP.NET Core projektmallar. Den här kursen väljer **Web API**.

    ![Välj typ av ASP.NET-projekt](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    Visual Studio skapar ett service-projekt och visar dem i Solution Explorer.

    ![Solution Explorer](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Lägga till filen VoteDataController.cs

I den **VotingData** projektet genom att högerklicka på den **domänkontrollanter** mapp, välj sedan **Lägg till -> Ny-objekt > klassen**. Namn på filen ”VoteDataController.cs” och klicka på **Lägg till**. Ersätt filens innehåll med följande och sedan spara ändringarna.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.ServiceFabric.Data;
using System.Threading;
using Microsoft.ServiceFabric.Data.Collections;

namespace VotingData.Controllers
{
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
            var ct = new CancellationToken();

            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                var list = await votesDictionary.CreateEnumerableAsync(tx);

                var enumerator = list.GetAsyncEnumerator();

                var result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return Json(result);
            }
        }

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
I den här nästa steg ska vi ansluta de två tjänsterna och gör frontwebbservern programmet get och set röstning information från backend-tjänsten.

Service Fabric ger fullständig flexibilitet i hur du kommunicerar med tillförlitlig tjänster. I ett enskilt program kanske tjänster som är tillgängliga via TCP. Andra tjänster som kan nås via en HTTP-REST-API och fortfarande andra tjänster kan vara tillgänglig via webben sockets. Bakgrund alternativen och kompromisser ingår, se [kommunicerar med tjänster](service-fabric-connect-and-communicate-with-services.md).

I den här kursen använder [ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md).

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Uppdatera VotesController.cs-filen
I den **VotingWeb** projektet öppnar den *Controllers/VotesController.cs* fil.  Ersätt den `VotesController` klassen definition innehållet med följande och sedan spara ändringarna.

```csharp
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;
        string serviceProxyUrl = "http://localhost:19081/Voting/VotingData/api/VoteData";
        string partitionKind = "Int64Range";
        string partitionKey = "0";

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            IEnumerable<KeyValuePair<string, int>> votes;

            HttpResponseMessage response = await this.httpClient.GetAsync($"{serviceProxyUrl}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            votes = JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync());

            return Json(votes);
        }

        // PUT: api/Votes/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            string payload = $"{{ 'name' : '{name}' }}";
            StringContent putContent = new StringContent(payload, Encoding.UTF8, "application/json");
            putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

            string proxyUrl = $"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}";

            HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent);

            return new ContentResult()
            {
                StatusCode = (int)response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }

        // DELETE: api/Votes/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            HttpResponseMessage response = await this.httpClient.DeleteAsync($"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            return new OkResult();

        }
    }
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Gå igenom röstning exempelprogrammet
Röstning program består av två tjänster:
- Web frontend-tjänst (VotingWeb) – en ASP.NET Core web frontend-tjänst som används på webbsidan och visar webb-API: er för att kommunicera med serverdelstjänsten.
- Backend-tjänst (VotingData)-ett ASP.NET Core webbtjänsten som Exponerar en API för att lagra rösten resultat i en tillförlitlig ordlista kvar på disken.

![Diagram över](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

När du rösta i programmet händer följande:
1. En JavaScript skickar röstningsbegäran till webb-API i Frontend webbtjänsten som ett HTTP PUT-begäran.

2. Frontend webbtjänsten använder en proxyserver för att leta upp och vidarebefordra en HTTP PUT-begäran till backend-tjänst.

3. Backend-tjänst tar den inkommande begäranden och lagrar det uppdaterade resultat i en tillförlitlig ordlista som hämtar replikeras till flera noder i klustret och kvar på disken. Alla programdata lagras i klustret, så det behövs ingen databas.

## <a name="debug-in-visual-studio"></a>Felsökning i Visual Studio
När du felsöker programmet i Visual Studio använder du en lokal utveckling Service Fabric-klustret. Du har möjlighet att anpassa upplevelsen felsökning för ditt scenario. I det här programmet lagrar vi data i vår backend-tjänst med hjälp av en tillförlitlig ordlista. Visual Studio tar bort programmet per standard när du stoppar felsökningsprogrammet. Ta bort programmet gör att data i backend-tjänsten också att tas bort. För att bevara data mellan felsökning sessioner, du kan ändra den **programmet felsökningsläge** som en egenskap på den **Röstningsdatabasen** projekt i Visual Studio.

Om du vill titta på vad som händer i koden, gör du följande:
1. Öppna den **VotesController.cs** fil och anger en brytpunkt i webb-API: er **placera** metod (rad 47) – du kan söka efter filen i Solution Explorer i Visual Studio.

2. Öppna den **VoteDataController.cs** fil och anger en brytpunkt i den här web API **placera** metod (rad 50).

3. Gå tillbaka till webbläsaren och klicka på röstning alternativet eller lägga till ett nytt alternativ för röstning. Du har nått den första brytpunkten i web front-slutets api-kontrollanten.
    
    1. Detta är där JavaScript i webbläsaren skickar en begäran till web API-kontrollanten i frontend-tjänsten.
    
    ![Lägg till röst frontend-tjänst](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. Först skapar vi URL till ReverseProxy för vår backend-tjänst **(1)**.
    3. Skickar vi den HTTP PUT-begäran till ReverseProxy **(2)**.
    4. Slutligen den returnerar vi svaret från backend-tjänst till klienten **(3)**.

4. Tryck på **F5** fortsätta
    1. Du är nu på break punkt i backend-tjänst.
    
    ![Lägga till röst backend-tjänst](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. I den första raden i metoden **(1)** vi använder den `StateManager` att hämta eller lägga till en tillförlitlig ordlista som heter `counts`.
    3. All interaktion med värden i en tillförlitlig ordlista kräver en transaktion detta med hjälp av instruktionen **(2)** skapar den aktuella transaktionen.
    4. I transaktionen, vi sedan uppdatera värdet för den aktuella nyckeln för alternativet röstning och genomför åtgärden **(3)**. När commit-metoden returnerar data uppdateras i ordlistan och replikeras till andra noder i klustret. Data lagras nu på ett säkert sätt i klustret och backend-tjänst kan växla över till andra noder som fortfarande har data som är tillgängliga.
5. Tryck på **F5** fortsätta

Om du vill stoppa felsökningssessionen trycker du på **SKIFT + F5**.


## <a name="next-steps"></a>Nästa steg
I denna del av kursen får du lära dig hur du:

> [!div class="checklist"]
> * Skapa en ASP.NET Core Web API-tjänst som en tillståndskänslig tillförlitlig tjänst
> * Skapa ett webbprogram för ASP.NET Core-tjänst som en tillståndslös webbtjänst
> * Använd omvänd proxy för att kommunicera med tjänsten tillståndskänslig

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Distribuera programmet till Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
