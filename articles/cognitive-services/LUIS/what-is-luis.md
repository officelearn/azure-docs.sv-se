---
title: Vad är Language Understanding Intelligent Service (LUIS)?
description: Language Understanding Intelligent Service (LUIS) är en molnbaserad API-tjänst som använder anpassad maskininlärningsinformation på en användares naturliga konversationsspråk för att förutsäga den övergripande betydelsen och hämta relevant, detaljerad information.
ms.topic: overview
ms.date: 05/05/2020
ms.openlocfilehash: 231a6580a6776b82173865744e9e8757c2fa08f1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538045"
---
# <a name="what-is-language-understanding-luis"></a>Vad är Language Understanding Intelligent Service (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding Intelligent Service (LUIS) är en molnbaserad API-tjänst som använder anpassad maskininlärningsinformation på en användares naturliga konversationsspråk för att förutsäga den övergripande betydelsen och hämta relevant, detaljerad information.

Ett klientprogram för LUIS är alla konversationsanpassade program som kommunicerar med en användare på naturligt språk för att utföra en uppgift. Exempel på klientprogram är appar för sociala medier, chattrobotar och talaktiverade skrivbordsprogram.

![Konceptuell bild av 3 klient program som arbetar med Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Konceptuell bild av 3 klient program som arbetar med Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Använda LUIS i en chattrobot

<a name="Accessing-LUIS"></a>

När LUIS-appen har skickar ett klientprogram yttranden (text) till LUIS slutpunkts-[API][endpoint-apis] för bearbetning av naturligt språk och får resultat som JSON-svar. Ett vanligt klientprogram för LUIS är en chattrobot.


![Konceptuell bilder av LUIS fungerar med chatt-roboten för att förutsäga användar text med naturligt språk förståelse (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Konceptuell bilder av LUIS fungerar med chatt-roboten för att förutsäga användar text med naturligt språk förståelse (NLP")

|Steg|Action|
|:--|:--|
|1|Klientprogrammet skickar användarens _yttrande_ (text med användarens egna ord), "I want to call my HR rep." (”Jag vill ringa HR-personalen.”) till LUIS-slutpunkten som en HTTP-begäran.|
|2|Med LUIS kan du utforma dina anpassade språk modeller för att lägga till information i ditt program. Datorns förkonfigurerade språk modeller tar användarens ostrukturerade indata och returnerar ett JSON-formaterat svar, med en topp avsikt `HRContact` . Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. Det kan också extrahera data, till exempel entiteten _kontakt typ_ .|
|3|Klientprogrammet använder JSON-svaret för att fatta beslut om hur användarens begäranden sak uppfyllas. Dessa beslut kan innehålla besluts träd i bot Framework-koden och anrop till andra tjänster. |

LUIS-appen tillhandahåller information så att klientprogrammet kan fatta smarta beslut. LUIS har inte dessa alternativ.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Bearbetning av naturligt språk

LUIS-appen innehåller en domänbaserad naturlig språk modell. Du kan starta LUIS-appen med en fördefinierad domänmodell, bygga din egen modell eller blanda delar av en fördefinierad modell med egen anpassad information.

* LUIS med**fördefinierad modell** har många fördefinierade domänmodeller som innehåller avsikter, yttranden och fördefinierade yttranden. Du kan använda de fördefinierade entiteterna utan att behöva använda den fördefinierade modellens avsikter och yttranden. [Fördefinierade domänmodeller](luis-how-to-use-prebuilt-domains.md) innehåller hela designen för dig och är ett bra sätt att börja använda LUIS snabbt.

* **Anpassad modell** LUIS ger dig flera olika sätt att identifiera dina egna anpassade modeller, inklusive avsikter och entiteter. Entiteter innehåller enheter för maskin inlärning, speciella eller litterala entiteter och en kombination av Machine-Learning och Literal.

## <a name="build-the-luis-model"></a>Skapa LUIS-modellen
Skapa modellen med API: erna för [redigering](https://go.microsoft.com/fwlink/?linkid=2092087) eller med [Luis-portalen](https://www.luis.ai).

LUIS-modellen börjar med kategorier av användaravsikter som kallas för **[avsikter](luis-concept-intent.md)**. Varje avsikt behöver exempel på **[yttranden](luis-concept-utterance.md)** från användaren. Varje uttryck kan tillhandahålla data som behöver extraheras.

|Exempel på användaryttrande|Avsikt|Extraherade data|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight (Boka flyg)|Seattle|
|`When does your store open?`|StoreHoursAndLocation (Butikens öppettider och plats)|open (öppen)|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting (Boka möte)|1pm, Bob (kl. 13, Bob)|

## <a name="query-prediction-endpoint"></a>Slutpunkt för frågeförutsägelse

När din app har tränats och publicerats till slut punkten skickar klient programmet yttranden till API: et för förutsägelse [slut punkt](https://go.microsoft.com/fwlink/?linkid=2092356) . API: t tillämpar modellen på uttryck för analys och svarar med förutsägelse resultatet i JSON-format.

Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. Det kan också extrahera data som följande **kontakt typ** entitet och övergripande sentiment.

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

När din LUIS-app har publicerats och tar emot verkliga användar yttranden ger LUIS [aktiv inlärning](luis-concept-review-endpoint-utterances.md) av slut punkts yttranden för att förbättra förutsägelse noggrannhet.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Utvecklingscykel
LUIS innehåller verktyg, versions hantering och samarbete med andra LUIS-författare som kan integreras i hela [utvecklings livs cykeln](luis-concept-app-iteration.md).

## <a name="implementing-luis"></a>Implementera LUIS
Language Understanding (LUIS), som REST API, kan användas med valfri produkt, tjänst eller ramverk med en HTTP-begäran. Följande lista innehåller de främsta Microsoft-produkterna och -tjänsterna som används med LUIS.

Det vanligaste klientprogram för LUIS är:
* [Web app bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) skapar snabbt en LUIS-aktiverad chattrobot för att prata med en användare via textinmatning. Använder [bot Framework][bot-framework] version [4. x](https://github.com/Microsoft/botbuilder-dotnet) för en fullständig bot-upplevelse.

Verktyg för att snabbt och enkelt använda LUIS med en robot:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) NPM-paketet ger redigering och förutsägelse med antingen ett fristående kommando rads verktyg eller som import.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen är ett verktyg för att generera starkt typbestämd C#- och TypeScript-källkod från en exporterad LUIS-modell.
* Med [Dispatch](https://aka.ms/dispatch-tool) kan flera LUIS- och QnA Maker-appar användas via en överordnad app som använder en dispatcher-modell.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown är ett kommando rads verktyg som hjälper dig att hantera språk modeller för din robot.
* [Bot Framework-Composer](https://github.com/microsoft/BotFramework-Composer) – ett integrerat utvecklingsverktyg för utvecklare och flera disciplin team för att bygga robotar-och konversations upplevelser med Microsoft bot Framework

Andra Cognitive Services-tjänster som används med LUIS:
* Med [QnA Maker][qnamaker] kan flera typer av text kombineras till en kunskapsbas med frågor och svar.
* [Speech Service](../Speech-Service/overview.md) konverterar begäranden med talat språk till text.
* Med [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) kan du skapa robotkonversationer snabbare med LUIS.

Exempel som använder LUIS:
* GitHub-lagringsplatsen [Conversational AI](https://github.com/Microsoft/AI).
* [Bot Framework – bot-exempel](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Nästa steg

* [Nyheter](whats-new.md)
* Skapa en ny LUIS-app med en [fördefinierad](luis-get-started-create-app.md) eller [anpassad](luis-quickstart-intents-only.md) domän.
* [Fråga slutpunkten för förutsägelse](luis-get-started-get-intent-from-browser.md) för en offentlig IoT-app.
* [Utvecklings resurser](developer-reference-resource.md) för Luis.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
