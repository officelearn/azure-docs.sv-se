---
title: Använda anpassade Vision förutsägelse tjänstslutpunkten - kognitiva Azure-tjänster | Microsoft Docs
description: 'Lär dig hur du använder API: T programmatiskt bilder med anpassad Vision Service-klassificerare.'
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 54f9d9fec1f40c167341dec6a8699b6a558419da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353304"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Använda förutsägelse slutpunkten för att testa bilder programmässigt med en anpassad Vision Service-klassificerare

När du träna din modell testa du avbildningar via programmering genom att skicka dem till API: T för förutsägelse. 

> [!NOTE]
> Det här dokumentet visar använda C# för att skicka en bild till API: T för förutsägelse. Mer information och exempel på användning av API: et finns i [förutsägelse API-referens för](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Hämta URL och förutsägelse nyckel

Från den [anpassad Vision webbsida](https://customvision.ai), Välj ditt projekt och välj sedan den __prestanda__ fliken. Om du vill visa information om hur du använder API: et för förutsägelse Välj __förutsägelse URL__. Kopiera följande information för användning i programmet:

* __URL: en__ för att använda en __avbildningsfil__.
* __Förutsägelse nyckeln__ värde.

> [!TIP]
> Du kan styra vilken som används genom att ange den som standard om du har flera iterationer. Välj iteration från den __iterationer__ avsnittet och väljer sedan __göra standard__ överst på sidan.

![Fliken prestanda visas med en röd rektangel runt URL: en förutsägelse.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Skapa programmet

1. Skapa ett nytt C#-konsolprogram från Visual Studio.

2. Använd följande kod som en del av den __Program.cs__ fil.

    > [!IMPORTANT]
    > Ändra följande information:
    >
    > * Ange den __namnområde__ till namnet på ditt projekt.
    > * Ange den __förutsägelse nyckeln__ värdet som du fått tidigare i den rad som börjar med `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Ange den __URL__ värdet som du fått tidigare i den rad som börjar med `string url =`.

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

När du kör programmet, ange sökvägen till en bildfil. Avbildningen skickas till API: et och resultatet returneras som ett JSON-dokument. Följande JSON är ett exempel på svaret

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

[Exportera modellen för mobila](export-your-model.md)