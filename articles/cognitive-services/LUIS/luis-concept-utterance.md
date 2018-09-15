---
title: Yttranden i LUIS-appar
titleSuffix: Azure Cognitive Services
description: Yttranden indata från användaren som appen behöver att tolka. Samla in fraser som du tror att användare ska ange. Inkludera yttranden som betyda samma sak, men är konstruerade på olika sätt i word längd och word placering.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: c455296348e3b4378a2912fa5388098b206b3ea4
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632321"
---
# <a name="utterances-in-luis"></a>Yttranden i LUIS

**Yttranden** indata från användaren som appen behöver att tolka. För att träna LUIS för att extrahera avsikter och entiteter från dem, är det viktigt att samla in en mängd olika indata för varje avsikt. Aktiv inlärning eller processen med att fortsätta att utbilda i nya yttranden är mycket viktigt att datorn lärt dig intelligens som innehåller LUIS.

Samla in fraser som du tror att användare ska ange. Inkludera yttranden som betyda samma sak, men är konstruerade på olika sätt i word längd och word placering. 

## <a name="how-to-choose-varied-utterances"></a>Hur du väljer olika yttranden
När du först komma igång genom [lägger du till exempel yttranden](luis-how-to-add-example-utterances.md) till LUIS-modellen, här finns vissa principer att ha i åtanke.

### <a name="utterances-arent-always-well-formed"></a>Yttranden stöddata inte alltid
Det kan vara en mening, till exempel ”boka mig en biljett till Paris” eller ett fragment i en mening, t.ex. ”bokning” eller ”Paris flygning”.  Användare kan du ofta göra stavfel. När du planerar din app bör du överväga att huruvida du stavningskontroll indata från användaren innan det skickas till LUIS. Den [stavningskontroll i Bing] [ BingSpellCheck] kan integreras med LUIS. Du kan associera LUIS-app med en nyckel som är externa för den stavningskontroll i Bing när du publicerar den. Om du inte stavningskontroll kontroll användaren yttranden, bör du tränar LUIS på yttranden som innehåller Skriv- och stavfel.

### <a name="use-the-representative-language-of-the-user"></a>Använd språket som är representativ för användaren
När du väljer yttranden, Tänk på att vad du tycker är en vanlig term eller frasen inte kanske för vanliga användare av ditt klientprogram. De kan inte ha domän upplevelse. Så var försiktig när du använder termer eller fraser som en användare bara skulle säger om de vore en expert.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Välj olika terminologi samt formulering
Du kommer märka att även om du gör för att skapa olika mening mönster kan du fortfarande upprepas vissa ordförråd.

Utföra dessa exempel yttranden:

|Exempel på yttranden|
|--|
|hur får jag en dator?|
|Var hittar jag en dator?|
|Jag vill ha en dator, hur går jag om den?|
|När kan jag har en dator?| 

Här, core termen är ”dator” inte variera. De kan säga stationär dator, bärbar dator, arbetsstation eller även bara datorn. LUIS härleder smart synonymer från kontext, men när du skapar uttryck för träning, är det fortfarande är bättre att variera dem.

## <a name="example-utterances-in-each-intent"></a>Exempel yttranden i varje avsikt
Varje avsikt måste ha exempel yttranden, minst 10 – 15. Om du har ett intent som inte har yttranden exempel kan du inte träna LUIS. Om du har en syftet med en eller mycket få exempel yttranden kommer LUIS inte korrekt förutse avsikten. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>Lägg till små grupper med 10 – 15 yttranden för varje redigering upprepning
Lägg inte till ett stort antal yttranden i varje iteration av modellen. Lägg till yttranden i mängder tiotals. [Träna](luis-how-to-train.md), [publicera](luis-how-to-publish-app.md), och [testa](luis-interactive-test.md) igen.  

LUIS skapar effektiva modeller med yttranden som väljs noggrant. Att lägga till för många uttryck är inte värdefulla eftersom förvirring.  

Är det bättre att börja med ett par yttranden sedan [granska endpoint yttranden](luis-how-to-review-endoint-utt.md) för rätt avsikt extrahering av förutsägelser och entiteten.

## <a name="ignoring-words-and-punctuation"></a>Ignorerar ord och skiljetecken
Om du vill ignorera vissa specifika ord eller skiljetecken i exempel-uttryck kan använda en [mönstret](luis-concept-patterns.md#pattern-syntax) med den _Ignorera_ syntax. 

## <a name="training-utterances"></a>Utbildning yttranden
Utbildning är icke-deterministisk: uttryck förutsägelsen kan variera något mellan versioner eller appar.

## <a name="testing-utterances"></a>Testa yttranden 

Utvecklare bör börja testa sina LUIS-program med verklig trafik genom att skicka yttranden till slutpunkten. Dessa uttryck används för att förbättra prestandan för avsikter och entiteter med [granska yttranden](luis-how-to-review-endoint-utt.md). Tester som skickades med webbplatsen LUIS testning fönstret skickas inte via slutpunkten och därför bidrar inte till aktiv inlärning. 

## <a name="review-utterances"></a>Granska yttranden
När din modell är tränade publicerade och mottagande [endpoint](luis-glossary.md#endpoint) frågor, [granska talade](luis-how-to-review-endoint-utt.md) föreslås av LUIS. LUIS väljer endpoint yttranden som har låg poäng för avsikt eller entitet. 

## <a name="best-practices"></a>Bästa praxis
Granska [bästa praxis](luis-concept-best-practices.md) vill veta mer.

## <a name="next-steps"></a>Nästa steg
Se [lägger du till exempel yttranden](luis-how-to-add-example-utterances.md) information om hur du tränar en LUIS-app för att förstå användaren yttranden.

[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text