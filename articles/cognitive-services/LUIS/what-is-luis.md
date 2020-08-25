---
title: Vad är Language Understanding Intelligent Service (LUIS)?
description: Language Understanding (LUIS) – en molnbaserad API-tjänst som använder maskin inlärning till konversationer, naturligt språk för att förutsäga innebörd och extrahering av information.
keywords: Azure, artificiell intelligens, AI, naturlig språk bearbetning, NLP, natur språks förståelse, NLU, AI-konversation, konversations-AI, AI-chattrobot, chattrobot Maker, LUIS, NLP AI, Luis AI, Azure Luis, förstå naturligt språk
ms.topic: overview
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 22fe99e1552a9612adfbc455d60852f1591a1a54
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752148"
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

## <a name="natural-language-understanding-nlu"></a>Natur språks förståelse (NLU)

[Luis tillhandahåller artificiell intelligens (AI)](artificial-intelligence.md) i form av NLU, en delmängd av NLP (naturlig Language Processing).

LUIS-appen innehåller en domänbaserad naturlig språk modell. Du kan starta LUIS-appen med en fördefinierad domänmodell, bygga din egen modell eller blanda delar av en fördefinierad modell med egen anpassad information.

* LUIS med**fördefinierad modell** har många fördefinierade domänmodeller som innehåller avsikter, yttranden och fördefinierade yttranden. Du kan använda de fördefinierade entiteterna utan att behöva använda den fördefinierade modellens avsikter och yttranden. [Fördefinierade domänmodeller](luis-how-to-use-prebuilt-domains.md) innehåller hela designen för dig och är ett bra sätt att börja använda LUIS snabbt.

* **Anpassad modell** LUIS ger dig flera olika sätt att identifiera dina egna anpassade modeller, inklusive avsikter och entiteter. Entiteter innehåller enheter för maskin inlärning, speciella eller litterala entiteter och en kombination av Machine-Learning och Literal.

Läs mer om [NLP](artificial-intelligence.md)och Luis NLU.

## <a name="step-1-design-and-build-your-model"></a>Steg 1: utforma och skapa din modell

Utforma din modell med kategorier av **[användar avsikter som kallas](luis-concept-intent.md)** avsikter. Varje avsikt behöver exempel på **[yttranden](luis-concept-utterance.md)** från användaren. Varje uttryck kan tillhandahålla data som måste extraheras med [enheter för maskin inlärning](luis-concept-entity-types.md#effective-machine-learned-entities).

|Exempel på användaryttrande|Avsikt|Extraherade data|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight (Boka flyg)|Seattle|
|`When does your store open?`|StoreHoursAndLocation (Butikens öppettider och plats)|open (öppen)|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting (Boka möte)|1pm, Bob (kl. 13, Bob)|

Skapa modellen med [redigerings](https://go.microsoft.com/fwlink/?linkid=2092087) -API: erna, eller med [**Luis-portalen**](https://www.luis.ai)eller båda. Lär dig mer om hur du skapar med [portalen](get-started-portal-build-app.md) och [SDK-klient biblioteken](quickstart-sdk.md).

## <a name="step-2-get-the-query-prediction"></a>Steg 2: Hämta frågan förutsägelse

När appens modell har tränats och publicerats till slut punkten skickar ett klient program (till exempel en chatt-robot) yttranden till API: et för förutsägelse [slut punkt](https://go.microsoft.com/fwlink/?linkid=2092356) . API: t tillämpar modellen på uttryck för analys och svarar med förutsägelse resultatet i JSON-format.

Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. Det kan också extrahera data som följande **kontakt typ** entitet och övergripande sentiment.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
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
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Steg 3: förbättra modell förutsägelsen

När din LUIS-app har publicerats och tar emot verkliga användar yttranden ger LUIS [aktiv inlärning](luis-concept-review-endpoint-utterances.md) av slut punkts yttranden för att förbättra förutsägelse noggrannhet. Granska dessa förslag som en del av ditt vanliga underhålls arbete i utvecklings livs cykeln.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Livs cykel och verktyg för utveckling
LUIS innehåller verktyg, versions hantering och samarbete med andra LUIS-författare som kan integreras i hela [utvecklings livs cykeln](luis-concept-app-iteration.md).

Language Understanding (LUIS), som REST API, kan användas med valfri produkt, tjänst eller ramverk med en HTTP-begäran. LUIS tillhandahåller också klient bibliotek (SDK: er) för flera viktigaste programmeringsspråk. Läs mer om de [resurser](developer-reference-resource.md) som tillhandahålls av utvecklare.

Verktyg för att snabbt och enkelt använda LUIS med en robot:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) NPM-paketet ger redigering och förutsägelse med antingen ett fristående kommando rads verktyg eller som import.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen är ett verktyg för att generera starkt typbestämd C#- och TypeScript-källkod från en exporterad LUIS-modell.
* Med [Dispatch](https://aka.ms/dispatch-tool) kan flera LUIS- och QnA Maker-appar användas via en överordnad app som använder en dispatcher-modell.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown är ett kommando rads verktyg som hjälper dig att hantera språk modeller för din robot.

## <a name="integrate-with-a-bot"></a>Integrera med en robot

Använd [Azure bot-tjänsten](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) med [Microsoft bot Framework](https://dev.botframework.com/) för att bygga och distribuera en chatt-robot. Utforma och utveckla med det grafiska gränssnitts verktyget, [Composer](https://docs.microsoft.com/composer/)eller [arbetsrobots exempel](https://github.com/microsoft/BotBuilder-Samples) som är utformade för de främsta bot scenarierna.

## <a name="integrate-with-other-cognitive-services"></a>Integrera med andra Cognitive Services

Andra Cognitive Services-tjänster som används med LUIS:
* Med [QnA Maker][qnamaker] kan flera typer av text kombineras till en kunskapsbas med frågor och svar.
* [Speech Service](../Speech-Service/overview.md) konverterar begäranden med talat språk till text.

LUIS tillhandahåller funktioner från Textanalys som en del av dina befintliga LUIS-resurser. Den här funktionen omfattar [sentiment analys](luis-how-to-publish-app.md#configuring-publish-settings) och [extrahering av nyckel fraser](luis-reference-prebuilt-keyphrase.md) med den fördefinierade nyckel frasen entitet.

## <a name="learn-with-the-quickstarts"></a>Lär dig med snabb starter

Lär dig mer om LUIS med praktiska snabb starter med hjälp av [portalen](get-started-portal-build-app.md) och [klient biblioteken för SDK](quickstart-sdk.md).


## <a name="next-steps"></a>Nästa steg

* [Vad är nytt](whats-new.md) med tjänsten och dokumentationen
* [Planera din app](luis-how-plan-your-app.md) med [avsikter](luis-concept-intent.md) och [entiteter](luis-concept-entity-types.md).
* [Fråga efter förutsägelse slut punkten](luis-get-started-get-intent-from-browser.md).
* [Utvecklings resurser](developer-reference-resource.md) för Luis.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
