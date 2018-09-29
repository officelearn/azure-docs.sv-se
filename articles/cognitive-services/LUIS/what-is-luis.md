---
title: Vad är Language Understanding Intelligent Service (LUIS) – Azure Cognitive Services | Microsoft Docs
description: Language Understanding Intelligent Service (LUIS) är en molnbaserad API-tjänst som använder anpassad maskininlärningsinformation på en användares naturliga konversationsspråk för att förutsäga den övergripande betydelsen och hämta relevant, detaljerad information. Ett klientprogram för LUIS är alla konversationsanpassade program som kommunicerar med en användare på naturligt språk för att utföra en uppgift. Exempel på klientprogram är appar för sociala medier, chattrobotar och talaktiverade skrivbordsprogram.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: overview
ms.date: 08/15/2018
ms.author: diberry
ms.openlocfilehash: a8e9deb7c677d04634b223045adc2d31fa74ba6e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033047"
---
# <a name="what-is-language-understanding-luis"></a>Vad är Language Understanding Intelligent Service (LUIS)?

Language Understanding Intelligent Service (LUIS) är en molnbaserad API-tjänst som använder anpassad maskininlärningsinformation på en användares naturliga konversationsspråk för att förutsäga den övergripande betydelsen och hämta relevant, detaljerad information. 

Ett klientprogram för LUIS är alla konversationsanpassade program som kommunicerar med en användare på naturligt språk för att utföra en uppgift. Exempel på klientprogram är appar för sociala medier, chattrobotar och talaktiverade skrivbordsprogram.  

