---
title: 'Skaffa modell med REST-samtal i C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 96129b9141b4759fd61b539fa08354f02af3af7b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80151290"
---
## <a name="prerequisites"></a>Krav

* Azure Language Understanding - Författare resurs 32 teckennyckel och redigering slutpunkt URL. Skapa med [Azure-portalen](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) eller [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importera [TravelAgent-appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) från GitHub-databasen för kognitiva tjänster-språkförståelse.
* App-id:t i LUIS för den importerade TravelAgent-appen. Program-ID visas på programmets instrumentpanel.
* Den versions-ID i programmet som tar emot yttrandena. Standard-id:t är ”0.1”.
* [.NET-kärna 3.1](https://dotnet.microsoft.com/download)
* [Visual Studio-kod](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Ändra modell programmässigt

1. Skapa ett nytt konsolprogram som är inriktat på `model-with-rest`C#-språket med ett projekt- och mappnamn på .

    ```console
    dotnet new console -lang C# -n model-with-rest
    ```

1. Installera obligatoriska beroenden med följande dotnet CLI-kommandon.

    ```console
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```
1. Skriv över Program.cs med följande kod:

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;

    // 3rd party NuGet packages
    using JsonFormatterPlus;

    namespace AddUtterances
    {
        class Program
        {
            // NOTE: use your LUIS authoring key - 32 character value
            static string authoringKey = "YOUR-KEY";

            // NOTE: Replace this endpoint with your authoring key endpoint
            // for example, your-resource-name.api.cognitive.microsoft.com
            static string endpoint = "YOUR-ENDPOINT";

            // NOTE: Replace this with the ID of your LUIS application
            static string appID = "YOUR-APP-ID";

            // NOTE: Replace this your version number
            static string appVersion = "0.1";

            static string host = String.Format("https://{0}/luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);

            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);

                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }

                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";

                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";

                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
                [
                    {
                    'text': 'go to Seattle today',
                    'intentName': 'BookFlight',
                    'entityLabels': [
                        {
                        'entityName': 'Location::LocationTo',
                        'startCharIndex': 6,
                        'endCharIndex': 12
                        }
                    ]
                    },
                    {
                        'text': 'a barking dog is annoying',
                        'intentName': 'None',
                        'entityLabels': []
                    }
                ]
                ";
                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
        }
    }
    ```

1. Ersätt de värden `YOUR-` som börjar med dina egna värden.

    |Information|Syfte|
    |--|--|
    |`YOUR-KEY`|Din 32 tecken författande nyckel.|
    |`YOUR-ENDPOINT`| Slutpunkten för redigerings-URL. Till exempel `replace-with-your-resource-name.api.cognitive.microsoft.com`. Du anger resursnamnet när du skapade resursen.|
    |`YOUR-APP-ID`| Ditt LUIS-app-ID. |

    Tilldelade nycklar och resurser visas i LUIS-portalen i avsnittet Hantera på sidan **Azure-resurser.** App-ID:et är tillgängligt i samma avsnittet Hantera på sidan **Programinställningar.**

1. Skapa konsolprogrammet.

    ```console
    dotnet build
    ```

1. Kör konsolprogrammet. Konsolutgången visar samma JSON som du såg tidigare i webbläsarfönstret.

    ```console
    dotnet run
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabbstarten tar du bort filen från filsystemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för en app](../luis-concept-best-practices.md)
