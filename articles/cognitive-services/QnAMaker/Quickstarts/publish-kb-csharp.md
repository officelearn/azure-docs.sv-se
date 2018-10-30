---
title: 'Snabbstart: Publicera kunskapsbas – REST, C# – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Den här REST-baserade snabbstarten vägleder dig genom publiceringen av din kunskapsbas som överför den senaste versionen av den testade kunskapsbasen till ett dedikerat Azure Search-index som representerar den publicerade kunskapsbasen. Den skapar även en slutpunkt som kan anropas i ditt program eller en chattrobot.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: ce027abb75423d0174a7175c3bbafe5c0fb3e157
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646270"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Snabbstart: Publicera en kunskapsbas i QnA Maker med C#

Den här snabbstarten går igenom hur du programmatiskt publicerar din kunskapsbas (KB). Publicering skickar den senaste versionen av kunskapsbasen till ett dedikerat Azure Search-index och skapar en slutpunkt som kan anropas i ditt program eller en chattrobot.

Den här snabbstarten anropar API:er för QnA Maker:
* [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) (Publicera) – detta API kräver inte någon information i brödtexten för begäran.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Senaste [**Visual Studio Community-versionen**](https://www.visualstudio.com/downloads/).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på instrumentpanelen. 
* ID för QnA Maker-kunskapsbas (KB) som finns i URL:en i kbid-frågesträngsparametern enligt nedan.

    ![QnA Maker-kunskapsbas-ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Om du inte har en kunskapsbas ännu kan du kan skapa en exempelkunskapsbas för den här snabbstarten: [Skapa en ny kunskapsbas](create-new-kb-csharp.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="create-knowledge-base-project"></a>Skapa kunskapsbasprojekt

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-required-dependencies"></a>Lägga till nödvändiga beroenden

[!INCLUDE [Add required dependencies to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-dependencies.md)] 

## <a name="add-required-constants"></a>Lägg till nödvändiga konstanter

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-constants.md)]  

## <a name="add-knowledge-base-id"></a>Lägga till kunskapsbas-ID

[!INCLUDE [Add knowledge base ID as constant](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-kb-id.md)] 

## <a name="add-supporting-functions-and-structures"></a>Lägga till funktionerna och strukturer

Lägg till följande kodblock i klassen Program:

```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="add-post-request-to-publish-kb"></a>Lägg till POST-begäran för att publicera kunskapsbas

Följande kod gör en HTTPS-begäran för API för QnA Maker för att publicera en kunskapsbas och tar emot svaret:

```csharp
async static void PublishKB()
{
    string responseText;

    var uri = host + service + method + kb;
    Console.WriteLine("Calling " + uri + ".");
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);

        // successful status doesn't return an JSON so create one
        if (response.IsSuccessStatusCode)
        {
            responseText = "{'result' : 'Success.'}";
        }
        else
        {
            responseText =  await response.Content.ReadAsStringAsync();
        }
    }
    Console.WriteLine(PrettyPrint(responseText));
    Console.WriteLine("Press any key to continue.");
}
```

API-anropet returnerar 204-status för en lyckad publicering utan något innehåll i svarets brödtext. Koden lägger till innehåll för 204-svar.

För andra svar returneras svaret oförändrat.
 
## <a name="add-the-publishkb-method-to-main"></a>Lägga till metoden PublishKB till Main

Ändra Main-metoden för att anropa metoden CreateKB:

```csharp
static void Main(string[] args)
{

    // Call the PublishKB() method to publish a knowledge base.
    PublishKB();

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Skapa och kör programmet. Det skickar automatiskt begäran till API för QnA Maker för att publicera kunskapsbasen, och sedan skrivs svaret ut till konsolfönstret.

När din kunskapsbas har publicerats kan köra frågor mot den från slutpunkten med ett klientprogram eller en chattrobot. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
