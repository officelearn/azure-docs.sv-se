---
title: 'Snabbstart: Skapa kunskapsbas – REST, C# – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Den här snabbstarten går igenom skapandet av ett exempel på QnA Maker-kunskapsbas, programmässigt, som visas i Azure-instrumentpanelen för ditt Cognitive Services-API-konto.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: diberry
ms.openlocfilehash: e6b8c769082b688b07bac78bca5e2dca59a2d9c2
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389419"
---
# <a name="quickstart-create-a-qna-maker-knowledge-base-in-c"></a>Snabbstart: Skapa en QnA Maker-kunskapsbas i C#

Den här snabbstarten går igenom hur du programmatiskt skapar ett exempel på QnA Maker-kunskapsbas. QnA Maker extraherar automatiskt frågor och svar för delvis strukturerat innehåll, som vanliga frågor från [datakällor](../Concepts/data-sources-supported.md). Modellen för kunskapsbasen har definierats i JSON som skickas i brödtexten i API-begäran. 

Den här snabbstarten anropar API:er för QnA Maker:
* [Skapa kunskapsbas](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Hämta åtgärdsinformation](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Nödvändiga komponenter

* Senaste [**Visual Studio Community-versionen**](https://www.visualstudio.com/downloads/).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på instrumentpanelen. 

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="create-a-knowledge-base-project"></a>Skapa ett kunskapsbasprojekt

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-dependencies.md)]  

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-constants.md)] 

## <a name="add-the-kb-definition"></a>Lägga till KB-definition

Efter konstanterna lägger du till följande KB-definition:

```csharp
static string kb = @"
{
  'name': 'QnA Maker FAQ from quickstart',
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your knowledge base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my knowledge base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ],
  'urls': [
    'https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs',
    'https://docs.microsoft.com/bot-framework/resources-bot-framework-faq'
  ],
  'files': []
}
";
```

## <a name="add-supporting-functions-and-structures"></a>Lägga till funktionerna och strukturer

[!INCLUDE [Add supporting functions and structures](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-support-functions.md)] 

## <a name="add-a-post-request-to-create-kb"></a>Lägga till en POST-begäran för att skapa KB

Följande kod gör en HTTPS-begäran för API för QnA Maker för att skapa en kunskapsbas och tar emot svaret:

```csharp
async static Task<Response> PostCreateKB(string kb)
{
    // Builds the HTTP request URI.
    string uri = host + service + method;

    // Writes the HTTP request URI to the console, for display purposes.
    Console.WriteLine("Calling " + uri + ".");

    // Asynchronously invokes the Post(string, string) method, using the
    // HTTP request URI and the specified data source.
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(kb, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

Detta API-anrop anropar ett JSON-svar som innehåller åtgärds-ID. Använd åtgärds-ID:t för att fastställa om KB har skapats. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Lägga till GET-begäran för att fastställa skapandestatus

Kontrollera status för åtgärden.

```csharp
async static Task<Response> GetStatus(string operationID)
{
    // Builds the HTTP request URI.
    string uri = host + service + operationID;

    // Writes the HTTP request URI to the console, for display purposes.
    Console.WriteLine("Calling " + uri + ".");

    // Asynchronously invokes the Get(string) method, using the
    // HTTP request URI.
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Get;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

Det här API-anropet returnerar ett JSON-svar som innehåller åtgärdsstatus: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Upprepa anropet tills det lyckas eller misslyckas: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Lägga till metoden CreateKB

Följande metod skapar KB och upprepar kontroller av statusen. Eftersom det kan ta lite tid att skapa KB måste du upprepa anrop för att kontrollera status tills statusen antingen lyckas eller misslyckas.

```csharp
async static void CreateKB()
{
    try
    {
        // Starts the QnA Maker operation to create the knowledge base.
        var response = await PostCreateKB(kb);

        // Retrieves the operation ID, so the operation's status can be
        // checked periodically.
        var operation = response.headers.GetValues("Location").First();

        // Displays the JSON in the HTTP response returned by the 
        // PostCreateKB(string) method.
        Console.WriteLine(PrettyPrint(response.response));

        // Iteratively gets the state of the operation creating the
        // knowledge base. Once the operation state is set to something other
        // than "Running" or "NotStarted", the loop ends.
        var done = false;
        while (true != done)
        {
            // Gets the status of the operation.
            response = await GetStatus(operation);

            // Displays the JSON in the HTTP response returned by the
            // GetStatus(string) method.
            Console.WriteLine(PrettyPrint(response.response));

            // Deserialize the JSON into key-value pairs, to retrieve the
            // state of the operation.
            var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

            // Gets and checks the state of the operation.
            String state = fields["operationState"];
            if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
            {
                // QnA Maker is still creating the knowledge base. The thread is 
                // paused for a number of seconds equal to the Retry-After header value,
                // and then the loop continues.
                var wait = response.headers.GetValues("Retry-After").First();
                Console.WriteLine("Waiting " + wait + " seconds...");
                Thread.Sleep(Int32.Parse(wait) * 1000);
            }
            else
            {
                // QnA Maker has completed creating the knowledge base. 
                done = true;
            }
        }
    }
    catch
    {
        // An error occurred while creating the knowledge base. Ensure that
        // you included your QnA Maker subscription key where directed in the sample.
        Console.WriteLine("An error occurred while creating the knowledge base.");
    }
    finally
    {
        Console.WriteLine("Press any key to continue.");
    }

}
``` 

## <a name="add-the-createkb-method-to-main"></a>Lägga till metoden CreateKB till Main

Ändra Main-metoden för att anropa metoden CreateKB:

```csharp
static void Main(string[] args)
{
    // Call the CreateKB() method to create a knowledge base, periodically 
    // checking the status of the QnA Maker operation until the 
    // knowledge base is created.
    CreateKB();

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Skapa och kör programmet. Begäran skickas automatiskt till API:et för QnA Maker för att skapa KB, och sedan söker den efter resultaten med 30 sekunders mellanrum. Varje svar skrivs ut i konsolfönstret.

När kunskapsbasen har skapats kan du visa den i QnA Maker-portalen, på sidan [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mina kunskapsbaser). 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
