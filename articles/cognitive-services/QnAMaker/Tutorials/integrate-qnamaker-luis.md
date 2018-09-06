---
title: Integrera QnA Maker och LUIS - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: en stegvis självstudiekurs om integrering av QnA Maker och LUIS
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 18eae69867dc9774f63b11c762b22df4595bdce6
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781755"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Integrera QnA Maker och LUIS för att distribuera din kunskapsbas
När kunskapsbasen QnA Maker växer stora, blir det svårt att underhålla den som en enda monolitisk ange och det finns en behöver delas upp i knowledge base i mindre logiska segment.

Det är enkelt att skapa flera kunskapsbaser i QnA Maker, behöver du vissa logik för att dirigera inkommande frågan till rätt kunskapsbas. Du kan göra detta med hjälp av LUIS.

## <a name="architecture"></a>Arkitektur

![QnA Maker luis-arkitektur](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

I scenariot ovan QnA Maker först hämtar syftet med den inkommande frågan från en LUIS-modell och sedan använda den för att dirigera till rätt kunskapsbas för QnA Maker.

## <a name="prerequisites"></a>Förutsättningar
- Logga in på den [LUIS](https://www.luis.ai/) portal och [skapa en app](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app).
- [Lägg till avsikter](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents) enligt ditt scenario.
- [Träna](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train) och [publicera](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp) LUIS-appen.
- Logga in på [QnA Maker](https://qnamaker.ai) och [skapa](https://www.qnamaker.ai/Create) kunskapsbaser enligt ditt scenario.
- Testa och publicera kunskapsbaser.

## <a name="qna-maker--luis-bot"></a>QnA Maker + LUIS-Bot
1. Först skapa en Web App-robot med LUIS-mall, koppla den till LUIS-app som du skapade ovan och ändra avsikter. Se detaljerade steg [här](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Lägga till beroenden överst i filen med de andra beroendena:

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Lägg till den nedan klass för att anropa QnA Maker-tjänsten:

    ```
        /// <summary>
        /// QnAMakerService is a wrapper over the QnA Maker REST endpoint
        /// </summary>
        [Serializable]
        public class QnAMakerService
        {
            private string qnaServiceHostName;
            private string knowledgeBaseId;
            private string endpointKey;
    
            /// <summary>
            /// Initialize a particular endpoint with it's details
            /// </summary>
            /// <param name="hostName">Hostname of the endpoint</param>
            /// <param name="kbId">Knowledge base ID</param>
            /// <param name="ek">Endpoint Key</param>
            public QnAMakerService(string hostName, string kbId, string ek)
            {
                qnaServiceHostName = hostName;
                knowledgeBaseId = kbId;
                endpointKey = ek;
            }
    
            /// <summary>
            /// Call the QnA Maker endpoint and get a response
            /// </summary>
            /// <param name="query">User question</param>
            /// <returns></returns>
            public string GetAnswer(string query)
            {
                var client = new RestClient( qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer");
                var request = new RestRequest(Method.POST);
                request.AddHeader("authorization", "EndpointKey " + endpointKey);
                request.AddHeader("content-type", "application/json");
                request.AddParameter("application/json", "{\"question\": \"" + query + "\"}", ParameterType.RequestBody);
                IRestResponse response = client.Execute(request);
    
                // Deserialize the response JSON
                QnAAnswer answer = JsonConvert.DeserializeObject<QnAAnswer>(response.Content);
    
                // Return the answer if present
                if (answer.answers.Count > 0)
                    return answer.answers[0].answer;
                else
                    return "No good match found.";
            }
        }
    
        /* START - QnA Maker Response Class */
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
        /* END - QnA Maker Response Class */
    ```

3. Gå till https://qnamaker.ai -> min kunskapsbaser -> Visa koden för motsvarande kunskapsbasen. Hämta följande information:

    ![QnA Maker HTTP-begäran](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. Skapa en instans av klassen QnAMakerService för var och en av dina kunskapsbaser:
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. Anropa motsvarande kunskapsbasen för avsikten.
    ```
            // HR Intent
            [LuisIntent("HR")]
            public async Task CancelIntent(IDialogContext context, LuisResult result)
            {
                // Ask the HR knowledge base
                await context.PostAsync(hrQnAService.GetAnswer(result.Query));
            }
    
            // Payroll intent
            [LuisIntent("Payroll")]
            public async Task GreetingIntent(IDialogContext context, LuisResult result)
            {
                // Ask the payroll knowledge base
                await context.PostAsync(payrollQnAService.GetAnswer(result.Query));
            }
    
            // Finance intent
            [LuisIntent("Finance")]
            public async Task HelpIntent(IDialogContext context, LuisResult result)
            {
                // Ask the finance knowledge base
                await context.PostAsync(financeQnAService.GetAnswer(result.Query));
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
