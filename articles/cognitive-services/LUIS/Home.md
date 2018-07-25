---
title: Om Språkförståelse (LUIS) i Azure | Microsoft Docs
description: Lär dig hur du använder Språkförståelse (LUIS) för att ta kraften i maskininlärning för att dina program.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: diberry
ms.openlocfilehash: 072176347adacbabc0a92f1c7e437f8233531003
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225466"
---
# <a name="what-is-language-understanding-luis"></a>Vad är Språkförståelse (LUIS)?
Språkförståelse (LUIS) är en molnbaserad tjänst som gäller anpassade machine learning för en användares konversationsanpassade, texter med naturligt språk att förutsäga övergripande betydelse och dra ut relevanta, detaljerad information. 

Ett klientprogram för LUIS kan vara konversationsanpassade program som kommunicerar med en användare på naturligt språk att slutföra en uppgift. Exempel på klientprogram är appar för sociala medier, chattrobotar och tal-aktiverade program.  

![Bild av 3 program vehicle information info LUIS](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>Vad är en LUIS-app?
En LUIS-app innehåller en domänspecifika naturligt språkmodell du utformar. Du kan påbörja din LUIS-app med en fördefinierade domänmodell, bygga egna eller blanda delar av en fördefinierade domän med din egen anpassade information.

[Fördefinierade domänmodeller](luis-how-to-use-prebuilt-domains.md) omfattar alla dessa delar du och är ett bra sätt att börja använda LUIS snabbt.

LUIS-app innehåller också inställningar för katalogintegrering [medarbetare](luis-concept-collaborator.md), och [versioner](luis-concept-version.md).

## <a name="using-a-luis-app"></a>Med hjälp av en LUIS-app
<a name="Accessing-LUIS"></a> När LUIS-appen har publicerats kan klientprogrammet skickar yttranden till LUIS [slutpunkts-API] [ endpoint-apis] och tar emot förutsägelseresultat som JSON-svaren.

I följande diagram skickar chattrobot för klienten först användaren texten i en person vill med egna ord att LUIS i en HTTP-begäran. Därefter LUIS gäller inlärda modellen för naturligt språk faller av användardata och returnerar ett svar för JavaScript Object Notation (JSON)-format. Chattrobot för klienten använder det tredje JSON-svar för att uppfylla användarens begäran. 

![Konceptuell imagery Luis arbeta med Chattrobot](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>Exempel på JSON-slutpunkt-svar

JSON-slutpunkten svar, ett minimum innehåller uttryck för fråga och upp bedömning avsikt. 

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
## <a name="what-is-a-natural-language-model"></a>Vad är en modell för naturligt språk?
En modell som börjar med en lista över allmänna användardata avsikter kallas _avsikter_, till exempel ”boken flygning” eller ”kontakta supportavdelningen”. Du anger användarens exempeltexten kallas _exempel yttranden_ för avsikter. Markera sedan betydande ord eller fraser i uttryck, kallas _entiteter_.


En modell innehåller:

* **[avsikter](#intents)**: kategorier av användaren avsikter (avsedda åtgärden eller resultat)
* **[entiteter](#entities)**: vissa typer av data i yttranden som tal, e-post eller namn
* **[exempel yttranden](#example-utterances)**: exempeltexten som en användare anger i klientprogrammet

### <a name="intents"></a>Avsikter 
En [avsikt](luis-how-to-add-intents.md), kort för _avsikt_, är ett syfte eller målet uttrycks i en användares uttryck, till exempel en flygning för bokning, betala en faktura eller att söka efter en nyhetsartikel. Du skapar ett intent för varje åtgärd. En resa LUIS-app kan definiera syftet med namnet ”BookFlight”. Klientprogrammet kan använda upp bedömning avsikt för att utlösa en åtgärd. När ”BookFlight” avsikt returneras från LUIS kan kan klientprogrammet utlösa ett API-anrop till en extern tjänst för en plan biljett för bokning.

### <a name="entities"></a>Entiteter
En [entitet](luis-how-to-add-entities.md) representerar detaljerad information hittar du i uttryck som är relevant för användarens begäran. Till exempel en enskild biljett begärs i uttryck ”boken en biljett till Paris” och ”Paris” är en plats. Två entiteter finns ”en biljett” som anger en enskild biljett och ”Paris” som anger målet. 

När LUIS returnerar entiteter som finns i användarens uttryck, använda klientprogrammet listan över entiteter som parametrar för att utlösa en åtgärd. Till exempel kräver en flygning för bokning entiteter som resmål, datum och flygbolag.

LUIS finns flera sätt att identifiera och klassificera entiteter.

* **Fördefinierade entiteter** LUIS har många fördefinierade domänmodeller inklusive avsikter yttranden, och [förskapade entiteter](luis-prebuilt-entities.md). Du kan använda fördefinierade entiteter utan att använda avsikter och yttranden av färdiga modellen. Fördefinierade entiteter du spara mycket tid.

* **Anpassade entiteter** LUIS ger dig flera olika sätt att identifiera dina egna anpassade [entiteter](luis-concept-entity-types.md) inklusive datorn lärt dig entiteter, specifika eller literal entiteter och en kombination av datorn lärt dig och literal.

### <a name="example-utterances"></a>Exempel på yttranden
Ett exempel [uttryck](luis-how-to-add-example-utterances.md) är textindata från användaren som klientprogrammet behöver förstå. Det kan vara en mening, till exempel ”boka en biljett till Paris” eller ett fragment i en mening, t.ex. ”bokning” eller ”Paris flygning”. Yttranden inte alltid korrekt strukturerad och det kan finnas många uttryck variationer för en viss avsikt. Lägg till 10 till 20 exempel yttranden till varje avsikt och markera entiteter i varje uttryck.

|Exempel användaren uttryck|Avsikten|Entiteter|
|-----------|-----------|-----------|
|”Boka en rör sig till __Seattle__”?|BookFlight|Seattle|
|”När gör din butik __öppna__”?|StoreHoursAndLocation|öppna|
|”Träffa på __1 pm__ med __Bob__ i Distribution”|ScheduleMeeting|1 pm, Bob|

## <a name="improve-prediction-accuracy"></a>Förbättra förutsägelsefunktionen
När LUIS-appen har publicerats och tar emot hur användarna yttranden, LUIS erbjuder flera metoder för att förbättra förutsägelsefunktionen: [aktiv inlärning](#active-learning) av slutpunkten yttranden [fras listor](#phrase-lists) för domän Word filinkludering och [mönster](#patterns) att minska antalet yttranden som behövs.

### <a name="active-learning"></a>Aktiv inlärning
I den [aktiv inlärning](luis-how-to-review-endoint-utt.md) processen, LUIS kan du anpassa LUIS-appen till verkliga yttranden genom att välja yttranden som togs emot vid slutpunkten för granskning. Du kan acceptera eller korrigera de endpoint förutsägelse, omtrimning och publicera på nytt. LUIS lär sig snabbt med den här iterativ process tar den minsta mängden din tid och arbete. 

### <a name="phrase-lists"></a>Fraslistor 
LUIS ger [fraser listor](luis-concept-feature.md) så att du kan ange viktiga ord eller fraser till din modell-domän. LUIS använder dessa listor för att lägga till ytterligare betydelse i dessa ord och fraser som inte skulle annars hittas i modellen.

### <a name="patterns"></a>Mönster 
Mönster kan du förenkla ett intent uttryck samling till vanliga [mallar](luis-concept-patterns.md) av word valmöjligheter och ordföljden. På så sätt kan LUIS för att lära dig snabbare genom att du behöver använda färre exempel yttranden avsikter. Mönster är ett hybridsystem av reguljära uttryck och datorn lärt dig uttryck. 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>Redigering och komma åt LUIS
Skapa din LUIS-app från LUIS-webbplats eller via programmering med den [redigering](https://aka.ms/luis-authoring-apis) API: er eller Använd båda beroende på behovet av redigering. Komma åt din publicerade LUIS-app genom att frågan [endpoint](https://aka.ms/luis-endpoint-apis). 

LUIS innehåller tre webbplatser över hela världen, beroende på region redigering. Regionen redigering anger den Azure-region där du kan publicera LUIS-appen.
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

Lär dig [mer](luis-reference-regions.md) om redigering och publicera regioner.

## <a name="what-technologies-work-with-luis"></a>Vilka tekniker som fungerar med LUIS?
Flera Microsoft-tekniker fungerar med LUIS:

* [Stavningskontroll i Bing](../bing-spell-check/proof-text.md) innehåller Textkorrigering innan förutsägelse. 
* [Bot Framework] [ bot-framework] tillåter en chattrobot att kommunicera med en användare via textinmatning. Välj [3.x](https://github.com/Microsoft/BotBuilder) eller [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK för en fullständig bot-upplevelse.
* [QnA Maker] [ qnamaker] tillåter flera typer av text ska kombineras till en kunskapsbas med frågor och svar.
* [Tal](../Speech/home.md) konverterar talat språk begäranden till text. När konverteras till text, bearbetar LUIS begäranden. Se [tal SDK](https://aka.ms/csspeech) för mer information.
* [Textanalys](../text-analytics/overview.md) ger sentiment analys och nyckeln frasen extrahering av data.

## <a name="next-steps"></a>Nästa steg
Skapa en ny LUIS-app med en [fördefinierade](luis-get-started-create-app.md) eller [anpassade](luis-quickstart-intents-only.md) domän.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/