---
title: Använd Custom Vision Service förutsägelse slutpunkt - Azure Cognitive Services | Microsoft Docs
description: 'Lär dig hur du använder API: T programmatiskt bilder med din klassificerare för Custom Vision Service.'
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: d7f9b90db06811e16cd0cd6ad2b32a27912cfee5
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341801"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Använd förutsägelse-slutpunkten för att testa avbildningar via programmering med en klassificerare för Custom Vision Service

När du träna din modell testa du avbildningar via programmering genom att skicka dem till förutsägelse-API. 

> [!NOTE]
> Det här dokumentet visar hur du använder C# för att skicka en avbildning förutsägelse-API: et. Mer information och exempel på användning av API: et finns i den [förutsägelse-API-referens](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Hämta nyckel för URL- och förutsägelsetransaktioner

Från den [Custom Vision-webbsida](https://customvision.ai), markera projektet och välj sedan den __prestanda__ fliken. Att visa information om hur du använder förutsägelse-API, inklusive den __förutsägelse-key__väljer __förutsägelse URL__. För projekt som är kopplade till en Azure-resurs din __förutsägelse-key__ finns också i den [Azure-portalen](https://portal.azure.com) för associerade Azure-resurs under __nycklar__. Kopiera följande information för användning i programmet:

* __URL: en__ för att använda en __bildfil__.
* __Förutsägelse-key__ värde.

> [!TIP]
> Om du har flera iterationer kan styra du vilken som används genom att ange den som standard. Välj iteration från den __iterationer__ avsnittet och välj sedan __ange som standard__ överst på sidan.

![Fliken prestanda visas med en röd rektangel omger URL: en förutsägelse.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Skapa programmet

1. Skapa ett nytt C#-konsolprogram i Visual Studio.

2. Använd följande kod som huvudstycket i den __Program.cs__ fil.

    > [!IMPORTANT]
    > Ändra följande information:
    >
    > * Ange den __namnområde__ till namnet på ditt projekt.
    > * Ange den __förutsägelse-Key__ värde som du fick tidigare på raden som börjar med `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Ange den __URL__ värde som du fick tidigare på raden som börjar med `string url =`.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "http://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

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
        }
    }
    ```

## <a name="use-the-application"></a>Använda programmet

När du kör programmet, ange sökvägen till en bildfil. Avbildningen skickas till API: et och resultaten returneras som JSON-dokument. Följande JSON är ett exempel på svaret

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>Nästa steg

[Exportera modellen för mobil användning](export-your-model.md)
