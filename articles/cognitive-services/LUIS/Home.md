---
title: Om språket förstå (THOMAS) i Azure | Microsoft Docs
description: Lär dig använda språk förstå (THOMAS) för att styrkan av maskininlärning för att dina program.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/22/2017
ms.author: v-geberr
ms.openlocfilehash: 5656ce051a171812e6d008d56b42bf16c0c2a2ca
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356129"
---
# <a name="what-is-language-understanding-luis"></a>Vad är språket förstå (THOMAS)?
Språk förstå (THOMAS) är en molnbaserad tjänst som använder anpassade maskininlärning i en användares vardagliga samtalsuttryck, naturligt språk texten att förutsäga övergripande mening och dra ut relevanta, detaljerad information. 

Ett klientprogram för THOMAS kan vara ett vardagliga samtalsuttryck program som kommunicerar med en användare i naturligt språk att slutföra en aktivitet. Exempel på program är sociala medier appar, chatbots och tal-aktiverade program.  

![Bild av mata information info THOMAS 3-program](./media/luis-overview/luis-entry-point.png)

Ditt klientprogram (till exempel en chatbot) skickar användaren texten för en person som vill i sina egna ord att THOMAS i en HTTP-begäran. THOMAS gäller inlärda modellen naturliga språket som ska vara meningsfullt för användardata och returnerar ett svar för JSON-format. Klientprogrammet använder JSON-svar för att uppfylla användarens begäran. 

![Konceptuell bilder visar färgerna i THOMAS arbeta med Chatbot](./media/luis-overview/luis-overview-process-2.png)

## <a name="what-is-a-luis-app"></a>Vad är en THOMAS app?
En THOMAS app är en domän-specifika språk du utformar. Du kan starta din app med en fördefinierad domänmodell, skapa egna eller blandas delar av en fördefinierad domän med din egen anpassade information.

En modell som börjar med en lista över allmänna användardata avsikter kallas _intents_, till exempel ”Book svarta” eller ”kontakta supportavdelningen”. Du kan ange användarens exempel fraser, kallas _utterances_ för innehållet. Markera betydande ord eller fraser i utterance, kallas _entiteter_.

[Fördefinierade domänmodeller] [ prebuilt-domains] innehåller dessa uppgifter du och är ett bra sätt att börja använda THOMAS snabbt.

<a name="Accessing-LUIS"></a>

När din modell har skapats och publicerats, klientprogrammet skickar utterances till THOMAS [endpoint API] [ endpoint-apis] och tar emot förutsägelse resultatet som JSON-svar.

### <a name="example-of-json-endpoint-response"></a>Exempel på JSON-svar för slutpunkten

JSON-slutpunkt svar, som ett minimum innehåller frågan utterance och upp bedömningen avsikt. 

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

<a name="Key-LUIS-concepts"></a>

## <a name="what-is-a-luis-model"></a>Vad är en THOMAS modell?
En THOMAS modellen innehåller:

