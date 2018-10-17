---
title: 'Snabbstart: Analysera en lokal bild – REST, C# – Visuellt innehåll'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten får du analysera en lokal bild med hjälp av API för visuellt innehåll med C#.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: v-deken
ms.openlocfilehash: 572d352b04debca0ab3afdea6d86a7383370050f
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634123"
---
# <a name="quickstart-analyze-a-local-image-using-the-rest-api-and-c35-in-computer-vision"></a>Snabbstart: Analysera en lokal bild med hjälp av REST-API:et och C&#35; i Visuellt innehåll

I den här snabbstarten får du analysera en lokal bild för att extrahera visuella funktioner med hjälp av REST-API:et för visuellt innehåll. Med metoden [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (Analysera bild) kan du extrahera visuella funktioner baserat på bildinnehåll.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) eller senare.
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan skaffa en prenumerationsnyckel genom att följa anvisningarna i [Skaffa prenumerationsnycklar](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample-application"></a>Skapa och kör exempelappen

Om du vill köra exemplet i Visual Studio så vidta följande steg:

1. Skapa en ny Visual Studio-lösning i Visual Studio med Visual C#-konsolappsmallen.
1. Installera NuGet-paketet Newtonsoft.Json.
    1. Klicka på **Verktyg** på menyn, välj **NuGet Package Manager** (NuGet-pakethanteraren) och välj sedan **Manage NuGet Packages for Solution** (Hantera NuGet-paket för lösning).
    1. Klicka på fliken **Bläddra** och skriv ”Newtonsoft.Json” i rutan **Sök**.
    1. Välj **Newtonsoft.Json** när det visas och klicka på kryssrutan bredvid namnet på ditt projekt och sedan på **Installera**.
1. Ersätt koden i `Program.cs` med följande kod och gör sedan följande ändringar i koden där det behövs:
    1. Ersätt värdet för `subscriptionKey` med din prenumerationsnyckel.
    1. Ersätt värdet för `uriBase` med slutpunktsadressen för metoden [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) från Azure-regionen där du fått dina prenumerationsnycklar om det behövs.
1. Kör programmet.
1. Ange sökvägen till en lokal bild i kommandotolken.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the West Central US region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Analyze an image:");
            Console.Write(
                "Enter the path to the image you wish to analyze: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeAnalysisRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the analysis of the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to analyze.</param>
        static async Task MakeAnalysisRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. A third optional parameter is "details".
                // The Analyze Image method returns information about the following
                // visual features:
                // Categories:  categorizes image content according to a
                //              taxonomy defined in documentation.
                // Description: describes the image content with a complete
                //              sentence in supported languages.
                // Color:       determines the accent color, dominant color, 
                //              and whether an image is black & white.
                string requestParameters =
                    "visualFeatures=Categories,Description,Color";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Read the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Add the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Asynchronously call the REST API method.
                    response = await client.PostAsync(uri, content);
                }

                // Asynchronously get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="examine-the-response"></a>Granska svaret

Ett svar som anger att åtgärden lyckades returneras i JSON. Exempelprogrammet tolkar och visar ett lyckat svar i konsolfönstret liknar följande exempel:

```json
{
    "categories": [
        {
            "name": "abstract_",
            "score": 0.00390625
        },
        {
            "name": "others_",
            "score": 0.0234375
        },
        {
            "name": "outdoor_",
            "score": 0.00390625
        }
    ],
    "description": {
        "tags": [
            "road",
            "building",
            "outdoor",
            "street",
            "night",
            "black",
            "city",
            "white",
            "light",
            "sitting",
            "riding",
            "man",
            "side",
            "empty",
            "rain",
            "corner",
            "traffic",
            "lit",
            "hydrant",
            "stop",
            "board",
            "parked",
            "bus",
            "tall"
        ],
        "captions": [
            {
                "text": "a close up of an empty city street at night",
                "confidence": 0.7965622853462756
            }
        ]
    },
    "requestId": "dddf1ac9-7e66-4c47-bdef-222f3fe5aa23",
    "metadata": {
        "width": 3733,
        "height": 1986,
        "format": "Jpeg"
    },
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": [
            "Black",
            "Grey"
        ],
        "accentColor": "666666",
        "isBWImg": true
    }
}
```

## <a name="clean-up-resources"></a>Rensa resurser

När Visual Studio-lösningen inte längre behövs kan du ta bort den. Gör det genom att öppna Utforskaren, navigera till mappen där du skapade Visual Studio-lösningen och ta bort mappen.

## <a name="next-steps"></a>Nästa steg

Utforska ett grundläggande Windows-program som använder Visuellt innehåll för att utföra optisk teckenläsning (OCR), skapa miniatyrbilder med smart beskärning och identifiera, kategorisera, tagga och beskriv visuella funktioner, inklusive ansikten, i en bild. Du kan snabbt experimentera med API:erna för visuellt innehåll genom att prova [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Självstudie: API för visuellt innehåll med C&#35;](../Tutorials/CSharpTutorial.md)