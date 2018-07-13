---
title: Bing News Search C#-självstudie | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ansluta till Cognitive Services Bing-nyhetssökning från en ASP.NET Core-webbapp.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 5cfa82067d28b05f32bd87e0e83d55a03da8d508
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665222"
---
# <a name="connect-to-bing-news-search-api-by-using-connected-services-in-visual-studio"></a>Ansluta till nyhetssökning i Bing med Connected Services i Visual Studio

Du kan aktivera appar och tjänster att dra nytta av en annonsfri sökmotor omfång på webben med hjälp av Sök på Bing News. Nyhetssökning i Bing är ett av söktjänster som är tillgängliga med Cognitive Services.

Den här artikeln innehåller information för att använda Visual Studio Connected Service-funktionen för nyhetssökning i Bing. Funktionen är tillgänglig i Visual Studio 2017 15.7 eller senare, med Cognitive Services-tillägget installerat.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 version 15.7, med arbetsbelastningen webbutveckling installerad. [Hämta nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Lägger till stöd i ditt projekt för nyhetssökning i Bing

1. Skapa ett nytt webbprojekt i ASP.NET Core med namnet MyWebApplication. Använd den **webbprogram (Model-View-Controller)** projektmall med alla standardinställningar. Det är viktigt att namnge projektet MyWebApplication, så att namnområdet matchar när du kopierar koden i projektet. 

1. I **Solution Explorer**, Välj **Lägg till** > **Connected Service**.
   Connected Service-sidan visas med tjänster som du kan lägga till i projektet.

   ![Skärmbild av Lägg till Connected Service menyalternativ](../media/vs-common/Connected-Service-Menu.PNG)

1. Meny med olika tjänster, Välj **ta med intelligenta sökning till dina appar**.

   ![Skärmbild av lista över anslutna tjänster](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Om du har loggat in Visual Studio och Azure-prenumeration som är associerade med ditt konto, visas en sida med en listruta med dina prenumerationer. Välj den prenumeration som du vill använda och välj sedan ett namn för den nyhetssökning i Bing. Du kan också välja **redigera** att ändra det automatiskt genererade namnet.

   ![Skärmbild av prenumerationen och namn på fält](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Välj resursgruppen och prisnivå.

   ![Skärmbild av resursgrupp och priser nivån fält](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Om du vill veta mer om prisnivåerna väljer **granska priser**.

1. Välj **Lägg till** att lägga till stöd för tjänsten är ansluten.
   Visual Studio ändrar ditt projekt för att lägga till NuGet-paket, poster i konfigurationsfilen och andra ändringar till stöd för en anslutning till den nyhetssökning i Bing. Utdata visar loggen för vad som händer i projektet. Du bör se något som liknar följande:

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
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Använd den nyhetssökning i Bing för att lägga till sökfunktioner till en webbsida

Nu när du har lagt till stöd för den nyhetssökning i Bing i projektet, är här hur du använder API: et för att lägga till intelligenta sökfunktioner till en webbsida.

1.  I *Startup.cs*i den `ConfigureServices` metod, lägga till ett anrop till `IServiceCollection.AddSingleton`. På så sätt blir det konfigurationsobjekt som innehåller de viktiga inställningarna som är tillgängliga för koden i projektet.
 
   ```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }
   ```


1. Lägg till en ny klassfil under den **modeller** mapp med namnet *BingNewsModel.cs*. Om du har gett ditt projekt på olika sätt använder du ditt eget projekt namnområde, i stället för MyWebApplication. Ersätt innehållet med följande kod:
 
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
 
1. I den **domänkontrollanter** mapp, lägga till en ny klassfil som kallas *IntelligentSearchController.cs*. Ersätt innehållet med följande kod:

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

   I den här koden konfigurerar konstruktorn konfigurationsobjekt som innehåller dina nycklar. Metoden för den `Search` vägen är bara en omdirigering till den `BingSearchResult` funktion. Detta anropar den `GetNewsSearchClient` metod för att hämta den `NewsSearchAPI` klientobjektet.  Den `NewsSearchAPI` klientobjektet innehåller den `SearchAsync` metod som faktiskt anropar tjänsten och returnerar resultaten i den `SearchResult` modellen som du nyss skapade. 

1. Lägg till en klass `MyHandler`, som refererades i föregående kod. Det här delegerar asynkront anrop till search-tjänst till dess basklass `DelegatingHandler`.

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

1. Du lägger till stöd för att skicka sökningar och visa resultaten i den **vyer** mapp, skapa en ny mapp med namnet **IntelligentSearch**. Lägg till en vy i den nya mappen *BingSearchResult.cshtml*. Kopiera in följande kod:

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

1. Starta webbappen lokalt, ange Webbadressen för sidan du just skapat (/ IntelligentSearch/BingSearchResult), och publicera en sökbegäran genom att använda på sökknappen.

   ![Skärmbild av nyhetssökning i Bing-resultat](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs kan du ta bort den. Detta tar bort cognitive service och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Ange namnet på resursgruppen i sökrutan överst på portalen. Välj den resursgrupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**.
3. I den **Skriv resursgruppens namn** rutan, ange namnet på resursgruppen och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

Läs mer om den nyhetssökning i Bing i [vad är nyhetssökning i Bing?](index.yml).
