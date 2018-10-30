---
title: 'Snabbstart: Identifiera ansikten i en bild med hjälp av .NET SDK med C#'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten identifierar du ansikten med Windows ansiktsigenkänning med C#-klientbibliotek i Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: pafarley
ms.openlocfilehash: a4b0b8b277ed6bc6e2bc3c7549d1e67d5f18c615
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954971"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-net-sdk-with-c"></a>Snabbstart: Identifiera ansikten i en bild med hjälp av .NET SDK med C#

I den här snabbstarten identifierar du människoansikten i en bild med klientbiblioteket för Windows-ansiktsigenkänning.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Du behöver en prenumerationsnyckel för att köra exemplet. Du kan hämta nycklar för kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
* Valfri version av [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* NuGet-paketet för klientbiblioteket [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Du behöver inte ladda ned paketet. Installationsinstruktioner finns nedan.

## <a name="detectwithurlasync-method"></a>Metoden DetectWithUrlAsync

> [!TIP]
> Hämta den senaste koden som en Visual Studio-lösning från [GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face).

Metoderna `DetectWithUrlAsync` och `DetectWithStreamAsync` omsluter [API:et för ansiktsigenkänning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) för fjärrbilder respektive lokala bilder. Använd dessa metoder för att identifiera ansikten i en bild och returnera ansiktsattribut som:

* Ansikts-ID: unikt ID som används i flera Ansikts-API-scenarier.
* Ansiktsrektangel: vänster, överkant, bredd och höjd som anger ansiktets placering i bilden.
* Landmärke: en matris med ansiktslandmärken med 27 punkter som pekar på viktiga positioner för ansiktsdelar.
* Ansiktsattribut som ålder, kön, leendeintensitet, huvudställning och ansiktsbehåring.

För att köra exemplet följer du dessa steg:

1. Skapa en ny Visual C#-konsolapp i Visual Studio.
1. Installera NuGet-paketet för klientbiblioteket för ansiktsigenkänning.
    1. Klicka på **Verktyg** på toppmenyn, välj **NuGet Package Manager** (NuGet-pakethanteraren) och välj sedan **Manage NuGet Packages for Solution** (Hantera NuGet-paket för lösning).
    1. Klicka på fliken **Bläddra** och välj sedan **Inkludera förhandsversion**.
    1. I **sökrutan** skriver du ”Microsoft.Azure.CognitiveServices.Vision.Face”.
    1. Välj **Microsoft.Azure.CognitiveServices.Vision.Face** när det visas. Klicka på kryssrutan bredvid namnet på ditt projekt och sedan på **Installera**.
1. Ersätt *Program.cs* med följande kod.
1. Ersätt `<Subscription Key>` med en giltig prenumerationsnyckel.
1. Om det behövs ändrar du `faceEndpoint` till den Azure-region som är associerad med dina prenumerationsnycklar.
1. Om du vill kan du ersätta <`LocalImage>` med sökvägen och filnamnet för en lokal bild (ignoreras om det inte anges).
1. Du kan också ange `remoteImageUrl` till en annan bild om du vill.
1. Kör programmet.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // You must use the same region as you used to get your subscription
        // keys. For example, if you got your subscription keys from westus,
        // replace "westcentralus" with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus
        // region. If you use a free trial subscription key, you shouldn't
        // need to change the region.
        // Specify the Azure region
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

        static void Main(string[] args)
        {
            FaceClient faceClient = new FaceClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });
            faceClient.Endpoint = faceEndpoint;

            Console.WriteLine("Faces being detected ...");
            var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
            var t2 = DetectLocalAsync(faceClient, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Detect faces in a remote image
        private static async Task DetectRemoteAsync(
            FaceClient faceClient, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            try
            {
                IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithUrlAsync(
                        imageUrl, true, false, faceAttributes);

                DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imageUrl + ": " + e.Message);
            }
        }

        // Detect faces in a local image
        private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            try
            {
                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    IList<DetectedFace> faceList =
                            await faceClient.Face.DetectWithStreamAsync(
                                imageStream, true, false, faceAttributes);
                    DisplayAttributes(
                        GetFaceAttributes(faceList, imagePath), imagePath);
                }
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imagePath + ": " + e.Message);
            }
        }

        private static string GetFaceAttributes(
            IList<DetectedFace> faceList, string imagePath)
        {
            string attributes = string.Empty;

            foreach (DetectedFace face in faceList)
            {
                double? age = face.FaceAttributes.Age;
                string gender = face.FaceAttributes.Gender.ToString();
                attributes += gender + " " + age + "   ";
            }

            return attributes;
        }

        // Display the face attributes
        private static void DisplayAttributes(string attributes, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(attributes + "\n");
        }
    }
}
```

### <a name="detectwithurlasync-response"></a>Svar på DetectWithUrlAsync

Ett lyckat svar visar kön och ålder för varje ansikte i bilden.

Ett exempel på JSON-råutdata finns i [API-snabbstarter: Analysera en lokal bild med C#](CSharp.md).

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar ett WPF-Windows-program som använder tjänsten Ansiktsigenkänning för att identifiera ansikten i en bild. Appen ritar en ram runt varje ansikte och visar en beskrivning av ansiktet i statusfältet.

> [!div class="nextstepaction"]
> [Självstudie: Skapa en WPF-app för att upptäcka och rama in ansikten i en bild](../Tutorials/FaceAPIinCSharpTutorial.md)
