---
title: Använd förutsägelseslutpunkt för att programmatiskt testa bilder med klassificerare – Custom Vision
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder API:et för att programmatiskt testa bilder med din klassificerare för Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 78ca1d7ceb9086e0d589f904b24b967d36b079a0
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895621"
---
# <a name="use-your-model-with-the-prediction-api"></a>Använd din modell med förutsägande API

När du har träna din modell, kan du testa avbildningar via programmering genom att skicka dem till förutsägelse-API-slutpunkt.

> [!NOTE]
> Det här dokumentet visar hur du använder C# för att skicka en bild till förutsägelse-API:et. Mer information och exempel finns i den [förutsägelse-API-referens](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publicera din tränade iteration

Från [Custom Vision-webbsidan](https://customvision.ai), markera projektet och välj sedan fliken __prestanda__.

För att skicka avbildningar förutsägelse-API: et, du måste först publicera din upprepningen för förutsägelse som kan göras genom att välja __publicera__ och ange ett namn för den publicerade upprepningen. Det gör din modell är tillgängliga för förutsägelse-API: et för din anpassade Vision Azure-resurs.

![Fliken prestanda visas med en röd rektangel omger knappen Publicera.](./media/use-prediction-api/unpublished-iteration.png)

När din modell har publicerats, ser du en etikett för ”publicerad” visas bredvid din iteration i vänster sidofält och dess namn visas i beskrivningen av iterationen.

![Fliken prestanda visas med en röd rektangel omger den publicerade etiketten och namnet på den publicerade iterationen.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Hämta URL och förutsägelsenyckel

När din modell har publicerats kan du hämta informationen som krävs genom att välja __förutsägelse URL__. Då öppnas en dialogruta med information för att använda förutsägelse-API, inklusive den __förutsägelse URL__ och __förutsägelse-nyckeln__.

![Fliken prestanda visas med en röd rektangel omger förutsägelse URL-knappen.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Fliken prestanda visas med en röd rektangel omger förutsägelse URL-värdet för att använda en bildfil och förutsägelse-nyckel-värde.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Din __förutsägelse-Key__ finns också i den [Azure-portalen](https://portal.azure.com) sidan för Custom Vision Azure-resurs som associeras med ditt projekt, under den __nycklar__ bladet.

I den här guiden ska du använda en lokal avbildning, så Kopiera URL som under **om du har en bildfil** till en tillfällig plats. Kopiera motsvarande __förutsägelse-Key__ även värdet.

## <a name="create-the-application"></a>Skapa programmet

1. I Visual Studio skapar du en ny C# -konsolapp.

1. Använd följande kod som brödtext i filen __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Ändra följande information:
   * Ange den `namespace` fältet med namnet på ditt projekt.
   * Ersätt platshållaren `<Your prediction key>` för nyckelvärdet som du hämtade tidigare.
   * Ersätt platshållaren `<Your prediction URL>` med URL-Adressen som du hämtade tidigare.

## <a name="run-the-application"></a>Köra programmet

När du kör programmet, uppmanas du att ange en sökväg till en bildfil i konsolen. Avbildningen skickas sedan förutsägelse-API: et och resultatet returneras som en JSON-formaterad sträng. Följande är ett exempel på ett svar.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Nästa steg

I den här handboken beskrivs hur du skickar bilder till din egen bild klassificerare/detektor och få ett svar via programmering med den C# SDK. Därefter lär dig hur du färdigställa scenarion för slutpunkt till slutpunkt med C#, eller komma igång med ett annat språk SDK.

* [Quickstart: .NET SDK](csharp-tutorial.md)
* [Snabbstart: Python SDK](python-tutorial.md)
* [Snabbstart: Java SDK](java-tutorial.md)
* [Snabbstart: SDK för Node](node-tutorial.md)
* [Snabbstart: Go SDK](go-tutorial.md)
