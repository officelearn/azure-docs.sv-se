---
title: Text Analytics C#-självstudie | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ansluta till textanalys från en ASP.NET Core-webbapp.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: ghogen
ms.openlocfilehash: c97f75e0a41a4bf314963dc26c6424a0b773822b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665236"
---
# <a name="connect-to-the-text-analytics-service-by-using-connected-services-in-visual-studio"></a>Ansluta till Text Analytics-tjänsten med hjälp av Connected Services i Visual Studio

Med hjälp av Text Analytics-tjänsten, kan du extrahera innehållsrik information för att kategorisera och bearbeta visuella data och utföra datorstödd moderering av bilder hjälper dig att moderera dina tjänster.

Den här artikeln och dess tillhörande artiklar innehåller information för att använda Visual Studio Connected Service-funktionen för Text Analytics-tjänsten. Funktionen är tillgänglig i både Visual Studio 2017 15.7 eller senare, med Cognitive Services-tillägget installerat.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 version 15.7, med arbetsbelastningen webbutveckling installerad. [Hämta nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Lägger till stöd i ditt projekt för Text Analytics-tjänsten

1. Skapa ett nytt webbprojekt i ASP.NET Core kallas TextAnalyticsDemo. Använd projektmall för webbprogram (Model-View-Controller) med alla standardinställningar. Det är viktigt att namnge projektet MyWebApplication, så att namnområdet matchar när du kopierar koden i projektet.  Exemplet i den här artikeln använder MVC, men du kan använda Text Analytics Connected Service med valfri typ för ASP.NET-projekt.

1. I **Solution Explorer**, dubbelklicka på den **Connected Service** objekt.
   Connected Service-sidan visas med tjänster som du kan lägga till i projektet.

   ![Skärmbild av Connected Service i Solution Explorer](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. Meny med olika tjänster, Välj **utvärdera åsikter med textanalys**.

   ![Skärmbild av Connected Services skärmen](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Om du har loggat in Visual Studio och Azure-prenumeration som är associerade med ditt konto, visas en sida med en listruta med dina prenumerationer.

   ![Skärmbild av Text Analytics Connected Service-skärmen](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Välj den prenumeration som du vill använda, Välj ett namn för Text Analytics-tjänsten och välj den **redigera** länk för att ändra det automatiskt genererade namnet, väljer du resursgruppen och den prisnivå.

   ![Skärmbild av resursgrupp och priser nivån fält](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Följ länken för mer information på prisnivå.

1. Välj **Lägg till** att lägga till stöd för tjänsten är ansluten.
   Visual Studio ändrar ditt projekt för att lägga till NuGet-paket, poster i konfigurationsfilen och andra ändringar till stöd för en anslutning till Text Analytics-tjänsten. Den **utdatafönstret** innehåller en logg över vad som händer i projektet. Du bör se något som liknar följande:

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Använda Text Analytics-tjänsten för att identifiera språket för en text-exemplet.

1. Lägg till följande using-satser i Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Lägg till ett fält för konfiguration och Lägg till en konstruktor som initierar fältet configuration i Start-klassen för att aktivera konfiguration i ditt program.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Lägg till en klassfil i mappen domänkontrollanter kallas DemoTextAnalyzeController och Ersätt innehållet med följande kod:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    Koden innehåller GetTextAnalyzeClient för att hämta objektet som du kan använda för att anropa API för textanalys och en hanterare som anropar DetectLanguage på en angiven text.

1. Lägg till MyHandler fonthelper-klass som används av föregående kod.

    ```csharp
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

1. Lägg till en klass för modellen i mappen modeller.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. Lägg till den vy som visar Analyserad text, identifiera språk och riskpoäng som representerar konfidensnivån i analysen.
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. Skapa och köra exemplet lokalt. Ange text och se vilka språk som Text Analytics identifierar.
   
## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort resursgruppen. Detta tar bort cognitive service och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här självstudien i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Läs mer om Text Analytics-tjänsten genom att läsa den [dokumentation om textanalys Service](index.yml).
