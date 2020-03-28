---
title: 'Snabbstart: Få bildinsikter med REST API och C# - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar upp en bild till API:et för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 07ecac46ab13058d308c17c5747701ee5ed577fc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446676"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Snabbstart: Hämta bildinsikter med hjälp av Bing Visual Search REST API och C #

Den här snabbstarten visar hur du laddar upp en bild till API:et för visuell sökning i Bing och visar de insikter som returneras.

## <a name="prerequisites"></a>Krav

* Alla utgåvor av [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Den [Json.NET ramen](https://www.newtonsoft.com/json), finns som en NuGet paket.
* Om du använder Linux/MacOS kan du köra det här programmet med [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa en ny konsollösning med namnet BingSearchApisQuickStart i Visual Studio. Lägg till följande namnområden i huvudkodfilen:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Lägg till variabler för din prenumerationsnyckel, slutpunkt och sökväg till den bild du vill överföra. `uriBase`kan vara den globala slutpunkten nedan eller den [anpassade underdomänslutpunkten](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure-portalen för din resurs:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Skapa en `GetImageFileName()` metod som heter för att hämta sökvägen till bilden:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Skapa en metod för att hämta den binära data av bilden:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Skapa formulärdata

Om du vill ladda upp en lokal avbildning skapar du först formulärdata som ska skickas till API:et. Formulärdata måste innehålla `Content-Disposition` huvudet, `name` dess parameter måste anges till `filename` "image", och parametern kan ställas in på valfri sträng. Innehållet i formuläret innehåller den binära data i bilden. Den maximala bildstorleken du kan ladda upp är 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Lägg till gränssträngar för att formatera POST-formulärdata. Gränssträngar bestämmer start-, slut- och nyradstecken för data:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Använd följande variabler för att lägga till parametrar i formulärdata:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Skapa en `BuildFormDataStart()` funktion med namnet för att skapa början av formulärdata med hjälp av gränssträngar och bildsökväg:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Skapa en `BuildFormDataEnd()` funktion med namnet för att skapa slutet av formulärdata med hjälp av gränssträngarna:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Anropa API för visuell sökning i Bing

1. Skapa en funktion för att anropa slutpunkten Bing Visual Search och returnera JSON-svaret. Funktionen startar och avslutar formulärdata, en bytematris som innehåller `contentType` bilddata och ett värde.

2. Använd en `WebRequest` för att lagra URI, contentType-värde och rubriker.  

3. Används `request.GetRequestStream()` för att skriva dina formulär- och bilddata och hämta sedan svaret. Din funktion bör likna den nedan:
        
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

## <a name="create-the-main-method"></a>Skapa huvudmetoden

1. I `Main` metoden för ditt program, hämta filnamn och binära data i din bild:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Ställ in POST-texten genom att formatera gränsen för den. Ring `startFormData()` sedan `endFormData` och skapa formulärdata:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Skapa `ContentType` värdet genom `CONTENT_TYPE_HEADER_PARAMS` att formatera och formulärdatagränsen:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Hämta API-svaret `BingImageSearch()` genom att ringa och skriva ut svaret:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Använda HttpClient

Om du `HttpClient`använder kan `MultipartFormDataContent` du använda klassen för att skapa formulärdata. Använd bara följande kodavsnitt för att ersätta motsvarande metoder i föregående exempel.

Ersätt `Main` metoden med den här koden:

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

Ersätt `BingImageSearch` metoden med den här koden:

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
> [Skapa en ensidig visuell sökwebbapp](../tutorial-bing-visual-search-single-page-app.md)
