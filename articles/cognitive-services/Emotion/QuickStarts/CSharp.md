---
title: Känslo -API C# Snabbstart | Microsoft Docs
description: Visa information och ett kodexempel som hjälper dig att snabbt komma igång med Känslo-API med C# i kognitiva Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 11/02/2017
ms.author: anroth
ms.openlocfilehash: acf30e7aded92c6d07331089fabd02836a8f3e9a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352422"
---
# <a name="emotion-api-c-quick-start"></a>Känslo -API C# Snabbstart

> [!IMPORTANT]
> Förhandsversion av Video-API avslutades 30 oktober 2017. Om du vill extrahera enkelt insikter från videor, prova den nya [Video indexeraren API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/). Du kan också använda den för att förbättra innehållsidentifiering upplevelser, till exempel sökresultat med hjälp av tal, ytor, tecken och känslor. Mer information finns i [Video indexeraren Preview](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) Översikt.

Den här artikeln innehåller information och en kodexempel för att hjälpa dig att snabbt komma igång med hjälp av den [Känslo-API känner igen metoden](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) med C#. Du kan använda den för att identifiera emotikoner uttryckt genom en eller flera personer i en bild. 

## <a name="prerequisites"></a>Förutsättningar
* Hämta kognitiva tjänster [Känslo-API: et Windows SDK](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/).
* Hämta din kostnadsfria [prenumeration nyckeln](https://azure.microsoft.com/try/cognitive-services/).

## <a name="emotion-recognition-c-example-request"></a>Känslo recognition C# exempelbegäran

Skapa en ny konsol lösning i Visual Studio och Ersätt Program.cs med följande kod. Ändra den `string uri` att använda den region där du har köpt din prenumeration nycklar. Ersätt den **Ocp-Apim-prenumeration-nyckeln** värde med en giltig prenumeration nyckel. Gå till Azure-portalen för att hitta nyckeln för prenumerationen. I navigeringsfönstret till vänster, under den **nycklar** bläddrar du till Känslo-API-resurs. På liknande sätt kan du rätt ansluta URI i den **översikt** panelen för din resurs som visas under **Endpoint**.

![Dina API-nycklar för resurs](../../media/emotion-api/keys.png)

Om du vill bearbeta svaret för din begäran, använder du ett bibliotek som `Newtonsoft.Json`. Det här sättet kan du hantera en JSON-sträng som en serie hanterbara objekt som kallas token. Lägg till det här biblioteket i paketet, högerklicka på projektet i Solution Explorer och markera **hantera Nuget-paket**. Sök sedan efter **Newtonsoft**. Det första resultatet bör vara **Newtonsoft.Json**. Välj **Installera**. Du kan nu refererar till det här biblioteket i ditt program.

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

## <a name="recognize-emotions-sample-response"></a>Identifiera emotikoner exempel svar
Lyckade anrop returnerar en matris av framsidan poster och deras associerade känslo resultat. De rangordnas efter ansikte rektangel storlek i fallande ordning. Ett tomt svar anger att ingen ytor upptäcktes. Känslo-posten innehåller följande fält:

* faceRectangle: placering av ansikte i avbildningen
* resultat: Känslo poäng för varje yta i avbildningen 

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
