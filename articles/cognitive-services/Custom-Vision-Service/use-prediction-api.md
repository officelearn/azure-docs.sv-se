---
title: Använd förutsägelseslutpunkt för att programmatiskt testa bilder med klassificerare – Custom Vision
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder API:et för att programmatiskt testa bilder med din klassificerare för Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: dcb12da680d70e1f0ce4cd763bee340bb3416c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169953"
---
# <a name="use-your-model-with-the-prediction-api"></a>Använda din modell med förutsägelse-API:et

När du har tränat din modell kan du testa bilder programmässigt genom att skicka dem till slutpunkten för förutsägelse-API.

> [!NOTE]
> Det här dokumentet visar hur du använder C# för att skicka en bild till förutsägelse-API:et. Mer information och exempel finns i [referensen för förutsägelse-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publicera din tränade iteration

Från [Custom Vision-webbsidan](https://customvision.ai), markera projektet och välj sedan fliken __prestanda__.

Om du vill skicka bilder till förutsägelse-API:et måste du först publicera iterationen för förutsägelse, vilket kan göras genom att välja __Publicera__ och ange ett namn för den publicerade iterationen. Detta gör din modell tillgänglig för förutsägelse-API:et för din Custom Vision Azure-resurs.

![Fliken Prestanda visas med en röd rektangel som omger knappen Publicera.](./media/use-prediction-api/unpublished-iteration.png)

När din modell har publicerats visas etiketten "Publicerad" bredvid din iteration i det vänstra sidofältet och dess namn visas i beskrivningen av iterationen.

![Fliken Prestanda visas med en röd rektangel som omger etiketten Publicerad och namnet på den publicerade iterationen.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Hämta URL och förutsägelsenyckel

När din modell har publicerats kan du hämta den information som krävs genom att välja __Url för förutsägelse__. Detta öppnar en dialogruta med information för att använda api:et för förutsägelse, inklusive __url:en förutsägelse__ och __förutsägelsenyckeln__.

![Fliken Prestanda visas med en röd rektangel som omger knappen Förutsägelse-URL.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Fliken Prestanda visas med en röd rektangel som omger url-värdet Förutsägelse för att använda en bildfil och värdet Förutsägelse-Nyckel.](./media/use-prediction-api/prediction-api-info.png)


I den här guiden ska du använda en lokal bild, så kopiera webbadressen under **Om du har en bildfil** till en tillfällig plats. Kopiera även motsvarande __förutsägelsenyckelvärde.__

## <a name="create-the-application"></a>Skapa programmet

1. Skapa ett nytt C#-konsolprogram i Visual Studio.

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
   * Ange `namespace` fältet till namnet på projektet.
   * Ersätt platshållaren `<Your prediction key>` med det nyckelvärde som du hämtade tidigare.
   * Ersätt platshållaren `<Your prediction URL>` med webbadressen som du hämtade tidigare.

## <a name="run-the-application"></a>Köra appen

När du kör programmet uppmanas du att ange en sökväg till en bildfil i konsolen. Avbildningen skickas sedan till förutsägelse-API:et och förutsägelseresultaten returneras som en JSON-formaterad sträng. Följande är ett exempel på svar.

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

I den här guiden har du lärt dig hur du skickar in bilder till din anpassade bildklassificerare/detektor och får ett svar programmässigt med C# SDK. Lär dig sedan hur du slutför heltäckande scenarier med C#, eller kommer igång med ett annat språk SDK.

* [Snabbstart: .NET SDK](csharp-tutorial.md)
* [Snabbstart: Python SDK](python-tutorial.md)
* [Snabbstart: Java SDK](java-tutorial.md)
* [Snabbstart: Nod SDK](node-tutorial.md)
* [Snabbstart: Gå SDK](go-tutorial.md)
