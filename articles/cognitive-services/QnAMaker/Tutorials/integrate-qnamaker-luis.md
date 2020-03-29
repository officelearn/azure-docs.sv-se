---
title: LUIS och QnAMaker - Bot Integration
titleSuffix: Azure Cognitive Services
description: När din QnA Maker kunskapsbas växer sig stor, blir det svårt att behålla den som en enda monolitisk uppsättning och det finns ett behov av att dela upp kunskapsbasen i mindre logiska bitar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e1ea234bde96ce84259841bbc592bf6373bc639
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71802796"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Använd bot med QnA Maker och LUIS för att distribuera din kunskapsbas
När din QnA Maker kunskapsbas växer sig stor, blir det svårt att behålla den som en enda monolitisk uppsättning och det finns ett behov av att dela upp kunskapsbasen i mindre logiska bitar.

Även om det är enkelt att skapa flera kunskapsbaser i QnA Maker, behöver du lite logik för att dirigera den inkommande frågan till lämplig kunskapsbas. Du kan göra detta genom att använda LUIS.

I den här artikeln används Bot Framework v3 SDK. Se denna [Bot Framework artikel](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp), om du är intresserad av Bot Framework v4 SDK version av denna information.

## <a name="architecture"></a>Arkitektur

![QnA Maker med arkitektur för språk understanding](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

I ovanstående scenario får QnA Maker först avsikten med den inkommande frågan från en LUIS-modell och använder den sedan för att dirigera den till rätt QnA Maker-kunskapsbas.

## <a name="create-a-luis-app"></a>Skapa en LUIS-app

1. Logga in [LUIS](https://www.luis.ai/) på LUIS-portalen.
1. [Skapa en app](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Lägg till en avsikt](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) för varje QnA Maker-kunskapsbas. Exempelyttrandena bör motsvara frågor i QnA Maker-kunskapsbaserna.
1. [Träna LUIS-appen](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) och [publicera LUIS-appen](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) din LUIS-app.
1. I avsnittet **Hantera** antecknar du luis-app-ID: n, LUIS-slutpunktsnyckeln och [det anpassade domännamnet](../../cognitive-services-custom-subdomains.md). Du behöver dessa värden senare. 

## <a name="create-qna-maker-knowledge-bases"></a>Skapa QnA Maker kunskapsbaser

1. Logga in på [QnA Maker](https://qnamaker.ai).
1. [Skapa](https://www.qnamaker.ai/Create) kunskapsbaser för varje avsikt i LUIS-appen.
1. Testa och publicera kunskapsbaserna. När du publicerar varje KB noterar du KB-ID:t, resursnamn (anpassad underdomän före _.azurewebsites.net/qnamaker_) och nyckeln för auktoriseringsslutpunkt. Du behöver dessa värden senare. 

    Den här artikeln förutsätter att KBs alla skapas i samma Azure QnA Maker-prenumeration.

    ![HTTP-begäran för QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Webbapp Bot

1. [Skapa en "Grundläggande" Web App bot](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) som automatiskt innehåller en LUIS-app. Välj Programmeringsspråk för C#.

1. När webbapproboten har skapats väljer du webbapproboten i Azure-portalen.
1. Välj **Programinställningar** i navigeringen i webbapprotjänsten och bläddra sedan ned till **avsnittet Programinställningar** med tillgängliga inställningar.
1. Ändra **LuisAppId** till värdet för LUIS-appen som skapats i föregående avsnitt och välj sedan **Spara**.


## <a name="change-code-in-basicluisdialogcs"></a>Ändra kod i BasicLuisDialog.cs
1. Välj **Skapa**i avsnittet **Bothantering** i webbappronvigeringen i Azure-portalen .
2. Välj **Öppna kodredigeraren online**. En ny webbläsarflik öppnas med onlineredigeringsmiljön. 
3. I avsnittet **WWWROOT** väljer du katalogen **Dialogrutor** och öppnar sedan **BasicLuisDialog.cs**.
4. Lägg till beroenden överst i **BasicLuisDialog.cs-filen:**

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

5. Lägg till nedanstående klasser för att deserialisera QnA Maker-svaret:

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


6. Lägg till följande klass för att göra en HTTP-begäran till QnA Maker-tjänsten. Observera att värdet **för auktoriseringshuvudet** innehåller ordet, `EndpointKey` med ett blanksteg som följer ordet. JSON-resultatet deserialiseras till föregående klasser och det första svaret returneras.

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


7. Ändra klassen BasicLuisDialog. Varje LUIS avsikt bör ha en metod dekorerad med **LuisIntent**. Parametern till dekorationen är det faktiska LUIS-avsiktsnamnet. Metodnamnet som är inrett _ska_ vara LUIS-avsiktsnamnet för läsbarhet och underhåll, men behöver inte vara detsamma vid design eller körning.  

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


## <a name="build-the-bot"></a>Bygg boten
1. Högerklicka på `build.cmd` kodredigeraren och välj **Kör från konsol**.

    ![köra från konsolen](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Kodvyn ersätts med ett terminalfönster som visar förloppet och resultaten av bygget.

    ![konsol bygga](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testa bot
I Azure-portalen väljer du **Testa i webbchatt** för att testa roboten. Skriv meddelanden från olika avsikter för att få svar från motsvarande kunskapsbas.

![webbchatt test](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en affärskontinuitetsplan för QnA Maker](../How-To/business-continuity-plan.md)
