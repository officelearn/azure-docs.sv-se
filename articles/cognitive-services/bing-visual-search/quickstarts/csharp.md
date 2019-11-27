---
title: 'Snabb start: Hämta bild insikter med hjälp av C# REST API och-visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar du upp en bild till API för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 04/26/2019
ms.author: scottwhi
ms.openlocfilehash: 82c1159aca51bc30839f5380a414bd2b3b488bb8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383641"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Snabb start: Hämta bild insikter med hjälp av Visuell sökning i Bing REST API ochC#

Den här snabb starten visar hur du laddar upp en avbildning till API för visuell sökning i Bing och visar de insikter som returneras.

## <a name="prerequisites"></a>Krav

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

2. Lägg till variabler för din prenumerations nyckel, slut punkt och sökväg till den avbildning som du vill överföra:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Skapa en metod med namnet `GetImageFileName()` för att hämta sökvägen till din avbildning:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Skapa en metod för att hämta binära data för avbildningen:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Skapa formulärdata

Om du vill ladda upp en lokal avbildning skapar du först formulär data att skicka till API: et. Formulär data måste innehålla `Content-Disposition`s huvudet, men dess `name`-parameter måste anges till "bild" och parametern `filename` kan anges till valfri sträng. Innehållet i formuläret innehåller binära data för bilden. Den maximala bild storlek som du kan ladda upp är 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Lägg till sträng strängar för att formatera POST formulär data. Avgränsnings strängar bestämmer Start-, slut-och rad matnings tecken för data:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Använd följande variabler för att lägga till parametrar i formulär data:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Skapa en funktion med namnet `BuildFormDataStart()` för att skapa början av formulär data med hjälp av gränser-strängar och avbildnings Sök väg:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Skapa en funktion med namnet `BuildFormDataEnd()` för att skapa slutet av formulär data med hjälp av gränser-strängarna:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Anropa API för visuell sökning i Bing

1. Skapa en funktion för att anropa Visuell sökning i Bing-slutpunkten och returnera JSON-svaret. Funktionen tar början och slutet av formulär data, en byte mat ris som innehåller bilddata och ett `contentType`-värde.

2. Använd en `WebRequest` för att lagra URI, contentType-värde och rubriker.  

3. Använd `request.GetRequestStream()` för att skriva dina formulär-och bilddata och hämta sedan svaret. Funktionen bör likna den som anges nedan:
        
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

1. Hämta fil namnet och binära data för din avbildning i appens `Main` metod:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Ställ in POST-texten genom att formatera gränsen för den. Anropa sedan `startFormData()` och `endFormData` för att skapa formulär data:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Skapa `ContentType`-värdet genom att formatera `CONTENT_TYPE_HEADER_PARAMS` och formulärets data gränser:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Hämta API-svaret genom att anropa `BingImageSearch()` och skriva ut svaret:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Använda HttpClient

Om du använder `HttpClient`kan du använda klassen `MultipartFormDataContent` för att skapa formulär data. Använd bara följande avsnitt med kod för att ersätta motsvarande metoder i föregående exempel.

Ersätt `Main`-metoden med den här koden:

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

Ersätt `BingImageSearch`-metoden med den här koden:

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
