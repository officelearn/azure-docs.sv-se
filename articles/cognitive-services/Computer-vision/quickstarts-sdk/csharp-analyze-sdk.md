---
title: 'Snabbstart: Analysera en bild – SDK, C# – Visuellt innehåll'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du analysera en bild med hjälp av Windows C#-klientbiblioteket för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: pafarley
ms.openlocfilehash: 7de7a87c73c2dabddf2268a6a0bc2992d6017e4d
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853939"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>Snabbstart: Analysera en bild med Computer Vision SDK och C#

I den här snabbstarten ska du analysera både en lokal bild och en fjärrbild för att extrahera visuella funktioner med hjälp av Windows-klientbiblioteket för Visuellt innehåll.

## <a name="prerequisites"></a>Nödvändiga komponenter

* För att använda Visuellt innehåll behöver du en prenumerationsnyckel. Mer information finns i avsnittet [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Hämta prenumerationsnycklar).
* Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).
* NuGET-paketet för [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision)-klientbiblioteket. Du behöver inte ladda ned paketet. Installationsinstruktioner finns nedan.

## <a name="analyzeimageasync-method"></a>Metoden AnalyzeImageAsync

> [!TIP]
> Hämta den senaste koden som en Visual Studio-lösning från [GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

Metoderna `AnalyzeImageAsync` och `AnalyzeImageInStreamAsync` omsluter [API:et för bildanalys](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) för fjärrbilder respektive lokala bilder. Du kan använda dessa metoder för att extrahera visuella funktioner baserat på bildinnehållet och välja vilka funktioner som ska returneras, inklusive:

* En detaljerad lista över taggar relaterade till bildinnehållet.
* En beskrivning av bildinnehållet i en fullständig mening.
* Koordinater, kön och ålder för ansikten som finns i bilden.
* Bildtyp (ClipArt eller en linjeteckning).
* Den mest framträdande färgen, accentfärgen eller huruvida en bild är svartvit.
* Kategorin som definierats i den här [taxonomin](../Category-Taxonomy.md).
* Innehåller bilden innehåll som inte är lämpligt för barn?

För att köra exemplet följer du dessa steg:

1. Skapa en ny Visual C#-konsolapp i Visual Studio.
1. Installera NuGet-paketet för klientbiblioteket för Visuellt innehåll.
    1. Klicka på **Verktyg** på menyn, välj **NuGet Package Manager** (NuGet-pakethanteraren) och välj sedan **Manage NuGet Packages for Solution** (Hantera NuGet-paket för lösning).
    1. Klicka på fliken **Bläddra** och skriv ”Microsoft.Azure.CognitiveServices.Vision.ComputerVision” i rutan **Sök**.
    1. Välj **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** när det visas. Klicka på kryssrutan bredvid namnet på ditt projekt och sedan på **Installera**.
1. Ersätt `Program.cs` med följande kod.
1. Ersätt `<Subscription Key>` med en giltig prenumerationsnyckel.
1. Om det behövs ändrar du `computerVision.Endpoint` till den Azure-region som är associerad med dina prenumerationsnycklar.
1. Ersätt `<LocalImage>` med sökvägen och filnamnet för en lokal bild.
1. Du kan också ange `remoteImageUrl` till en annan bild om du vill.
1. Kör programmet.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace ImageAnalyze
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

        // Specify the features to return
        private static readonly List<VisualFeatureTypes> features =
            new List<VisualFeatureTypes>()
        {
            VisualFeatureTypes.Categories, VisualFeatureTypes.Description,
            VisualFeatureTypes.Faces, VisualFeatureTypes.ImageType,
            VisualFeatureTypes.Tags
        };

        static void Main(string[] args)
        {
            ComputerVisionClient computerVision = new ComputerVisionClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "westcentralus" with "westus".
            //
            // Free trial subscription keys are generated in the "westus"
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

            Console.WriteLine("Images being analyzed ...");
            var t1 = AnalyzeRemoteAsync(computerVision, remoteImageUrl);
            var t2 = AnalyzeLocalAsync(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press ENTER to exit");
            Console.ReadLine();
        }

        // Analyze a remote image
        private static async Task AnalyzeRemoteAsync(
            ComputerVisionClient computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            ImageAnalysis analysis =
                await computerVision.AnalyzeImageAsync(imageUrl, features);
            DisplayResults(analysis, imageUrl);
        }

        // Analyze a local image
        private static async Task AnalyzeLocalAsync(
            ComputerVisionClient computerVision, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            using (Stream imageStream = File.OpenRead(imagePath))
            {
                ImageAnalysis analysis = await computerVision.AnalyzeImageInStreamAsync(
                    imageStream, features);
                DisplayResults(analysis, imagePath);
            }
        }

        // Display the most relevant caption for the image
        private static void DisplayResults(ImageAnalysis analysis, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
        }
    }
}
```

## <a name="analyzeimageasync-response"></a>Svar från AnalyzeImageAsync

Om åtgärden lyckades visas den mest relevanta beskrivningen för varje bild.

Ett exempel på JSON-råutdata finns i [API Quickstarts: Analyze a local image with C#](../QuickStarts/CSharp-analyze.md#examine-the-response) (API-snabbstarter: Analysera en lokal bild med C#).

```
http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>Nästa steg

Utforska API:erna för visuellt innehåll som används för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text.

> [!div class="nextstepaction"]
> [Utforska API:er för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
