---
title: 'Ansikts-API: et C#-självstudie | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Skapa en enkel Windows-app som använder kognitiva tjänster Ansikts-API för att identifiera funktioner i ansikten i en bild.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ghogen
ms.openlocfilehash: b51760f889db27aa25e54582070ee7d3adcf66f8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665243"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Ansluta till Cognitive Services, Ansikts-API med Connected Services i Visual Studio

Med hjälp av Cognitive Services Ansikts-API kan du identifiera, analysera, organisera och tagga ansikten i foton.

Den här artikeln och dess tillhörande artiklar innehåller information för att använda Visual Studio Connected Service-funktionen för Cognitive Services, Ansikts-API. Funktionen är tillgänglig i både Visual Studio 2017 15.7 eller senare, med Cognitive Services-tillägget installerat.

## <a name="prerequisites"></a>Förutsättningar

- **En Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 version 15.7** med den **webbutveckling** arbetsbelastning som är installerad. [Hämta nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>Skapa ett projekt och lägga till stöd för Cognitive Services, Ansikts-API

1. Skapa ett nytt webbprojekt i ASP.NET Core. Med mallen tomt projekt. 

1. I **Solution Explorer**, Välj **Lägg till** > **Connected Service**.
   Connected Service-sidan visas med tjänster som du kan lägga till i projektet.

   ![Lägg till Connected Service menyobjekt](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. Meny med olika tjänster, Välj **Cognitive Services, Ansikts-API**.

   ![Välj tjänsten för att ansluta till](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Om du har loggat in Visual Studio och Azure-prenumeration som är associerade med ditt konto, visas en sida med en listruta med dina prenumerationer.

   ![Välj din prenumeration](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Välj den prenumeration som du vill använda och välj sedan ett namn för Ansikts-API eller Välj länken Redigera för att ändra det automatiskt genererade namnet, väljer du resursgruppen och den prisnivå.

   ![Redigera anslutna tjänstinformation](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Följ länken för mer information på prisnivå.

1. Välj Lägg till att lägga till stöds för Connected Service.
   Visual Studio ändrar ditt projekt för att lägga till NuGet-paket, poster i konfigurationsfilen och andra ändringar till stöd för en anslutning Ansikts-API.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Använda Ansikts-API för att identifiera attribut för ansikten i en bild

1. Lägg till följande using-satser i Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Lägg till ett fält för konfiguration och Lägg till en konstruktor som initierar fältet configuration i Start-klassen för att aktivera konfiguration i ditt program.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Lägg till en bildmapp Wwwroot-mappen i projektet och lägga till en fil i Wwwroot-mappen. Exempelvis kan du använda en avbildning på den här [Ansikts-API-sidan](https://azure.microsoft.com/services/cognitive-services/face/). Högerklicka på en av avbildningarna, spara till den lokala hårddisken och sedan i Solution Explorer högerklickar du på bildmappen och choosee **Lägg till** > **befintligt objekt** att lägga till den i projektet. Ditt projekt bör se ut ungefär så här i Solution Explorer:
 
   ![bildmappen med bildfil](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Högerklicka på bildfilen, välja egenskaper och välj sedan **kopiera om nyare**.

   ![Kopiera om nyare](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Ersätt metoden konfigurera med följande kod för att komma åt Ansikts-API och testa en bild. Ändra imagePath strängen till rätt sökväg och filnamn för ansikts-avbildningen.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    Koden i det här steget skapar en HTTP-begäran med ett anrop till Ansikts-REST API, med hjälp av den nyckel som du har lagt till när du har lagt till den anslutna tjänsten.

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

1. Kör webbappen och se vad Ansikts-API som finns i avbildningen.
 
   ![Ansikts-API-avbildningen och formaterade resultat](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort resursgruppen. Detta tar bort cognitive service och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
1. Välj **Ta bort resursgrupp**.
1. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Läs mer om Ansikts-API genom att läsa den [Ansikts-API-dokumentation](Overview.md).
