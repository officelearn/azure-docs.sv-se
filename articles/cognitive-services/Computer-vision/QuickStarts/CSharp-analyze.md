---
title: 'Snabb start: analysera en lokal avbildning – REST, C #'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten får du analysera en lokal bild med hjälp av API för visuellt innehåll med C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c29aa2fa229ac95269d269f2eaaa7542eee2860f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014211"
---
# <a name="quickstart-analyze-a-local-image-using-the-computer-vision-rest-api-and-c"></a>Snabb start: analysera en lokal avbildning med hjälp av Visuellt innehåll REST API och C #

I den här snabb starten ska du analysera en lokalt lagrad bild för att extrahera visuella funktioner med hjälp av Visuellt innehåll REST API. Med metoden [analysera avbildning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) kan du extrahera visuell funktions information från bild innehåll.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Du måste ha [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) eller senare
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* [Skapa miljövariabler](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för nyckel-och slut punkts-URL: en, med namnet respektive `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

## <a name="create-and-run-the-sample-application"></a>Skapa och kör exempelappen

Skapa exemplet i Visual Studio enligt följande:

1. Skapa en ny Visual Studio-lösning/projekt i Visual Studio med hjälp av mallen Visual C#-konsolen (.NET Core Framework).
1. Installera NuGet-paketet Newtonsoft.Json.
    1. Klicka på **Verktyg** på menyn, välj **NuGet Package Manager** (NuGet-pakethanteraren) och välj sedan **Manage NuGet Packages for Solution** (Hantera NuGet-paket för lösning).
    1. Klicka på fliken **Bläddra** . i **sökrutan skriver** du Newtonsoft.Jspå (om den inte redan visas).
    1. Välj **Newtonsoft.Jspå** och klicka sedan på kryss rutan bredvid ditt projekt namn och **Installera**.
1. Kopiera/klistra in exempel kod avsnittet nedan i din Program.cs-fil. Justera namn områdets namn om det skiljer sig från det som du skapade.
1. Lägg till en bild som du väljer i mappen bin/debug/netcoreappX. X och Lägg sedan till avbildnings namnet (med tillägget) i variabeln ' imageFilePath '.
1. Kör programmet.

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
        // Add your Computer Vision subscription key and endpoint to your environment variables.
        static string subscriptionKey = Environment.GetEnvironmentVariable("COMPUTER_VISION_SUBSCRIPTION_KEY");

        static string endpoint = Environment.GetEnvironmentVariable("COMPUTER_VISION_ENDPOINT");
        
        // the Analyze method endpoint
        static string uriBase = endpoint + "vision/v3.1/analyze";

        // Image you want analyzed (add to your bin/debug/netcoreappX.X folder)
        // For sample images, download one from here (png or jpg):
        // https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/ComputerVision/Images
        static string imageFilePath = @"my-sample-image";

        public static void Main()
        {
            // Call the API
            MakeAnalysisRequest(imageFilePath).Wait();

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

Ett lyckat svar returneras i JSON (baserat på din egen avbildning som används) i konsol fönstret, som liknar följande exempel:

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

## <a name="next-steps"></a>Nästa steg

Utforska ett grundläggande Windows-program som använder Visuellt innehåll för att utföra optisk teckenläsning (OCR), skapa miniatyrbilder med smart beskärning och identifiera, kategorisera, tagga och beskriv visuella funktioner, inklusive ansikten, i en bild.

> [!div class="nextstepaction"]
> [API för visuellt innehåll med C# – Självstudie](../Tutorials/CSharpTutorial.md)