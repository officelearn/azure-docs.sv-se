---
title: LUIS-och QnAMaker-bot-integrering
titleSuffix: Azure Cognitive Services
description: Eftersom din QnA Maker kunskaps bas ökar, blir det svårt att underhålla den som en enda monolitisk uppsättning. Dela kunskaps basen i mindre logiska segment.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402707"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Använd en robot med QnA Maker-och LUIS för att distribuera kunskaps basen
Eftersom din QnA Maker kunskaps bas ökar, blir det svårt att underhålla den som en enda monolitisk uppsättning. Dela kunskaps basen i mindre logiska segment.

Även om det är enkelt att skapa flera kunskaps baser i QnA Maker behöver du en del logik för att dirigera den inkommande frågan till rätt kunskaps bas. Du kan göra detta med hjälp av LUIS.

I den här artikeln används bot Framework v3 SDK. Om du är intresse rad av robot Framework v4 SDK-versionen av den här informationen kan du läsa mer i [använda flera Luis-och QNA-modeller](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp).

## <a name="architecture"></a>Arkitektur

![Bild som visar arkitekturen för QnA Maker med Language Understanding](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Föregående bild visar att QnA Maker först hämtar avsikten till den inkommande frågan från en LUIS-modell. Sedan använder QnA Maker den avsikten för att dirigera frågan till rätt QnA Maker kunskaps bas.

## <a name="create-a-luis-app"></a>Skapa en LUIS-app

1. Logga in på [Luis](https://www.luis.ai/) -portalen.
1. [Skapa en app](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Lägg till en avsikt](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) för varje QNA Maker kunskaps bas. Exemplet yttranden bör motsvara frågor i QnA Maker kunskaps baser.
1. [Träna Luis-appen](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) och [publicera Luis-appen](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. I avsnittet **Hantera** noterar du ditt Luis app-ID, Luis-slutpunkt-nyckeln och det [anpassade domän namnet](../../cognitive-services-custom-subdomains.md). Du behöver dessa värden senare.

## <a name="create-qna-maker-knowledge-bases"></a>Skapa QnA Maker kunskaps baser

1. Logga in på [QNA Maker](https://qnamaker.ai).
1. [Skapa](https://www.qnamaker.ai/Create) en kunskaps bas för varje avsikt i Luis-appen.
1. Testa och publicera kunskaps baserna. När du publicerar var och en måste du anteckna ID, resurs namn (den anpassade under domänen före _. azurewebsites.net/qnamaker_) och nyckeln för att ta slut punkten. Du behöver dessa värden senare.

    I den här artikeln förutsätts att kunskaps baserna har skapats i samma Azure QnA Maker-prenumeration.

    ![Skärm bild av QnA Maker HTTP-begäran](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Robot för webbapp

1. [Skapa en "Basic"-robot med Azure bot service](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0), som automatiskt innehåller en Luis-app. Välj C#-programmeringsspråk.

1. När du har skapat roboten för webbapp går du till Azure Portal och väljer roboten för webbapp.
1. I navigeringen för Web App bot-tjänsten väljer du **program inställningar**. Rulla ned till avsnittet **program inställningar** i tillgängliga inställningar.
1. Ändra **LuisAppId** till värdet för Luis-appen som skapades i föregående avsnitt. Välj sedan **Spara**.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>Ändra koden i BasicLuisDialog.cs-filen
1. I avsnittet **robot Management** i navigeringen för Web App Bot i Azure Portal väljer du **build**.
2. Välj **Öppna online Code Editor**. En ny flik i webbläsaren öppnas med redigerings miljön online.
3. I avsnittet **wwwroot** väljer du **dialog** rutan katalog och öppnar sedan **BasicLuisDialog.cs**.
4. Lägg till beroenden överst i **BasicLuisDialog.cs** -filen:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Lägg till följande klasser för att deserialisera QnA Maker svaret:

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Lägg till följande klass för att göra en HTTP-begäran till tjänsten QnA Maker. Observera att värdet för **Authorization** -huvudet inkluderar ordet, `EndpointKey`, med ett blank steg efter ordet. JSON-resultatet avserialiseras till föregående klasser och det första svaret returneras.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Ändra `BasicLuisDialog` klassen. Varje LUIS-avsikt bör ha en metod som är dekorerad med **LuisIntent**. Parametern till dekorationen är det faktiska namnet på LUIS. Det metod namn som anges _ska_ vara namnet på den Luis avsikten för läsbarhet och hanterbarhet, men behöver inte vara samma vid design eller körning.

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";

        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>Bygg bot
1. I kod redigeraren högerklickar du på **build. cmd**och väljer **Kör från konsol**.

    ![Skärm bild av alternativet Kör från konsol i kod redigeraren](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Kodvyn ersätts med ett terminalfönster som visar förloppet och resultatet av bygget.

    ![Skärm bild av konsol versionen](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testa bot
I Azure Portal väljer du **test i Web Chat** för att testa roboten. Skriv meddelanden från olika avsikter för att få svar från motsvarande kunskaps bas.

![Skärm bild av Web Chat-test](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Integrera din kunskaps bas med en agent i virtuella energi agenter](integrate-with-power-virtual-assistant-fallback-topic.md)
