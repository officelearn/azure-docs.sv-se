---
title: 'Snabbstart: Hämta information om bilder med hjälp av REST API för visuell sökning i Bing och C#'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar du upp en bild till API för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 3/28/2019
ms.author: scottwhi
ms.openlocfilehash: d2f5e87bd6c6780e8504abe1753e90eca5db763a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880414"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Snabbstart: Hämta information om bilder med hjälp av REST API för visuell sökning i Bing och C#

Den här snabbstarten visar hur du överför en bild till Bing Visual Search API och visa insikter som returneras.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Valfri version av [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Den [Json.NET framework](https://www.newtonsoft.com/json)är tillgängligt som ett NuGet-paket.
* Om du använder Linux/Mac OS, kan du köra det här programmet med [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa en ny konsol-lösning med namnet BingSearchApisQuickStart i Visual Studio. Lägg till följande namnrymder huvudsakliga kodfilen:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Lägg till variabler för din prenumerationsnyckel, slutpunkt och sökvägen till den avbildning som du vill ladda upp:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Skapa en metod med namnet `GetImageFileName()` att hämta sökvägen för din avbildning:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Skapa en metod för att få den binära data för avbildningen:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Skapa formulärdata

Om du vill överföra en lokal avbildning skapa du först formulärdata som skickas till API: et. Formulärdata måste innehålla den `Content-Disposition` rubrik, dess `name` parametern måste anges till ”bild” och `filename` parameter kan anges till valfri sträng. Formuläret innehåller binära data för avbildningen. Den maximala bildstorlek som du kan ladda upp är 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Lägga till gräns strängar om du vill formatera postdata för formuläret. Gräns strängar fastställer start och end ny rad tecken för data:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Använd följande variabler för att lägga till parametrar i formuläret:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Skapa en funktion med namnet `BuildFormDataStart()` att skapa början av formulärdata med hjälp av gräns strängar och avbildningssökväg:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Skapa en funktion med namnet `BuildFormDataEnd()` att skapa slutet av formulärdata med gräns-strängar:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Anropa API för visuell sökning i Bing

1. Skapa en funktion för att anropa Bing Visual Search-slutpunkten och returnerar JSON-svar. Funktionen tar början och slutet av formuläret, en bytematris som innehåller bilddata och en `contentType` värde.

2. Använd en `WebRequest` för att lagra URI, contentType-värde och rubriker.  

3. Använd `request.GetRequestStream()` att skriva dina formulär- och bildfiler data och få ett svar. Din funktion ska se ut ungefär som den nedan:
        
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

## <a name="create-the-main-method"></a>Skapa Main-metoden

1. I den `Main` metoden för ditt program, hämta filnamn eller binära data i din avbildning:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Ställ in POST-texten genom att formatera gränsen för den. Anropa sedan `startFormData()` och `endFormData` att skapa formuläret:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Skapa den `ContentType` värde genom att formatera `CONTENT_TYPE_HEADER_PARAMS` och webbformulär för data:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Hämta API-svar genom att anropa `BingImageSearch()` och skrivas ut:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Använda HttpClient

Om du använder `HttpClient`, du kan använda den `MultipartFormDataContent` klassen för att skapa formuläret. Använd bara följande delar av kod för att ersätta motsvarande metoderna i föregående exempel.

Ersätt den `Main` metoden med den här koden:

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

Ersätt den `BingImageSearch` metoden med den här koden:

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
> [Skapa en enda sida i Visual Search webbapp](../tutorial-bing-visual-search-single-page-app.md)
