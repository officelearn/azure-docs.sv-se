---
title: LUIS och QnAMaker - Bot-integrering
titleSuffix: Azure Cognitive Services
description: När kunskapsbasen QnA Maker växer stora, blir det svårt att underhålla den som en enda monolitisk ange och det finns en behöver delas upp i knowledge base i mindre logiska segment.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1d7130e841ce48ead4fca1d76bf5e3e051263efd
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216803"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Använd bot med QnA Maker och LUIS för att distribuera din kunskapsbas
När kunskapsbasen QnA Maker växer stora, blir det svårt att underhålla den som en enda monolitisk ange och det finns en behöver delas upp i knowledge base i mindre logiska segment.

Det är enkelt att skapa flera kunskapsbaser i QnA Maker, behöver du vissa logik för att dirigera inkommande frågan till rätt kunskapsbas. Du kan göra detta med hjälp av LUIS.

Den här artikeln använder Bot Framework v3-SDK. Finns i denna [Bot Framework artikeln](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp), om du är intresserad av i Bot Framework v4 SDK-version av den här informationen.

## <a name="architecture"></a>Arkitektur

![QnA Maker luis-arkitektur](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

I scenariot ovan QnA Maker först hämtar syftet med den inkommande frågan från en LUIS-modell och sedan använda den för att dirigera till rätt kunskapsbas för QnA Maker.

## <a name="create-a-luis-app"></a>Skapa en LUIS-app

1. Logga in på den [LUIS](https://www.luis.ai/) portal.
1. [Skapa en app](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Lägg till ett intent](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) för varje QnA Maker knowledge base. Exempel yttranden bör motsvara frågor i kunskapsbaser QnA Maker.
1. [Träna LUIS-app](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) och [publicera LUIS-app](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) LUIS-appen.
1. I den **hantera** avsnittet, anteckna din LUIS app-ID, LUIS slutpunktsnyckeln, och vara värd för regionen. Du behöver dessa värden senare. 

## <a name="create-qna-maker-knowledge-bases"></a>Skapa QnA Maker kunskapsbaser

1. Logga in på [QnA Maker](https://qnamaker.ai).
1. [Skapa](https://www.qnamaker.ai/Create) en kunskapsbaser för varje avsikt i LUIS-app.
1. Testa och publicera kunskapsbaser. När du publicerar varje KB anteckna KB-ID, värd (underdomän innan _.azurewebsites.net/qnamaker_), och auktoriseringsnyckeln för slutpunkten. Du behöver dessa värden senare. 

    Den här artikeln förutsätter att de KB-artiklar skapas i samma Azure QnA Maker-prenumeration.

    ![QnA Maker HTTP-begäran](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web app-robot

1. [Skapa en Web App-robot](https://docs.microsoft.com/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample) med LUIS-mall. Välj 3.x SDK och programmeringsspråket C#.

1. När web app-robot skapas i Azure-portalen väljer du web app-robot.
1. Välj **programinställningar** i Web app bot service navigering, rulla ned till **programinställningar** under tillgängliga inställningar.
1. Ändra den **LuisAppId** till värdet för LUIS-app som skapats i föregående avsnitt som sedan är väljer **spara**.


## <a name="change-code-in-basicluisdialogcs"></a>Ändra koden i BasicLuisDialog.cs
1. Från den **Bot Management** delen av web app bot navigering i Azure portal, Välj **skapa**.
2. Välj **öppna online Kodredigerare**. En ny webbläsarflik öppnas med online Kodredigering. 
3. I den **WWWROOT** väljer den **-dialogrutor** katalogen och sedan öppna **BasicLuisDialog.cs**.
4. Lägga till beroenden överst i **BasicLuisDialog.cs** fil:

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

5. Lägg till den nedan klasser att deserialisera QnA Maker-svaret:

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


6. Lägg till följande klass för att göra en HTTP-begäran för QnA Maker-tjänsten. Observera att den **auktorisering** sidhuvudets värde innehåller word, `EndpointKey` med ett blanksteg efter ordet. JSON-resultat avserialiseras föregående klasserna och det första svaret returneras.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceHostName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string hostName, string kbId, string endpointkey)
        {
            qnaServiceHostName = hostName;
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
            string uri = qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = @"{'question': '" + question + "'}";

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


7. Ändra klassen BasicLuisDialog. Varje LUIS avsikt ska ha en metod som dekorerad med **LuisIntent**. Parametern för är dekorativa är det faktiska avsikt LUIS-namnet. Metodnamnet som är dekorerad _bör_ vara LUIS avsikt namnet för Läs- och underhåll men måste inte vara samma på design eller körtid.  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_endpointDomain = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

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


## <a name="build-the-bot"></a>Skapa roboten
1. I kodredigeraren, högerklickar du på `build.cmd` och välj **kör från konsolen**.

    ![Kör från konsolen](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Kodvyn ersätts med ett terminalfönster som visar förlopp och resultat för versionen.

    ![konsolen build](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testa roboten
I Azure-portalen väljer du **testa i Web Chat** att testa roboten. Skriv meddelanden från olika avsikter att få svaret från motsvarande kunskapsbasen.

![chatt webbtest](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kontinuitetsplan för QnA Maker](../How-To/business-continuity-plan.md)