* **[intents](#intents)**: kategorier av användaren avsikter (avsedda åtgärden eller resultatet)
* **[entiteter](#entities)**: specifika typer av data i utterances, till exempel tal, e-post eller namn
* **[exempel utterances](#example-utterances)**: exempeltexten som en användare anger i ditt klientprogram

### <a name="intents"></a>Avsikter 
En [avsikt][add-intents], kort för _avsikt_, är ett syfte eller målet uttryckas i en användares utterance, till exempel Bokningstyp en svarta, betalar en faktura eller söka efter en nyhetsartikel i. Du skapar en avsett för varje åtgärd. En resa app kan definiera syftet med namnet ”BookFlight”. Ditt klientprogram kan använda upp bedömningen avsikt för att utlösa en åtgärd. När ”BookFlight” avsikt returneras från THOMAS kan ditt klientprogram utlösa ett API-anrop till en extern tjänst för Bokningstyp plan biljett.

### <a name="entities"></a>Entiteter
En [entiteten] [ add-entities] representerar detaljerad information finns i utterance som är relevant för användarens begäran. Exempelvis en enda biljett begärs i utterance ”Book biljetter för Paris” och ”Paris” är en plats. Två entiteter finns ”en biljett” som anger en enda biljetter och ”Paris” som anger målet. 

När THOMAS returnerar entiteter som finns i användarens utterance, kan ditt klientprogram använda listan över enheter som parametrar till en utlösta åtgärd. Till exempel kräver Bokningstyp en svarta entiteter som resa mål, datum och flygbolag.

THOMAS finns flera sätt att identifiera och klassificera entiteter.

* **Fördefinierade entiteter** THOMAS har många fördefinierade domänmodeller inklusive avsikter utterances, och [färdiga entiteter][prebuilt-entities]. Du kan använda de fördefinierade enheterna utan att använda avsikter och utterances av fördefinierade modellen. De färdiga enheterna spara tid.

* **Anpassade entiteter** THOMAS finns flera sätt att identifiera dina egna anpassade [entiteter] [ entity-concept] inklusive datorn inlärda entiteter, särskilda eller literal entiteter och en kombination av datorn lärt dig och literal.

### <a name="example-utterances"></a>Exempel utterances
Ett exempel [utterance] [ add-example-utterances] är text indata från användaren som din app behöver förstå. Det kan vara en mening, t.ex. ”boka en biljett till Paris” eller ett fragment i en mening, t.ex. ”boka” eller ”Paris svarta”. Utterances alltid är inte korrekt, och det kan finnas många utterance variationer för en viss avsikt. Lägg till 10 till 20 exempel utterances varje avsikt och markera entiteter i varje utterance.

|Exempel användaren utterance|Avsikten|Entiteter|
|-----------|-----------|-----------|
|”Boka en rör sig till __Seattle__”?|BookFlight|Seattle|
|”När har din store __öppna__”?|StoreHoursAndLocation|öppna|
|”Träffa på __1 pm__ med __Bob__ distribution”|ScheduleMeeting|1 pm Bob|

## <a name="improve-prediction-accuracy"></a>Prognosens noggrannhet kan förbättras
När programmet har publicerats och tar emot användare utterances, THOMAS erbjuder flera metoder för att förbättra förutsägelsefunktionen: [active learning](#active-learning) av slutpunkten utterances [fras listor](#phrase-lists) för domän word inkludering och [mönster](#patterns) att minska antalet utterances som behövs.

### <a name="active-learning"></a>Aktiv inlärning
I den [active learning](label-suggested-utterances.md) processen, THOMAS kan du anpassa din app till verkliga utterances genom att välja utterances togs emot vid slutpunkten för din granskning. Du kan acceptera eller korrigera endpoint förutsägelse, omtrimning och publicera på nytt. THOMAS lär sig snabbt med den här iterativ processen tar den minsta mängden din tid och arbete. 

### <a name="phrase-lists"></a>Frasen listor 
THOMAS ger [fraser listor](luis-concept-feature.md) så anger du viktiga ord eller fraser till din domän i modellen. THOMAS använder dessa listor för att lägga till ytterligare betydelse i dessa ord och fraser som inte skulle annars kan hittas i modellen.

### <a name="patterns"></a>Mönster 
Mönster kan du förenkla en avsikt utterance samling till gemensamma [mallar] [ patterns] av word val och word ordning. Detta gör att THOMAS lära sig snabbare genom att behöva färre exempel utterances för innehållet. Mönster är ett hybrid-system med reguljära uttryck och datorn lärt dig uttryck. 

## <a name="using-luis"></a>Med hjälp av THOMAS
Du kan bygga appen THOMAS från den [www.luis.ai](http://www.luis.ai) webbplatsen och du kan skapa ditt program via programmering med den [redigering](https://aka.ms/luis-authoring-apis) API: er. Åtkomst till publicerade THOMAS appen av frågan [endpoint](https://aka.ms/luis-endpoint-apis). 

## <a name="what-technologies-work-with-luis"></a>Vilka tekniker fungerar med THOMAS?
Flera Microsoft-tekniker fungerar med THOMAS:

* [Bing stavningskontroll kontrollera API] [ bing-spell-check-api] innehåller Textkorrigering innan förutsägelse. 
* [Bot Framework] [ bot-framework] kan en chatbot så att den kommunicerar med en användare via textinmatning. Välj [3.x](https://github.com/Microsoft/BotBuilder) eller [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK för en fullständig bot upplevelse.
* [Frågor och svar om Maker] [ qnamaker] tillåter flera typer för att kombinera i en fråga och svar knowledge base.
* [Tal] [ speech] konverterar talas begäranden till text. När konverteras till text, bearbetar THOMAS begäranden. Se [tal SDK](https://aka.ms/csspeech) för mer information.
* [Textanalys] [ text-analytics] ger sentiment analys och nyckeln frasen data extrahering.

## <a name="next-steps"></a>Nästa steg
Skapa en [ny THOMAS app](LUIS-get-started-create-app.md).

<!-- Reference-style links -->
[create-app]:luis-get-started-create-app.md
[azure-portal]:https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account
[publish-app]:PublishApp.md#test-your-published-endpoint-in-a-browser
[luis-concept-entity-types]:luis-concept-entity-types.md
[add-example-utterances]: luis-how-to-add-example-utterances.md
[prebuilt-entities]: pre-builtentities.md
[prebuilt-domains]: luis-how-to-use-prebuilt-domains.md
[label-suggested-utterances]: label-suggested-utterances.md
[intro-video]:https://aka.ms/LUIS-Intro-Video
[bot-framework]:https://docs.microsoft.com/bot-framework/
[speech]:../Speech/index.md
[flow]:https://docs.microsoft.com/connectors/luis/
[entity-concept]:luis-concept-entity-types.md
[add-intents]:luis-how-to-add-intents.md
[add-entities]:luis-how-to-add-entities.md
[authoring-apis]:https://aka.ms/luis-authoring-api
[endpoint-apis]:https://aka.ms/luis-endpoint-apis
[LUIS]:luis-reference-regions.md
[text-analytics]:https://azure.microsoft.com/services/cognitive-services/text-analytics/
[patterns]:luis-concept-patterns.md
[bing-spell-check-api]:https://azure.microsoft.com/services/cognitive-services/spell-check/
[qnamaker]:https://qnamaker.ai/