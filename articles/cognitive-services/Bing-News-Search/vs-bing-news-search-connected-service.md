---
title: 'Självstudie: Nyhetssökning i Bing, C#'
titleSuffix: Azure Cognitive Services
description: Ansluta till Nyhetssökning i Bing från en ASP.NET Core-webbapp.
services: cognitive-services
author: ghogen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: f1f5c590216975ce6b0813da6d9d98279d591454
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804352"
---
# <a name="tutorial-connect-to-bing-news-search-api-by-using-connected-services-in-visual-studio"></a>Självstudie: Ansluta till API för nyhetssökning i Bing med anslutna tjänster i Visual Studio

Du kan aktivera appar och tjänster för att dra nytta av en annonsfri sökmotor som omfattar webben. Nyhetssökning i Bing är en av de söktjänster som är tillgängliga med Cognitive Services.

Den här artikeln innehåller information om att använda Visual Studio-funktionen för ansluten tjänst för nyhetssökning i Bing. Funktionen är tillgänglig i Visual Studio 2017 15.7 eller senare, med Cognitive Services-tillägget installerat.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure-prenumeration. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 version 15.7 med arbetsbelastningen webbutveckling installerad. [Ladda ned det nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Lägga till stöd i ditt projekt för API för nyhetssökning i Bing

1. Skapa ett nytt webbprojekt i ASP.NET Core med namnet MyWebApplication. Använd projektmallen för **webbprogram (Model-View-Controller)** med samtliga standardinställningar. Det är viktigt att ge projektet namnet MyWebApplication, så namnrymden matchar när du kopierar kod till projektet. 

1. I **Solution Explorer** väljer du **Lägg till** > **Ansluten tjänst**.
   Sidan Ansluten tjänst visas med tjänster som du kan lägga till i projektet.

   ![Skärmbild av Lägga till menyobjektet Ansluten tjänst](../media/vs-common/Connected-Service-Menu.PNG)

1. På menyn med tillgängliga tjänster väljer du alternativet för att **lägga till smarta sökfunktioner i dina appar**.

   ![Skärmbild av listan över anslutna tjänster](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Om du har loggat in till Visual Studio och har en Azure-prenumeration som är associerad med ditt konto, visas en sida med en listruta med dina prenumerationer. Välj den prenumeration som du vill använda och välj sedan ett namn på API:et för nyhetssökning i Bing. Du kan också välja **Redigera** för att ändra det automatiskt genererade namnet.

   ![Skärmbild av prenumerationen och namnfälten](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Välj resursgruppen och prisnivån.

   ![Skärmbild av fälten resursgrupp och prisnivå](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Om du vill veta mer om prisnivåerna väljer du **Review pricing** (Se prissättning).

1. Välj **Lägg till** om du vill lägga till sådana stöd för den anslutna tjänsten.
   Visual Studio ändrar ditt projekt för att lägga till NuGet-paketen, konfigurationsfilsposter och andra ändringar till stöd för en anslutning till API för nyhetssökning i Bing. Utdata innehåller en logg över vad som händer i projektet. Du bör se något som liknar följande:

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   Filen appsettings.json innehåller nu följande nya inställningar:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Använd API för nyhetssökning i Bing för att lägga till sökfunktioner till en webbsida

Nu när du har lagt till stöd för API för nyhetssökning i Bing i projektet ser du här hur du använder API:et för att lägga till intelligenta sökfunktioner till en webbsida.

1.  I `ConfigureServices`-metoden i *Startup.cs* lägger du till ett anrop till `IServiceCollection.AddSingleton`. På så sätt blir konfigurationsobjektet som innehåller de viktiga inställningarna tillgängligt för koden i projektet.
 
   ```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }
   ```


1. Lägg till en ny klassfil under mappen **Models** med namnet *BingNewsModel.cs*. Om du har namngett ditt projekt på olika sätt använder du ditt eget projekts namnområde i stället för MyWebApplication. Ersätt innehållet med följande kod:
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   Den här modellen används för att lagra resultatet av ett anrop till tjänsten för nyhetssökning i Bing.
 
1. I mappen **Controllers** lägger du till en ny klassfil med namnet *IntelligentSearchController.cs*. Ersätt innehållet med följande kod:

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   I den här koden konfigurerar konstruktorn konfigurationsobjektet som innehåller dina nycklar. Metoden för vägen `Search` är bara en omdirigering till funktionen `BingSearchResult`. Detta anropar metoden `GetNewsSearchClient` för att hämta klientobjektet `NewsSearchAPI`.  Klientobjektet `NewsSearchAPI` innehåller metoden `SearchAsync` som faktiskt anropar tjänsten och returnerar resultaten i `SearchResult`-modellen som du nyss skapade. 

1. Lägg till en klass, `MyHandler`, som refererades i föregående kod. Det här delegerar det asynkrona anropet till söktjänsten till dess basklass, `DelegatingHandler`.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. För att lägga till stöd för att skicka sökningar och för att visa resultatet går du till mappen **Vyer** och skapar en ny mapp med namnet **IntelligentSearch**. Lägg till en vy i den nya mappen, *BingSearchResult.cshtml*. Kopiera följande kod:

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Starta webbappen lokalt, ange webbadressen för sidan du just skapat (/ IntelligentSearch/BingSearchResult) och publicera en sökbegäran genom att använda sökknappen.

   ![Skärmbild av resultat för nyhetssökning i Bing](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs kan du ta bort den. Detta tar bort den kognitiva tjänsten och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. Välj den resursgrupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**.
3. I rutan för att **skriva resursgruppsnamnet** anger du namnet på resursgruppen och väljer **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Läs mer om API för nyhetssökning i Bing i [Vad är nyhetssökning i Bing?](index.yml).
