---
title: Datorn Vision C#-självstudie | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ansluta till Cognitive Services för visuellt innehåll från en ASP.NET Core-webbapp.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 76ca1215144a5caa40971e1eda23f6462f7bf27b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665257"
---
# <a name="connecting-to-cognitive-services-computer-vision-api-by-using-connected-services-in-visual-studio"></a>Ansluta till Cognitive Services API för visuellt innehåll med Connected Services i Visual Studio

Med hjälp av Cognitive Services-datorn Vision API kan du extrahera innehållsrik information för att kategorisera och bearbeta visuella data och utföra datorstödd moderering av bilder hjälper dig att moderera dina tjänster.

Den här artikeln och dess tillhörande artiklar innehåller information för att använda Visual Studio Connected Service-funktionen för Cognitive Services API för visuellt innehåll. Funktionen är tillgänglig i både Visual Studio 2017 15.7 eller senare, med Cognitive Services-tillägget installerat.

## <a name="prerequisites"></a>Förutsättningar

- **En Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 version 15.7** med den **webbutveckling** arbetsbelastning som är installerad. [Hämta nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Lägger till stöd i ditt projekt för Cognitive Services API för visuellt innehåll

1. Skapa ett nytt webbprojekt i ASP.NET Core. Med mallen tomt projekt. 

1. I **Solution Explorer**, Välj **Lägg till** > **Connected Service**.
   Connected Service-sidan visas med tjänster som du kan lägga till i projektet.

   ![Lägg till Connected Service menyobjekt](../media/vs-common/Connected-Service-Menu.PNG)

1. Meny med olika tjänster, Välj **Cognitive Services API för visuellt innehåll**.

   ![Välj tjänsten för att ansluta till](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Om du har loggat in Visual Studio och Azure-prenumeration som är associerade med ditt konto, visas en sida med en listruta med dina prenumerationer.

   ![Välj din prenumeration](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Välj den prenumeration som du vill använda och välj sedan ett namn för den API för visuellt innehåll eller Välj länken Redigera för att ändra det automatiskt genererade namnet, väljer du resursgruppen och den prisnivå.

   ![Redigera anslutna tjänstinformation](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Följ länken för mer information på prisnivå.

1. Välj Lägg till att lägga till stöds för Connected Service.
   Visual Studio ändrar ditt projekt för att lägga till NuGet-paket, poster i konfigurationsfilen och andra ändringar till stöd för en anslutning i API för visuellt innehåll. Utdatafönstret visar loggen för vad som händer i projektet. Du bör se något som liknar följande:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 1.0.2-preview.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Använd API för visuellt innehåll för att identifiera attribut för en avbildning

1. Lägg till följande using-satser i Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Lägg till ett fält för konfiguration och Lägg till en konstruktor som initierar configuration-fältet i den `Startup` klassen för att aktivera konfiguration i ditt program.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Lägg till en bildmapp Wwwroot-mappen i projektet och lägga till en fil i Wwwroot-mappen. Exempelvis kan du använda en avbildning på den här [API för visuellt innehåll sidan](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Högerklicka på en av avbildningarna, spara till den lokala hårddisken och sedan i Solution Explorer högerklickar du på bildmappen och choosee **Lägg till** > **befintligt objekt** att lägga till den i projektet. Ditt projekt bör se ut ungefär så här i Solution Explorer: 
  
   ![bildmappen med bildfil](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Högerklicka på bildfilen, välja egenskaper och välj sedan **kopiera om nyare**. 

   ![Kopiera om nyare](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Ersätt metoden konfigurera med följande kod för att komma åt den API för visuellt innehåll och testa en bild.

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
    Den här koden skapar en HTTP-begäran med URI: N och image som binärt innehåll för ett anrop till den REST API för visuellt innehåll.

1. Lägg till hjälpfunktioner GetImageAsByteArray och JsonPrettyPrint.

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

1. Kör webbappen och se vad API för visuellt innehåll finns i din avbildning.

   ![Avbildning av API för visuellt innehåll och formaterade resultat](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort resursgruppen. Detta tar bort cognitive service och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Läs mer om API för visuellt innehåll genom att läsa den [datorn Vision API-dokumentation](Home.md).