![Konceptbild av tre klientprogram som fungerar med Cognitive Services Language Understanding Intelligent Service (LUIS)](./media/luis-overview/luis-entry-point.png "Konceptbild av tre klientprogram som fungerar med Cognitive Services Language Understanding Intelligent Service (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Använda LUIS i en chattrobot

<a name="Accessing-LUIS"></a>

När LUIS-appen har skickar ett klientprogram yttranden (text) till LUIS slutpunkts-[API][endpoint-apis] för bearbetning av naturligt språk och får resultat som JSON-svar. Ett vanligt klientprogram för LUIS är en chattrobot.


![Konceptbilder av LUIS som fungerar med chattrobot för att förutsäga användartext med förståelse av naturligt språk (NLP)](./media/luis-overview/luis-overview-process-2.png "Konceptbilder av LUIS som fungerar med chattrobot för att förutsäga användartext med förståelse av naturligt språk (NLP")

|Steg|Åtgärd|
|:--|:--|
|1|Klientprogrammet skickar användarens _yttrande_ (text med användarens egna ord), "I want to call my HR rep." (”Jag vill ringa HR-personalen.”) till LUIS-slutpunkten som en HTTP-begäran.|
|2|LUIS tillämpar den inlärda modellen på texten med naturligt språk för att tillhandahålla intelligent förståelse om användardata. LUIS returnerar ett JSON-formaterat svar, med en främsta avsikt, "HRContact" (”HR-kontakt”). Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. Det kan även extrahera data som entiteten Kontakttyp.|
|3|Klientprogrammet använder JSON-svaret för att fatta beslut om hur användarens begäranden sak uppfyllas. Dessa beslut kan inkludera ett beslutsträd i Bot Framework-koden och anropar andra tjänster. |

LUIS-appen tillhandahåller information så att klientprogrammet kan fatta smarta beslut. LUIS har inte dessa alternativ. 

<!--

### Example of JSON endpoint response

The minimum JSON endpoint response contains the query utterance, and the top scoring intent. It can also extract data such as the following **Contact Type** entity. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```
-->

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Bearbetning av naturligt språk

En LUIS-app innehåller en domänspecifik modell för naturligt språk. Du kan starta LUIS-appen med en fördefinierad domänmodell, bygga din egen modell eller blanda delar av en fördefinierad modell med egen anpassad information.

* LUIS med**fördefinierad modell** har många fördefinierade domänmodeller som innehåller avsikter, yttranden och fördefinierade yttranden. Du kan använda de fördefinierade entiteterna utan att behöva använda den fördefinierade modellens avsikter och yttranden. [Fördefinierade domänmodeller](luis-how-to-use-prebuilt-domains.md) innehåller hela designen för dig och är ett bra sätt att börja använda LUIS snabbt.

* LUIS med **anpassade entiteter** ger dig flera sätt att identifiera dina egna avsikter och entiteter, till exempel maskininlärda entiteter, specifika eller exakta entiteter, och en kombination av maskininlärda och exakta.

## <a name="build-the-luis-model"></a>Skapa LUIS-modellen
Skapa modellerna med [redigerings](https://aka.ms/luis-authoring-apis)-API:er eller med LUIS-portalen.

LUIS-modellen börjar med kategorier av användaravsikter som kallas för **[avsikter](luis-concept-intent.md)**. Varje avsikt behöver exempel på **[yttranden](luis-concept-utterance.md)** från användaren. Varje yttrande kan ge olika data som måste extraheras med **[entiteter](luis-concept-entity-types.md)**. 

|Exempel på användaryttrande|Avsikt|Entiteter|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?" (”Boka en resa till Seattle?”)|BookFlight (Boka flyg)|Seattle|
|"When does your store __open__?" (”När öppnar butiken”)|StoreHoursAndLocation (Butikens öppettider och plats)|open (öppen)|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution" (”Boka ett möte kl. 13 med Bob”)|ScheduleMeeting (Boka möte)|1pm, Bob (kl. 13, Bob)|

<!--
## What is a natural language model?

A model begins with a list of general user intentions, called _intents_, such as "Book Flight" or "Contact Help Desk." You provide user's example text, called _example utterances_ for the intents. Then mark significant words or phrases in the utterance, called _entities_.


A model includes:

* **[intents](#intents)**: categories of user intentions (overall intended action or result)
* **[entities](#entities)**: specific types of data in utterances such as number, email, or name contained in text
* **[example utterances](#example-utterances)**: example text a user might enter in the client application

### Intents 

An [intent](luis-how-to-add-intents.md), short for _intention_, is a purpose or goal expressed in a user's utterance, such as booking a flight, paying a bill, or finding a news article. You create an intent for each action. A LUIS travel app may define an intent named "BookFlight." Each prediction query includes the top scored intent. 

The client application can use the top scoring intent to trigger an action. For example, when "BookFlight" intent is returned from LUIS, a client application could trigger an API call to an external service for booking a plane ticket.

### Entities

An [entity](luis-how-to-add-entities.md) represents detailed information found within the utterance that is relevant to the user's request. For example, in the utterance "Book a ticket to Paris",  a single ticket is requested, and "Paris" is a location. Two entities are found "a ticket" indicating a single ticket and "Paris" indicating the destination. 

After LUIS returns the entities found in the user’s utterance, the client application can use the list of entities as parameters to trigger an action. For example, booking a flight requires entities like the travel destination, date, and airline.
-->
<!--
### Example utterances

An example [utterance](luis-how-to-add-example-utterances.md) is text input from the user that the client application needs to understand. It may be a sentence, like "Book a ticket to Paris", or a fragment of a sentence, like "Booking" or "Paris flight." Utterances aren't always well-formed, and there can be many utterance variations for a particular intent. Add 10 to 20 example utterances to each intent and mark entities in every utterance.

|Example user utterance|Intent|Entities|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?"|BookFlight|Seattle|
|"When does your store __open__?"|StoreHoursAndLocation|open|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution"|ScheduleMeeting|1pm, Bob|
-->
## <a name="query-prediction-endpoint"></a>Slutpunkt för frågeförutsägelse

När modellen har skapats och publicerats på slutpunkten skickar klientprogrammet yttranden till den API:et för [slutpunkt](https://aka.ms/luis-endpoint-apis) för förutsägelse. API:et tillämpar modellen på texten som ska analyseras. API:et svarar med förutsägelseresultatet i JSON-format.  

Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. Det kan även extrahera data, som följande **Kontakttyp**-entitet. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>Förbättra modellförutsägelse

När en LUIS-modell har publicerats och får verkliga användaryttranden har LUIS flera metoder att förbättra förutsägelsernas precision: [aktiv inlärning](#active-learning) för slutpunktsyttranden, [fraslistor](#phrase-lists) för inkludering av domänord och [mönster](#patterns) för att minska antalet yttranden som krävs.
<!--
### Active learning

In the [active learning](luis-how-to-review-endoint-utt.md) process, LUIS allows you to adapt the LUIS app to real-world utterances by selecting utterances it received at the endpoint for your review. You can accept or correct the endpoint prediction, retrain, and republish. LUIS learns quickly with this iterative process, taking the minimum amount of your time and effort. 

### Phrase lists 

LUIS provides [phrases lists](luis-concept-feature.md) so you can indicate important words or phrases of the model. LUIS uses these lists to add additional significance to those words and phrases that would otherwise not be found in the model.

### Patterns 

Patterns allow you to simplify an intent's utterance collection into common [templates](luis-concept-patterns.md) of word choice and word order. This allows LUIS to learn quicker by needing fewer example utterances for the intents. Patterns are a hybrid system of regular expressions and machine-learned expressions. 
-->
<a name="using-luis"></a>
<!--
## Authoring and accessing models
Author LUIS from the [authoring](https://aka.ms/luis-authoring-apis) APIs or from the LUIS portal. Query the published prediction endpoint of the model from the [endpoint](https://aka.ms/luis-endpoint-apis) APIs.
-->

## <a name="integrating-with-luis"></a>Integrera med LUIS
LUIS kan, som REST-API, användas med alla produkter, tjänster eller ramverk som gör en HTTP-begäran. Följande lista innehåller de främsta Microsoft-produkterna och -tjänsterna som används med LUIS.

Microsoft-klientprogram för LUIS är:
* [Web app bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0) skapar snabbt en LUIS-aktiverad chattrobot för att prata med en användare via textinmatning. Använder [Bot Framework][bot-framework] version [3.x](https://github.com/Microsoft/BotBuilder) eller [4.x](https://github.com/Microsoft/botbuilder-dotnet) för en komplett robotupplevelse.
* [Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/) – är dig mer med den här [kursen om mixad verklighet](https://docs.microsoft.com/windows/mixed-reality/mr-azure-303) med LUIS. 

Microsoft-verktyg för att använda LUIS med en robot:
* Med [Dispatch](https://aka.ms/dispatch-tool) kan flera LUIS- och QnA Maker-appar användas via en överordnad app som använder en dispatcher-modell.
* Med [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) kan du skapa robotkonversationer snabbare med LUIS.
* [Project personality chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) för att hantera robotsmåprat.

Andra Cognitive Services-tjänster som används med LUIS:
* Med [QnA Maker][qnamaker] kan flera typer av text kombineras till en kunskapsbas med frågor och svar.
* [API för stavningskontroll i Bing](../bing-spell-check/proof-text.md) kontrollerar texten innan förutsägelse. 
* [Speech Service](../Speech-Service/overview.md) konverterar begäranden med talat språk till text. 



## <a name="next-steps"></a>Nästa steg

Skapa en ny LUIS-app med en [fördefinierad](luis-get-started-create-app.md) eller [anpassad](luis-quickstart-intents-only.md) domän. [Fråga slutpunkten för förutsägelse](luis-get-started-cs-get-intent.md) för en offentlig IoT-app.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/