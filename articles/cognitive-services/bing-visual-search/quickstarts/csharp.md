---
title: 'Snabb start: Hämta bild insikter med hjälp av REST API och C#-Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du laddar upp en avbildning med hjälp av API för visuell sökning i Bing och C# och få insikter om avbildningen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-csharp
ms.openlocfilehash: c39f1eeda5ccdfb72a58f5c082d14100812ec0fa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075039"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Snabb start: Hämta bild insikter med hjälp av Visuell sökning i Bing REST API och C #

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Den här snabb starten visar hur du laddar upp en avbildning till API för visuell sökning i Bing och visar de insikter som returneras.

## <a name="prerequisites"></a>Förutsättningar

* En version av [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* [JSON.net-ramverket](https://www.newtonsoft.com/json), som är tillgängligt som ett NuGet-paket.
* Om du använder Linux/MacOS kan du köra det här programmet med [mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa en ny konsol lösning med namnet BingSearchApisQuickStart i Visual Studio. Lägg till följande namn områden i huvud kod filen:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Lägg till variabler för prenumerations nyckeln, slut punkten och sökvägen till den avbildning som du vill ladda upp. För `uriBase` värdet kan du använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Skapa en metod med namnet `GetImageFileName()` för att hämta sökvägen till din avbildning.
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Skapa en metod för att hämta binära data för avbildningen.

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Skapa formulärdata

1. Om du vill överföra en lokal avbildning skapar du först formulär data att skicka till API: et. Formulär data inkluderar `Content-Disposition` sidhuvudet, `name` parametern har angetts till "image" och `filename` parametern anges till fil namnet på avbildningen. Innehållet i formuläret innehåller binära data för bilden. Den maximala bild storlek som du kan ladda upp är 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

2. Lägg till sträng strängar för att formatera POST formulär data. Avgränsnings strängarna bestämmer Start-, slut-och rad matnings tecken för data.

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

3. Använd följande variabler för att lägga till parametrar i formulär data:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

4. Skapa en funktion med namnet `BuildFormDataStart()` för att skapa början av formulär data med hjälp av gränser-strängar och bild Sök väg.
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

5. Skapa en funktion som heter `BuildFormDataEnd()` för att skapa slutet av formulär data med hjälp av gränser-strängarna.
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Anropa API för visuell sökning i Bing

1. Skapa en funktion för att anropa Visuell sökning i Bing-slutpunkten och returnera JSON-svaret. Funktionen tar början och slutet av formulär data, en byte mat ris som innehåller bilddata och ett `contentType` värde.

2. Använd en `WebRequest` för att lagra URI, contentType-värde och rubriker.  

3. Använd `request.GetRequestStream()` för att skriva dina formulär-och bilddata och hämta sedan svaret. Funktionen bör likna följande kod:
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>Skapa main-metoden

1. `Main()`Hämta fil namnet och binära data för din avbildning i-metoden för ditt program.

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Ställ in INLÄGGs texten genom att formatera gränsen. Anropa sedan `BuildFormDataStart()` och `BuildFormDataEnd()` skapa formulär data.

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Skapa `ContentType` värdet med formatering `CONTENT_TYPE_HEADER_PARAMS` och formulärets data gränser.

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Hämta API-svaret genom att anropa `BingImageSearch()` och skriv sedan ut svaret.

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Använda HttpClient

Om du använder `HttpClient` kan du använda- `MultipartFormDataContent` klassen för att skapa formulär data. Använd följande avsnitt med kod för att ersätta motsvarande metoder i föregående exempel:

1. Ersätt metoden `Main()` med följande kod:

   ```csharp
           static void Main()
           {
               try
               {
                   Console.OutputEncoding = System.Text.Encoding.UTF8;

                   if (accessKey.Length == 32)
                   {
                       if (IsImagePathSet(imagePath))
                       {
                           var filename = GetImageFileName(imagePath);
                           Console.WriteLine("Getting image insights for image: " + filename);
                           var imageBinary = GetImageBinary(imagePath);

                           var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                           var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                           Console.WriteLine("\nJSON Response:\n");
                           Console.WriteLine(JsonPrettyPrint(json));
                       }
                   }
                   else
                   {
                       Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                       Console.WriteLine("Please paste yours into the source code.");
                   }

                   Console.Write("\nPress Enter to exit ");
                   Console.ReadLine();
               }
               catch (Exception e)
               {
                   Console.WriteLine(e.Message);
               }
           }
   ```

2. Ersätt metoden `BingImageSearch()` med följande kod:

   ```csharp
           /// <summary>
           /// Calls the Bing visual search endpoint and returns the JSON response.
           /// </summary>
           static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
           {
               var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
               requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

               var content = new MultipartFormDataContent(boundary);
               content.Add(new ByteArrayContent(image), "image", "myimage");
               requestMessage.Content = content;

               var httpClient = new HttpClient();

               Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
               HttpResponseMessage httpResponse = httpRequest.Result;
               HttpStatusCode statusCode = httpResponse.StatusCode;
               HttpContent responseContent = httpResponse.Content;

               string json = null;

               if (responseContent != null)
               {
                   Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                   json = stringContentsTask.Result;
               }

               return json;
           }
   ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en Visuell sökning webb program med en enda sida](../tutorial-bing-visual-search-single-page-app.md)
