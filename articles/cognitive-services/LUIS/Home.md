---
title: Om språket förstå (THOMAS) i Azure | Microsoft Docs
description: Lär dig använda språk förstå (THOMAS) för att styrkan av maskininlärning för att dina program.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: v-geberr
ms.openlocfilehash: bbd0a532e54f9b221739c8ae9ff097fe44fdc4df
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751603"
---
# <a name="what-is-language-understanding-luis"></a>Vad är språket förstå (THOMAS)?
Språk förstå (THOMAS) är en molnbaserad tjänst som använder anpassade maskininlärning i en användares vardagliga samtalsuttryck, naturligt språk texten att förutsäga övergripande mening och dra ut relevanta, detaljerad information. 

Ett klientprogram för THOMAS kan vara ett vardagliga samtalsuttryck program som kommunicerar med en användare i naturligt språk att slutföra en aktivitet. Exempel på program är sociala medier appar, chatbots och tal-aktiverade program.  

![Bild av mata information info THOMAS 3-program](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>Vad är en THOMAS app?
En THOMAS app innehåller en modell för domänspecifika naturligt språk som du skapar. Du kan starta THOMAS appen med en fördefinierad domänmodell, skapa egna eller blandas delar av en fördefinierad domän med din egen anpassade information.

[Fördefinierade domänmodeller](luis-how-to-use-prebuilt-domains.md) innehåller dessa uppgifter du och är ett bra sätt att börja använda THOMAS snabbt.

THOMAS appen även innehåller inställningar för katalogintegrering [medarbetare](luis-concept-collaborator.md), och [versioner](luis-concept-version.md).

## <a name="using-a-luis-app"></a>Med en app som THOMAS
<a name="Accessing-LUIS"></a> När appen THOMAS publiceras klientprogrammet skickar utterances till THOMAS [endpoint API] [ endpoint-apis] och tar emot förutsägelse resultatet som JSON-svar.

I följande diagram skickar klient-chatbot först användaren texten för en person som vill i sina egna ord att THOMAS i en HTTP-begäran. Därefter THOMAS gäller inlärda modellen naturliga språket som ska vara meningsfullt för användardata och returnerar ett svar för JavaScript Object Notation (JSON)-format. Klient-chatbot använder tredje JSON-svar för att uppfylla användarens begäran. 

![Konceptuell bilder visar färgerna i THOMAS arbeta med Chatbot](./media/luis-overview/luis-overview-process-2.png)

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
<a name="what-is-a-luis-model"></a>
## <a name="what-is-a-natural-language-model"></a>Vad är en modell med naturligt språk?
En modell som börjar med en lista över allmänna användardata avsikter kallas _intents_, till exempel ”Book svarta” eller ”kontakta supportavdelningen”. Du anger användarens exempeltexten kallas _exempel utterances_ för innehållet. Markera betydande ord eller fraser i utterance, kallas _entiteter_.


En modellen innehåller:

* **[intents](#intents)**: kategorier av användaren avsikter (avsedda åtgärden eller resultatet)
* **[entiteter](#entities)**: specifika typer av data i utterances, till exempel tal, e-post eller namn
* **[exempel utterances](#example-utterances)**: exempeltexten som användaren använder i klientprogrammet

### <a name="intents"></a>Avsikter 
En [avsikt](luis-how-to-add-intents.md), kort för _avsikt_, är ett syfte eller målet uttryckas i en användares utterance, till exempel Bokningstyp en svarta, betalar en faktura eller söka efter en nyhetsartikel i. Du skapar en avsett för varje åtgärd. En THOMAS resa app kan definiera syftet med namnet ”BookFlight”. Ditt klientprogram kan använda upp bedömningen avsikt för att utlösa en åtgärd. När ”BookFlight” avsikt returneras från THOMAS kan ditt klientprogram utlösa ett API-anrop till en extern tjänst för Bokningstyp plan biljett.

### <a name="entities"></a>Entiteter
En [entiteten](luis-how-to-add-entities.md) representerar detaljerad information finns i utterance som är relevant för användarens begäran. Exempelvis en enda biljett begärs i utterance ”Book biljetter för Paris” och ”Paris” är en plats. Två entiteter finns ”en biljett” som anger en enda biljetter och ”Paris” som anger målet. 

När THOMAS returnerar entiteter som finns i användarens utterance använda klientprogrammet listan över enheter som parametrar för att utlösa en åtgärd. Till exempel kräver Bokningstyp en svarta entiteter som resa mål, datum och flygbolag.

THOMAS finns flera sätt att identifiera och klassificera entiteter.

* **Fördefinierade entiteter** THOMAS har många fördefinierade domänmodeller inklusive avsikter utterances, och [färdiga entiteter](pre-builtentities.md). Du kan använda de fördefinierade enheterna utan att använda avsikter och utterances av fördefinierade modellen. De färdiga enheterna spara tid.

* **Anpassade entiteter** THOMAS finns flera sätt att identifiera dina egna anpassade [entiteter](luis-concept-entity-types.md) inklusive datorn inlärda entiteter, särskilda eller literal entiteter och en kombination av datorn lärt dig och literal.

### <a name="example-utterances"></a>Exempel utterances
Ett exempel [utterance](luis-how-to-add-example-utterances.md) är text indata från användaren som klientprogrammet behöver förstå. Det kan vara en mening, t.ex. ”boka en biljett till Paris” eller ett fragment i en mening, t.ex. ”boka” eller ”Paris svarta”. Utterances alltid är inte korrekt, och det kan finnas många utterance variationer för en viss avsikt. Lägg till 10 till 20 exempel utterances varje avsikt och markera entiteter i varje utterance.

|Exempel användaren utterance|Avsikten|Entiteter|
|-----------|-----------|-----------|
|”Boka en rör sig till __Seattle__”?|BookFlight|Seattle|
|”När har din store __öppna__”?|StoreHoursAndLocation|öppna|
|”Träffa på __1 pm__ med __Bob__ distribution”|ScheduleMeeting|1 pm Bob|

## <a name="improve-prediction-accuracy"></a>Prognosens noggrannhet kan förbättras
När appen THOMAS publiceras och tar emot användare utterances, THOMAS erbjuder flera metoder för att förbättra förutsägelsefunktionen: [active learning](#active-learning) av slutpunkten utterances [fras listor](#phrase-lists) för domänen Word införandet och [mönster](#patterns) att minska antalet utterances som behövs.

### <a name="active-learning"></a>Aktiv inlärning
I den [active learning](label-suggested-utterances.md) processen, THOMAS kan du anpassa din app THOMAS verkliga utterances genom att välja utterances togs emot vid slutpunkten för din granskning. Du kan acceptera eller korrigera endpoint förutsägelse, omtrimning och publicera på nytt. THOMAS lär sig snabbt med den här iterativ processen tar den minsta mängden din tid och arbete. 

### <a name="phrase-lists"></a>Frasen listor 
THOMAS ger [fraser listor](luis-concept-feature.md) så anger du viktiga ord eller fraser till din domän i modellen. THOMAS använder dessa listor för att lägga till ytterligare betydelse i dessa ord och fraser som inte skulle annars kan hittas i modellen.

### <a name="patterns"></a>Mönster 
Mönster kan du förenkla en avsikt utterance samling till gemensamma [mallar](luis-concept-patterns.md) av word val och word ordning. Detta gör att THOMAS lära sig snabbare genom att behöva färre exempel utterances för innehållet. Mönster är ett hybrid-system med reguljära uttryck och datorn lärt dig uttryck. 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>Redigering och åtkomst till THOMAS
Skapa din THOMAS app från webbplatsen THOMAS eller programmässigt med den [redigering](https://aka.ms/luis-authoring-apis) API: er eller använda båda beroende på behovet av redigering. Åtkomst till publicerade THOMAS appen av frågan [endpoint](https://aka.ms/luis-endpoint-apis). 

THOMAS innehåller tre webbplatser runtom i världen, beroende på region redigering. Redigering region anger Azure-regionen där du kan publicera appen THOMAS.
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

Läs [mer](luis-reference-regions.md) om redigering och publicering av regioner.

## <a name="what-technologies-work-with-luis"></a>Vilka tekniker fungerar med THOMAS?
Flera Microsoft-tekniker fungerar med THOMAS:

* [Bing stavningskontroll kontrollera API](../bing-spell-check/proof-text.md) innehåller Textkorrigering innan förutsägelse. 
* [Bot Framework] [ bot-framework] kan en chatbot så att den kommunicerar med en användare via textinmatning. Välj [3.x](https://github.com/Microsoft/BotBuilder) eller [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK för en fullständig bot upplevelse.
* [Frågor och svar om Maker] [ qnamaker] tillåter flera typer för att kombinera i en fråga och svar knowledge base.
* [Tal](../Speech/home.md) konverterar talas begäranden till text. När konverteras till text, bearbetar THOMAS begäranden. Se [tal SDK](https://aka.ms/csspeech) för mer information.
* [Textanalys](../text-analytics/overview.md) ger sentiment analys och nyckeln frasen data extrahering.

## <a name="next-steps"></a>Nästa steg
Skapa en ny THOMAS app med en [färdiga](luis-get-started-create-app.md) eller [anpassade](luis-quickstart-intents-only.md) domän.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/