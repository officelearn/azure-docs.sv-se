---
title: Integrera frågor och svar om Maker och THOMAS - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: en stegvis självstudiekurs om att integrera frågor och svar om Maker och THOMAS
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 0a0eeb3815b793ed81f60b2b239bc459e5574788
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354111"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Integrera frågor och svar om Maker och THOMAS om du vill distribuera kunskapsbasen
Som frågor och svar om Maker kunskapsbasen blir stor, är det svårt att underhålla den eftersom en enda monolitisk och måste du dela kunskapsbasen i kortare logiska segment.

Det är enkelt att skapa flera knowledge baser i frågor och svar om Maker, behöver du vissa logik för att dirigera inkommande frågan till lämplig knowledge base. Du kan göra detta med hjälp av THOMAS.

## <a name="architecture"></a>Arkitektur

![Frågor och svar om Maker Thomas arkitektur](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

I scenariot ovan frågor och svar om Maker först hämtar syftet med den inkommande frågan från en THOMAS modell och sedan använder du den och vidarebefordra den till rätt frågor och svar om Maker knowledge base.

## <a name="prerequisites"></a>Förutsättningar
- Logga in på den [THOMAS](https://www.luis.ai/) portal och [skapa en app](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app).
- [Lägg till avsikter](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents) enligt ditt scenario.
- [Train](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train) och [publicera](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp) LUIS appen.
- Logga in på [frågor och svar om Maker](https://qnamaker.ai) och [skapa knowledge]() baserar enligt ditt scenario.
- [Testa]() och [publicera]() knowledge baser.

## <a name="qna-maker--luis-bot"></a>Frågor och svar om Maker + THOMAS Bot
1. Först skapar en Webbapp bot med mallen THOMAS länka med THOMAS appen som du skapade ovan och ändra innehållet. Detaljerade anvisningar finns [här](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Lägga till beroenden överst i filen med de andra beroenden:

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Lägg till den nedan klassen för att anropa tjänsten frågor och svar om Maker:

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

3. Gå till https://qnamaker.ai -> min knowledge baser -> Visa kod för motsvarande kunskapsbasen. Hämta följande information:

    ![Frågor och svar om Maker HTTP-begäran](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. Skapa en instans av klassen QnAMakerService för varje knowledge-databaser:
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

## <a name="build-the-bot"></a>Skapa bot
1. Högerklicka på i kodredigeraren `build.cmd` och välj **köra från konsolen**.

    ![Kör från konsolen](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Kodvyn ersätts med ett terminalfönster visar förlopp och resultat för versionen.

    ![konsolen build](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testa bot
Välj i Azure-portalen **testa i Web chatta** att testa bot. Skriv meddelanden från olika avsikter hämta svaret från motsvarande knowledge base.

![chatt webbtestet](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kontinuitetsplan för frågor och svar om Maker](../How-To/business-continuity-plan.md)
