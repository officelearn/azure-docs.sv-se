---
title: Ansluten tjänst för Visual Studio – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Ansluta till API:et för visuellt innehåll från en ASP.NET Core-webbapp med Visual Studio Connected Service-funktionen.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: 4911a872a4b5479abe84c2e06573256092185cf2
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669709"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Använda Connected Services i Visual Studio för att ansluta till API för visuellt innehåll

Genom att använda Cognitive Services API för visuellt innehåll kan du extrahera information att kategorisera och bearbeta visuella data, utföra datorstödd moderering av bilder som hjälper dig att moderera dina tjänster.

Den här artikeln, och dess relaterade artiklar, innehåller information om hur man använder Visual Studios Connected Service-funktion för Cognitive Services API för visuellt innehåll. Funktionen är tillgänglig i Visual Studio 2017 15.7 och senare med Cognitive Services-tillägget installerat.

## <a name="prerequisites"></a>Nödvändiga komponenter

- **En Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 version 15.7** med arbetsbelastningen **Webbutveckling** installerad. [Ladda ned det nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Lägg till stöd för ditt projekt för Cognitive Services API för visuellt innehåll

1. Skapa ett nytt ASP.NET Core-webbprojekt. Använd den tomma projektmallen. 

1. I **Solution Explorer** väljer du **Lägg till** > **Ansluten tjänst**.
   Sidan Ansluten tjänst visas med tjänster som du kan lägga till i projektet.

   ![Skärmbild av en snabbmeny för ett Visual Studio-projekt: Lägg till > Ansluten tjänst](../media/vs-common/Connected-Service-Menu.PNG)

1. På menyn med tillgängliga tjänster väljer du **Cognitive Services API för visuellt innehåll**.

   ![menyn Anslutna tjänster, markera Analysera bilder med visuellt innehåll](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Om du har loggat in till Visual Studio och har en Azure-prenumeration som är associerad med ditt konto, visas en sida med en listruta med dina prenumerationer.

   ![Ett Visual Studio-fönster för ”API för visuellt innehåll” med listrutan Prenumeration markerad](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Välj den prenumeration som du vill använda, välj ett namn för API:et för visuellt innehåll och välj sedan länken Redigera för att ändra det automatiskt genererade namnet. Välj sedan resursgrupp och prisnivå.

   ![Redigera information om ansluten tjänst](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Följ länken om du vill ha mer information om prisnivåer.

1. Välj Lägg till för att lägga till sådana som stöds för den anslutna tjänsten.
   Visual Studio ändrar ditt projekt för att lägga till NuGet-paketen, konfigurationsfilposter och andra ändringar till stöd för en anslutning till API för visuellt innehåll. Utdatafönstret innehåller en logg över vad som händer i projektet. Det ser ut ungefär så här:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Använd API:et för visuellt innehåll för att identifiera attribut för en bild

1. Lägg till nedanstående med hjälp av instruktionerna i Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Lägg till ett konfigurationsfält och lägg till en konstruktor som initierar konfigurationsfältet i `Startup`-klassen, så att konfiguration aktiveras i programmet.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Lägg till en bildfil i mappen wwwroot i projektet och lägg till en bildfil i din wwwroot-mapp. Som exempel kan du använda en av bilderna på den här [API för visuellt innehåll-sidan](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Högerklicka på en av bilderna, spara den på den lokala hårddisken och högerklicka sedan på bildmappen i Solution Explorer och välj **Lägg till** > **Befintligt objekt** för att lägga till den i projektet. Ditt projekt bör se ut ungefär så här i Solution Explorer: 
  
   ![En skärmbild av Solution Explorer-vyn med en vald avbildningsfil](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Högerklicka på bildfilen, välj Egenskaper och välj sedan **Kopiera om nyare**. 

   ![Ett fönster för avbildningsegenskaper; Kopiera till utdatakatalog har ställts in på Kopiera om nyare](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Ersätt konfigurationsmetoden med följande kod för att komma åt API:et för visuellt innehåll och testa en bild.

   ```csharp
        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site. 
            string imagePath = @"images/subway.png";

            // Enable static files such as image files. 
            app.UseStaticFiles();

            string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
            string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

            // Assemble the URI for the REST API Call.
            string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

            HttpResponseMessage response;

            // Request body. Posts an image you've added to your site's images folder. 
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentString = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentString = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
                await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentString));
                await context.Response.WriteAsync("<p>");
            });
        }

   ```
    Koden här skapar en HTTP-begäran med URI:et och bilden som binärt innehåll för ett anrop till REST API för visuellt innehåll.

1. Lägg till hjälpfunktionerna GetImageAsByteArray och JsonPrettyPrint.

   ```csharp
        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            string INDENT_STRING = "    ";
            var indent = 0;
            var quoted = false;
            var sb = new StringBuilder();
            for (var i = 0; i < json.Length; i++)
            {
                var ch = json[i];
                switch (ch)
                {
                    case '{':
                    case '[':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case '}':
                    case ']':
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        sb.Append(ch);
                        break;
                    case '"':
                        sb.Append(ch);
                        bool escaped = false;
                        var index = i;
                        while (index > 0 && json[--index] == '\\')
                            escaped = !escaped;
                        if (!escaped)
                            quoted = !quoted;
                        break;
                    case ',':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case ':':
                        sb.Append(ch);
                        if (!quoted)
                            sb.Append(" ");
                        break;
                    default:
                        sb.Append(ch);
                        break;
                }
            }
            return sb.ToString();
        }
   ```

1. Kör webbappen och se vad API för visuellt innehåll har hittat i bilden.

   ![Bild och formaterat innehåll för API för visuellt innehåll](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resursgruppen. Detta tar bort den kognitiva tjänsten och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om API:et för visuellt innehåll genom att läsa [dokumentationen om API för visuellt innehåll](Home.md).
