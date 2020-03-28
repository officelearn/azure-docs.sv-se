---
title: Vad är Language Understanding Intelligent Service (LUIS)?
description: Language Understanding Intelligent Service (LUIS) är en molnbaserad API-tjänst som använder anpassad maskininlärningsinformation på en användares naturliga konversationsspråk för att förutsäga den övergripande betydelsen och hämta relevant, detaljerad information.
ms.topic: overview
ms.date: 02/23/2020
ms.openlocfilehash: 98fb936422f8e23e728efea19fa2cd75d90fac57
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053357"
---
# <a name="what-is-language-understanding-luis"></a>Vad är Language Understanding Intelligent Service (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding Intelligent Service (LUIS) är en molnbaserad API-tjänst som använder anpassad maskininlärningsinformation på en användares naturliga konversationsspråk för att förutsäga den övergripande betydelsen och hämta relevant, detaljerad information.

Ett klientprogram för LUIS är alla konversationsanpassade program som kommunicerar med en användare på naturligt språk för att utföra en uppgift. Exempel på klientprogram är appar för sociala medier, chattrobotar och talaktiverade skrivbordsprogram.

![Konceptuell bild av 3 klientprogram som arbetar med Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Konceptuell bild av 3 klientprogram som arbetar med Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Använda LUIS i en chattrobot

<a name="Accessing-LUIS"></a>

När LUIS-appen har skickar ett klientprogram yttranden (text) till LUIS slutpunkts-[API][endpoint-apis] för bearbetning av naturligt språk och får resultat som JSON-svar. Ett vanligt klientprogram för LUIS är en chattrobot.


![Konceptuella bilder av LUIS som arbetar med chattrobot för att förutsäga användartext med förståelse för naturligt språk (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Konceptuella bilder av LUIS som arbetar med chattrobot för att förutsäga användartext med förståelse för naturligt språk (NLP")

|Steg|Åtgärd|
|:--|:--|
|1|Klientprogrammet skickar användarens _yttrande_ (text med användarens egna ord), "I want to call my HR rep." (”Jag vill ringa HR-personalen.”) till LUIS-slutpunkten som en HTTP-begäran.|
|2|LUIS kan du skapa dina egna språkmodeller för att lägga till intelligens i ditt program. Maskinlärda språkmodeller tar användarens ostrukturerade indatatext och returnerar ett JSON-formaterat `HRContact`svar med högsta avsikt. Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. Den kan också extrahera data som _entiteten kontakttyp._|
|3|Klientprogrammet använder JSON-svaret för att fatta beslut om hur användarens begäranden sak uppfyllas. Dessa beslut kan innehålla beslutsträd i bot-ramkoden och samtal till andra tjänster. |

LUIS-appen tillhandahåller information så att klientprogrammet kan fatta smarta beslut. LUIS har inte dessa alternativ.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Bearbetning av naturligt språk

Luis-appen innehåller en domänspecifik modell för naturligt språk. Du kan starta LUIS-appen med en fördefinierad domänmodell, bygga din egen modell eller blanda delar av en fördefinierad modell med egen anpassad information.

* LUIS med**fördefinierad modell** har många fördefinierade domänmodeller som innehåller avsikter, yttranden och fördefinierade yttranden. Du kan använda de fördefinierade entiteterna utan att behöva använda den fördefinierade modellens avsikter och yttranden. [Fördefinierade domänmodeller](luis-how-to-use-prebuilt-domains.md) innehåller hela designen för dig och är ett bra sätt att börja använda LUIS snabbt.

* **Anpassad modell** LUIS ger dig flera sätt att identifiera dina egna anpassade modeller, inklusive avsikter och entiteter. Entiteter inkluderar maskininlärda entiteter, specifika eller bokstavliga entiteter och en kombination av maskininlärd och bokstavlig.

## <a name="build-the-luis-model"></a>Skapa LUIS-modellen
Skapa modellen med [authoring](https://go.microsoft.com/fwlink/?linkid=2092087) redigerings-API:erna eller med [LUIS-portalen](https://www.luis.ai).

LUIS-modellen börjar med kategorier av användaravsikter som kallas för **[avsikter](luis-concept-intent.md)**. Varje avsikt behöver exempel på **[yttranden](luis-concept-utterance.md)** från användaren. Varje uttryck kan tillhandahålla data som måste extraheras.

|Exempel på användaryttrande|Avsikt|Extraherade data|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight (Boka flyg)|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation (Butikens öppettider och plats)|open (öppen)|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting (Boka möte)|1pm, Bob (kl. 13, Bob)|

## <a name="query-prediction-endpoint"></a>Slutpunkt för frågeförutsägelse

När appen har tränats och publicerats till slutpunkten skickar klientprogrammet yttranden till api:et för [förutsägelseslutpunkt.](https://go.microsoft.com/fwlink/?linkid=2092356) API:et tillämpar appen på uttryck för analys och svarar med förutsägelseresultaten i ett JSON-format.

Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. Det kan också extrahera data som följande **kontakttyp** entitet och övergripande sentiment.

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

När LUIS-appen har publicerats och tar emot verkliga användaryttranden ger LUIS [aktiv inlärning](luis-concept-review-endpoint-utterances.md) av slutpunktsyttranden för att förbättra förutsägelsenoggrannheten.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Utvecklingscykel
LUIS tillhandahåller verktyg, versionshantering och samarbete med andra LUIS-författare för att integreras i hela [utvecklingslivscykeln](luis-concept-app-iteration.md).

## <a name="implementing-luis"></a>Implementera LUIS
Språkförståelse (LUIS), som ett REST API, kan användas med valfri produkt, tjänst eller ramverk med en HTTP-begäran. Följande lista innehåller de främsta Microsoft-produkterna och -tjänsterna som används med LUIS.

Det vanligaste klientprogram för LUIS är:
* [Web app bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) skapar snabbt en LUIS-aktiverad chattrobot för att prata med en användare via textinmatning. Använder [Bot Framework][bot-framework] version [4.x](https://github.com/Microsoft/botbuilder-dotnet) för en komplett botupplevelse.

Verktyg för att snabbt och enkelt använda LUIS med en robot:
* [LUIS CLI (OLIKA)](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) NPM-paketet ger redigering och förutsägelse med som antingen ett fristående kommandoradsverktyg eller som import.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen är ett verktyg för att generera starkt typbestämd C#- och TypeScript-källkod från en exporterad LUIS-modell.
* Med [Dispatch](https://aka.ms/dispatch-tool) kan flera LUIS- och QnA Maker-appar användas via en överordnad app som använder en dispatcher-modell.
* [LUDown (på andra)](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown är ett kommandoradsverktyg som hjälper till att hantera språkmodeller för din robot.
* [Bot framework - Composer](https://github.com/microsoft/BotFramework-Composer) - ett integrerat utvecklingsverktyg för utvecklare och tvärvetenskapliga team för att bygga robotar och konversationsupplevelser med Microsoft Bot Framework

Andra Cognitive Services-tjänster som används med LUIS:
* Med [QnA Maker][qnamaker] kan flera typer av text kombineras till en kunskapsbas med frågor och svar.
* [Speech Service](../Speech-Service/overview.md) konverterar begäranden med talat språk till text.
* Med [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) kan du skapa robotkonversationer snabbare med LUIS.

Exempel som använder LUIS:
* GitHub-lagringsplatsen [Conversational AI](https://github.com/Microsoft/AI).
* [Bot ram - Bot prover](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Nästa steg

* [Nyheter](whats-new.md)
* Skapa en ny LUIS-app med en [fördefinierad](luis-get-started-create-app.md) eller [anpassad](luis-quickstart-intents-only.md) domän.
* [Fråga slutpunkten för förutsägelse](luis-get-started-get-intent-from-browser.md) för en offentlig IoT-app.
* [Utvecklarresurser](developer-reference-resource.md) för LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
