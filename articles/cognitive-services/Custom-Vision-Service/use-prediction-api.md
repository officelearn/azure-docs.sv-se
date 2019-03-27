---
title: 'Exempel: Använd förutsägelseslutpunkt för att programmatiskt testa bilder med klassificerare – Custom Vision'
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder API:et för att programmatiskt testa bilder med din klassificerare för Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472735"
---
#  <a name="use-your-model-with-the-prediction-api"></a>Använd din modell med förutsägande API

När du tränar din modell kan du testa bilder programmatiskt genom att skicka dem till förutsägelse-API:et.

> [!NOTE]
> Det här dokumentet visar hur du använder C# för att skicka en bild till förutsägelse-API:et. Mer information och exempel på användning av API:et finns i [Referens för förutsägelse-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publicera din tränade iteration

Från [Custom Vision-webbsidan](https://customvision.ai), markera projektet och välj sedan fliken __prestanda__.

För att skicka avbildningar förutsägelse-API: et, du måste först publicera din upprepningen för förutsägelse som kan göras genom att välja __publicera__ och ange ett namn för den publicerade upprepningen. Detta aktiverar din modell ska vara tillgänglig för förutsägelse-API: et för din anpassade Vision Azure-resurs. 

![Fliken prestanda visas med en röd rektangel omger knappen Publicera.](./media/use-prediction-api/unpublished-iteration.png)

När modellen har publicerats, visas en ”publicerad” etikett som visas bredvid din iteration i vänster sidofält samt namnet på den publicerade iterationen i beskrivningen i iteration.

![Fliken prestanda visas med en röd rektangel omger den publicerade etiketten och namnet på den publicerade iterationen.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Hämta URL och förutsägelsenyckel

När din modell har publicerats kan du hämta information om hur du använder förutsägelse-API genom att välja __förutsägelse URL__. Då öppnas en dialogruta som liknar den nedan med information för att använda förutsägelse-API, inklusive den __förutsägelse URL__ och __förutsägelse-nyckeln__.

![Fliken prestanda visas med en röd rektangel omger förutsägelse URL-knappen.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Fliken prestanda visas med en röd rektangel omger förutsägelse URL-värdet för att använda en bildfil och förutsägelse-nyckel-värde.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Din __förutsägelse-Key__ finns också i den [Azure-portalen](https://portal.azure.com) sidan för Custom Vision Azure-resurs som är kopplad till ditt projekt, under __nycklar__. 

Kopiera följande information för användning i programmet från dialogrutan:

* __URL: en förutsägelse__ för att använda en __bildfil__.
* __Förutsägelse-Key__ värde.

## <a name="create-the-application"></a>Skapa programmet

1. Skapa ett nytt C#-konsolprogram från Visual Studio.

1. Använd följande kod som brödtext i filen __Program.cs__.

    > [!IMPORTANT]
    > Ändra följande information:
    >
    > * Ange __namnområdet__ till namnet på ditt projekt.
    > * Ange den __förutsägelse-Key__ värde som du hämtade tidigare i den rad som börjar med `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Ange den __förutsägelse URL__ värde som du hämtade tidigare i den rad som börjar med `string url =`.

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
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

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

## <a name="use-the-application"></a>Använd programmet

När du kör programmet, anger du sökvägen till en bildfil i konsolen. Avbildningen skickas förutsägelse-API: et och resultatet returneras som JSON-dokument. Följande JSON är ett exempel på svaret.

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

[Exportera modellen för mobilanvändning](export-your-model.md)

[Kom igång med .NET SDK: er](csharp-tutorial.md)

[Kom igång med Python SDK: er](python-tutorial.md)

[Kom igång med Java SDK: er](java-tutorial.md)

[Kom igång med Node SDK: er](node-tutorial.md)

[Kom igång med Go-SDK: er](go-tutorial.md)
