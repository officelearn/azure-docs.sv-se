---
title: Bra exempel yttranden
titleSuffix: Language Understanding - Azure Cognitive Services
description: Yttranden indata från användaren som appen behöver att tolka. Samla in fraser som du tror att användare ska ange. Inkludera yttranden som betyda samma sak, men är konstruerade på olika sätt i word längd och word placering.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 63334c861cc85c7119ccd3111429dee47ada3162
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862946"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Förstå vad bra yttranden är avsedda för LUIS-app

**Yttranden** indata från användaren som appen behöver att tolka. För att träna LUIS för att extrahera avsikter och entiteter från dem, är det viktigt att samla in en mängd olika exempel yttranden för varje avsikt. Aktiv inlärning eller processen med att fortsätta att utbilda i nya yttranden är mycket viktigt att datorn lärt dig intelligens som innehåller LUIS.

Samla in yttranden som du tror att användare ska ange. Inkludera yttranden som betyda samma sak men skapas i en mängd olika sätt:

* Uttryck längd - kort, medel och lång för ditt klientprogram
* Längd för ord och fraser 
* Placering av Word - entiteten i början, i mitten och slutet av uttryck
* Grammatik 
* Pluralization
* Ordstamsigenkänning
* Substantiv och verb val
* Skiljetecken - bra olika med rätt, fel och ingen grammatik

## <a name="how-to-choose-varied-utterances"></a>Hur du väljer olika yttranden

När du först komma igång genom [lägger du till exempel yttranden](luis-how-to-add-example-utterances.md) till LUIS-modellen, här finns vissa principer att ha i åtanke.

### <a name="utterances-arent-always-well-formed"></a>Yttranden stöddata inte alltid

Det kan vara en mening, till exempel ”boka en biljett till Paris för mig” eller ett fragment i en mening, t.ex. ”bokning” eller ”Paris flygning”.  Användare kan du ofta göra stavfel. När du planerar din app bör tänka på om huruvida du använder [Bing stavningskontroll Check][(luis-tutorial-bing-spellcheck) att korrigera indata från användaren innan det skickas till LUIS. 

Om du inte stavningskontroll kontroll användaren yttranden, bör du tränar LUIS på yttranden som innehåller Skriv- och stavfel.

### <a name="use-the-representative-language-of-the-user"></a>Använd språket som är representativ för användaren

När du väljer yttranden, Tänk på att vad du tycker är en vanlig term eller frasen inte kanske korrekt för normal användare av ditt klientprogram. De kan inte ha domän upplevelse. Var försiktig när du använder termer eller fraser som en användare bara skulle säger om de vore en expert.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Välj olika terminologi samt formulering

Du kommer märka att även om du gör för att skapa olika mening mönster kan du fortfarande upprepas vissa ordförråd.

Utföra dessa exempel yttranden:

|Exempel på yttranden|
|--|
|hur får jag en dator?|
|Var hittar jag en dator?|
|Jag vill ha en dator, hur går jag om den?|
|När kan jag har en dator?| 

Här, core termen inte är ”dator” olika. Använda alternativ som stationär dator, bärbar dator, arbetsstation eller även bara datorn. LUIS härleder smart synonymer från kontext, men när du skapar uttryck för träning, är det fortfarande är bättre att variera dem.

## <a name="example-utterances-in-each-intent"></a>Exempel yttranden i varje avsikt

Varje avsikt måste ha exempel yttranden, minst 15. Om du har ett intent som inte har yttranden exempel kan du inte träna LUIS. Om du har en syftet med en eller mycket få exempel yttranden kommer LUIS inte korrekt förutse avsikten. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Lägg till små grupper med 15 yttranden för varje redigering upprepning

Lägg inte till ett stort antal yttranden i varje iteration av modellen. Lägg till yttranden i mängder 15. [Träna](luis-how-to-train.md), [publicera](luis-how-to-publish-app.md), och [testa](luis-interactive-test.md) igen.  

LUIS skapar effektiva modeller med yttranden som väljs noggrant installationskommandot LUIS-modellen. Att lägga till för många yttranden inte värdefulla eftersom förvirring.  

Är det bättre att börja med ett par yttranden sedan [granska endpoint yttranden](luis-how-to-review-endoint-utt.md) för rätt avsikt extrahering av förutsägelser och entiteten.

## <a name="punctuation-marks"></a>Skiljetecken

LUIS går inte att Ignorera skiljetecken, som standard, eftersom alla klientprogram kan placera betydelse på dessa märken. Kontrollera att ditt exempel yttranden använder både interpunktion och inga skiljetecken för båda formaten för att returnera samma relativa poängen. Om skiljetecken har ingen specifik betydelse i klientprogrammet, bör du [Ignorerar skiljetecken](#ignoring-words-and-punctuation) med hjälp av mönster. 

## <a name="ignoring-words-and-punctuation"></a>Ignorerar ord och skiljetecken

Om du vill ignorera vissa specifika ord eller skiljetecken i exempel-uttryck kan använda en [mönstret](luis-concept-patterns.md#pattern-syntax) med den _Ignorera_ syntax. 

## <a name="training-utterances"></a>Utbildning yttranden

Utbildning som är allmänt icke-deterministisk: uttryck förutsägelsen kan variera något mellan versioner eller appar. Du kan ta bort icke-deterministisk utbildning genom att uppdatera den [inställningarna version](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API med den `UseAllTrainingData` namn/värde-par att använda alla träningsdata.

## <a name="testing-utterances"></a>Testa yttranden 

Utvecklare bör börja testa sina LUIS-program med verklig trafik genom att skicka yttranden till den [förutsägelse endpoint](luis-how-to-azure-subscription.md) URL: en. Dessa uttryck används för att förbättra prestandan för avsikter och entiteter med [granska yttranden](luis-how-to-review-endoint-utt.md). Tester som skickades med webbplatsen LUIS testning fönstret skickas inte via slutpunkten och därför bidrar inte till aktiv inlärning. 

## <a name="review-utterances"></a>Granska yttranden

När din modell är tränade publicerade och mottagande [endpoint](luis-glossary.md#endpoint) frågor, [granska talade](luis-how-to-review-endoint-utt.md) föreslås av LUIS. LUIS väljer endpoint yttranden som har låg poäng för avsikt eller entitet. 

## <a name="best-practices"></a>Bästa praxis

Granska [bästa praxis](luis-concept-best-practices.md) och använda dem som en del av din vanliga redigering cykel.

## <a name="next-steps"></a>Nästa steg
Se [lägger du till exempel yttranden](luis-how-to-add-example-utterances.md) information om hur du tränar en LUIS-app för att förstå användaren yttranden.

