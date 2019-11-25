---
title: Vad är Language Understanding Intelligent Service (LUIS)?
titleSuffix: Azure Cognitive Services
description: Language Understanding Intelligent Service (LUIS) är en molnbaserad API-tjänst som använder anpassad maskininlärningsinformation på en användares naturliga konversationsspråk för att förutsäga den övergripande betydelsen och hämta relevant, detaljerad information.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456563"
---
# <a name="what-is-language-understanding-luis"></a>Vad är Language Understanding Intelligent Service (LUIS)?

Language Understanding (LUIS) is a cloud-based API service that applies custom machine-learning intelligence to natural language text to predict overall meaning, and pull out relevant, detailed information. 

For example, when a client application sends the text, `find me a wireless keyboard for $30`, LUIS responds with the following JSON object. 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
In the example above, the _**intent**_ , or overall meaning of the phrase is that the user is trying to find an item. The detailed pieces of information that LUIS extracts are called _**entities**_ . In this case, the entities are the name of the item the user is looking for and the amount of money they want to spend.

Client applications use LUIS's returned JSON, the _intent_ (category), and _entities_ (extracted detailed information), to drive actions in the client application. A client application for LUIS is often a conversational application that communicates with a user in natural language to complete a task. Exempel på klientprogram är appar för sociala medier, chattrobotar och talaktiverade skrivbordsprogram. 

![Conceptual image of 3 client applications working with Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Conceptual image of 3 client applications working with Cognitive Services Language Understanding (LUIS)")

## <a name="example-use-luis-in-a-chat-bot"></a>Example use LUIS in a chat bot

<a name="Accessing-LUIS"></a>

A client application sends utterances (text) to the published LUIS natural language processing endpoint [API][endpoint-apis] and receives the results as JSON responses. Ett vanligt klientprogram för LUIS är en chattrobot.


![Conceptual imagery of LUIS working with Chat bot to predict user text with natural language understanding (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Conceptual imagery of LUIS working with Chat bot to predict user text with natural language understanding (NLP")

|Steg|Åtgärd|
|:--|:--|
|1|Klientprogrammet skickar användarens _yttrande_ (text med användarens egna ord), "I want to call my HR rep." (”Jag vill ringa HR-personalen.”) till LUIS-slutpunkten som en HTTP-begäran.|
|2|LUIS applies machine learned language models to the user's unstructured input text and returns a JSON-formatted response, with a top intent, `HRContact`. Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. It can also extract data such as the _Contact Type_ entity.|
|3|Klientprogrammet använder JSON-svaret för att fatta beslut om hur användarens begäranden sak uppfyllas. These decisions can include a decision tree in the bot and calls to other services. |

LUIS-appen tillhandahåller information så att klientprogrammet kan fatta smarta beslut. LUIS har inte dessa alternativ. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Bearbetning av naturligt språk

Your LUIS app contains domain-specific natural language models, which work together. You can start the LUIS app with one or more prebuilt models, build your own model, or blend prebuilt models with your own custom information.

* **Prebuilt model** LUIS has many prebuilt domains that include intent and entity models that work together to complete common usage scenarios. These domains include labeled utterances that can be inspected and edited, allowing you to customize them. [Fördefinierade domänmodeller](luis-how-to-use-prebuilt-domains.md) innehåller hela designen för dig och är ett bra sätt att börja använda LUIS snabbt. In addition, there are prebuilt entities such as currency and number that you can use independently from the prebuilt domains.

* **Custom model** LUIS gives you several ways to build your own custom models including intents, and entities. Entities include machine-learned entities, pattern matching entities, and a combination of machine-learned and pattern matching.

## <a name="build-the-luis-app"></a>Build the LUIS app
Build the app with the [authoring](https://go.microsoft.com/fwlink/?linkid=2092087) APIs or with the [LUIS portal](https://www.luis.ai).

The LUIS app begins with categories of input text called **[intents](luis-concept-intent.md)** . Varje avsikt behöver exempel på **[yttranden](luis-concept-utterance.md)** från användaren. Each utterance can provide data that needs to be extracted. 

|Exempel på användaryttrande|Avsikt|Extracted data|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight (Boka flyg)|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation (Butikens öppettider och plats)|open (öppen)|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting (Boka möte)|1pm, Bob (kl. 13, Bob)|

## <a name="query-prediction-endpoint"></a>Slutpunkt för frågeförutsägelse

After your app is trained and published to the endpoint, the client application sends utterances to the prediction [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) API. The API applies the app to the utterance for analysis and responds with the prediction results in a JSON format.  

Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. It can also extract data such as the following **Contact Type** entity and overall sentiment. 

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>Förbättra modellförutsägelse

After your LUIS app is published and receives real user utterances, LUIS provides [active learning](luis-concept-review-endpoint-utterances.md) of endpoint utterances to improve prediction accuracy. 

<a name="using-luis"></a>

## <a name="iterative-development-lifecycle"></a>Iterative development lifecycle
LUIS provides tools, versioning, and collaboration with other LUIS authors to integrate into the full iterative [development life cycle](luis-concept-app-iteration.md). 

## <a name="implementing-luis"></a>Implementera LUIS
Language Understanding (LUIS), as a REST API, can be used with any product, service, or framework with an HTTP request. Följande lista innehåller de främsta Microsoft-produkterna och -tjänsterna som används med LUIS.

Det vanligaste klientprogram för LUIS är:
* [Web app bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) skapar snabbt en LUIS-aktiverad chattrobot för att prata med en användare via textinmatning. Uses [Bot Framework][bot-framework] version [4.x](https://github.com/Microsoft/botbuilder-dotnet) for a complete bot experience.

Verktyg för att snabbt och enkelt använda LUIS med en robot:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) The NPM package provides authoring and prediction with as either a stand-alone command line tool or as import. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen är ett verktyg för att generera starkt typbestämd C#- och TypeScript-källkod från en exporterad LUIS-modell.
* Med [Dispatch](https://aka.ms/dispatch-tool) kan flera LUIS- och QnA Maker-appar användas via en överordnad app som använder en dispatcher-modell.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown är ett kommandoradsverktyg som hjälper dig att hantera språkmodeller för din robot.
* [Bot framework - Composer](https://github.com/microsoft/BotFramework-Composer) - an integrated development tool for developers and multi-disciplinary teams to build bots and conversational experiences with the Microsoft Bot Framework

Andra Cognitive Services-tjänster som används med LUIS:
* [QnA Maker][qnamaker] allows several types of text to combine into a question and answer knowledge base.
* [Speech Service](../Speech-Service/overview.md) konverterar begäranden med talat språk till text. 
* Med [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) kan du skapa robotkonversationer snabbare med LUIS.

Exempel som använder LUIS:
* GitHub-lagringsplatsen [Conversational AI](https://github.com/Microsoft/AI).
* [Bot framework - Bot samples](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Nästa steg

* [Nyheter](whats-new.md)
* Author a new LUIS app with a [prebuilt](luis-get-started-create-app.md) or [custom](luis-quickstart-intents-only.md) domain
* [Fråga slutpunkten för förutsägelse](luis-get-started-get-intent-from-browser.md) för en offentlig IoT-app. 
* [Developer resources](developer-reference-resource.md) for LUIS. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
