---
title: 'Snabbstart: Känsloigenkänning i ansikten i en bild – Känslo-API, C#'
titlesuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med hjälp av Känslo-API med C#.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 0874fc7b0c78f93df806b5bf782477efab0c0207
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234166"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Snabbstart: Skapa en app för att känna igen känslor i ansikten i en bild.

> [!IMPORTANT]
> Känslo-API:et blir inaktuellt den 15 februari 2019. Funktionen för känsloigenkänning är nu allmänt tillgänglig som en del av [ansikts-API:et](https://docs.microsoft.com/azure/cognitive-services/face/).

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med hjälp av [Känsloigenkänningsmetoden i Känslo-API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) med C#. Du kan använda den för att identifiera känslor som uttrycks av en eller flera personer på en bild.

## <a name="prerequisites"></a>Nödvändiga komponenter
* Hämta Cognitive Services [Känslo-API Windows SDK](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/).
* Hämta din kostnadsfria [prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/).

## <a name="emotion-recognition-c-example-request"></a>C#-exempelbegäran för känsloigenkänning

Skapa en ny konsollösning i Visual Studio och ersätt sedan Program.cs med följande kod. Ändra `string uri` till att använda den region där du hämtade dina prenumerationsnycklar. Ersätt värdet **Ocp-Apim-Subscription-Key** med en giltig prenumerationsnyckel. Hitta prenumerationsnyckeln genom att gå till Azure-portalen. I navigeringsfönstret till vänster går du till avsnittet **Nycklar** och bläddrar till Känslo-API-resursen. På ett liknande sätt kan du hämta rätt anslutnings-URI i panelen **Översikt** för din resurs som visas under **Slutpunkt**.

![Dina API-resursnycklar](../../media/emotion-api/keys.png)

För att bearbeta svaret på din begäran använder du ett bibliotek såsom `Newtonsoft.Json`. På så sätt kan du hantera en JSON-sträng som en serie hanterbara objekt som kallas token. Om du vill lägga till det här biblioteket i paketet högerklickar du på projektet i Solution Explorer och väljer **Hantera NuGet-paket**. Sök sedan efter **Newtonsoft**. Det första resultatet bör vara **Newtonsoft.Json**. Välj **Installera**. Du kan nu referera till det här biblioteket i ditt program.

![Installera Newtonsoft.Json](../../media/emotion-api/newtonsoft-nuget.png)

```csharp
using System;
using System.IO;
using System.Net.Http.Headers;
using System.Net.Http;
using Newtonsoft.Json.Linq;

namespace CSHttpClientSample
{
    static class Program
    {
        static void Main()
        {
            Console.Write("Enter the path to a JPEG image file:");
            string imageFilePath = Console.ReadLine();

            MakeRequest(imageFilePath);

            Console.WriteLine("\n\n\nWait for the result below, then hit ENTER to exit...\n\n\n");
            Console.ReadLine(); // wait for ENTER to exit program
        }

        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        static async void MakeRequest(string imageFilePath)
        {
            var client = new HttpClient();

            // Request headers - replace this example key with your valid key.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<your-subscription-key>"); //

            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
            //   URI below with "westcentralus".
            string uri = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?";
            HttpResponseMessage response;
            string responseContent;

            // Request body. Try this sample with a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (var content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                response = await client.PostAsync(uri, content);
                responseContent = response.Content.ReadAsStringAsync().Result;
            }

            // A peek at the raw JSON response.
            Console.WriteLine(responseContent);

            // Processing the JSON into manageable objects.
            JToken rootToken = JArray.Parse(responseContent).First;

            // First token is always the faceRectangle identified by the API.
            JToken faceRectangleToken = rootToken.First;

            // Second token is all emotion scores.
            JToken scoresToken = rootToken.Last;

            // Show all face rectangle dimensions
            JEnumerable<JToken> faceRectangleSizeList = faceRectangleToken.First.Children();
            foreach (var size in faceRectangleSizeList) {
                Console.WriteLine(size);
            }

            // Show all scores
            JEnumerable<JToken> scoreList = scoresToken.First.Children();
            foreach (var score in scoreList) {
                Console.WriteLine(score);
            }
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>Exempelsvar för känsloigenkänning
Ett genomfört anrop returnerar en matris med ansiktsposter och deras associerade känsloresultat. De är rankade efter ansiktsrektangelns storlek i fallande ordning. Ett tomt svar indikerar att inga ansikten kändes igen. En känslopost innehåller följande fält:

* faceRectangle: Ansiktsrektangelns placering i bilden
* scores: känsloresultat för varje ansikte i bilden

```json
application/json
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
