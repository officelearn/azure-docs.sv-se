---
title: 'Självstudier: Ansluta till tjänsten Textanalys med hjälp av Anslutna tjänster i Visual Studio'
titleSuffix: Azure Cognitive Services
description: Lär dig att ansluta till Textanalys från en ASP.NET Core-webbapp.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: a37ea5fdad941e1559e470ccb22303a8e3c7ca3d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243900"
---
# <a name="tutorial-connect-to-the-text-analytics-service-with-connected-services-in-visual-studio"></a>Självstudier: Ansluta till tjänsten Textanalys med hjälp av Anslutna tjänster i Visual Studio

Genom att använda textanalystjänsten kan du extrahera information att kategorisera och bearbeta visuella data, utföra datorstödd moderering av bilder som hjälper dig att moderera dina tjänster.

Den här artikeln, och andra med den sammanhängande artiklar, innehåller information om hur man använder Visual Studio Connected Service-funktionen för textanalystjänsten. Funktionen är tillgänglig i Visual Studio 2017 15.7 och senare med Cognitive Services-tillägget installerat.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure-prenumeration. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 version 15.7 med arbetsbelastningen webbutveckling installerad. [Ladda ned det nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Lägger till stöd i ditt projekt för textanalystjänsten

1. Skapa ett nytt webbprojekt i ASP.NET Core kallat TextAnalyticsDemo. Använd projektmallen för webbprogram (Model-View-Controller) med samtliga standardinställningar. Det är viktigt att ge projektet namnet MyWebApplication, så namnrymden matchar när du kopierar kod till projektet.  Exemplet i den här artikeln använder MVC, men du kan använda Text Analytics Connected Service med valfri typ av ASP.NET-projekt.

1. Dubbelklicka på objektet **Ansluten tjänst**  i **Solution Explorer**.
   Sidan Ansluten tjänst visas med tjänster som du kan lägga till i projektet.

   ![Skärmbild av Ansluten tjänst i Solution Explorer](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. Välj **Utvärdera känsla med textanalys** på menyn med tillgängliga tjänster.

   ![Skärmbild av skärmen Anslutna tjänster](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Om du har loggat in till Visual Studio och har en Azure-prenumeration som är associerade med ditt konto visas en sida med en listruta med dina prenumerationer.

   ![Skärmbild av Ansluten tjänst för textanalys](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Välj den prenumeration som du vill använda, och välj ett namn för textanalystjänsten och välj länken **Redigera** för att ändra det automatiskt genererade namnet. Välj sedan resursgrupp och prisnivå.

   ![Skärmbild av fälten för resursgrupp och prisnivå](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Följ länken om du vill ha mer information om prisnivåer.

1. Välj **Lägg till** om du vill lägga till sådana stöd för den anslutna tjänsten.
   Visual Studio ändrar ditt projekt för att lägga till NuGet-paketen, konfigurationsfilsposter och andra ändringar till stöd för en anslutning till textanalystjänsten. **Utdatafönstret** innehåller en logg över vad som händer i projektet. Det ser ut ungefär så här:

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
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Identifiera textexemplets språk med hjälp av textanalystjänsten.

1. Lägg till följande using-uttryck i Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Lägg till ett konfigurationsfält och lägg till en konstruktor som initierar konfigurationsfältet i startklassen, så att konfiguration aktiveras i programmet.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Lägg till en klassfil i kontrollantmappen som kallas DemoTextAnalyzeController och ersätt dess innehåll med följande kod:

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
    
    Koden innehåller GetTextAnalyzeClient för att kunna hämta det klientobjekt som du kan använda för att anropa API:et för textanalys och en begäranshanterare som anropar DetectLanguage i en angiven text.

1. Lägg till den MyHandler-hjälpklass som används av föregående kod.

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

1. Lägg till en klass för modellen i modellmappen.

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

1. Lägg till en vy som visar den analyserade texten, det identifierade språket och det resultat som representerar analysens förtroendenivå. Gör detta genom att högerklicka på mappen **Vyer**, välja **Lägg till**, och sedan **Visa**. Ange namnet _TextAnalyzeResult_ i den dialogruta som visas, godkänn standardinställningarna för att lägga till en ny fil med namnet _TextAnalyzeResult.cshtml_ i mappen **Vyer** och kopiera följande innehåll till den:
    
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
 
1. Skapa och kör exemplet lokalt. Ange text och se vilka språk som textanalysen identifierar.
   
## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resursgruppen. Detta tar bort den kognitiva tjänsten och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här självstudien bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Läs mer om textanalystjänsten genom att läsa [textanalystjänstdokumentationen](index.yml).
