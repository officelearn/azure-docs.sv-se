---
title: Användbart exempel yttranden – LUIS
titleSuffix: Azure Cognitive Services
description: Yttranden är indata från användaren som appen behöver tolka. Samla in fraser som du tror att användarna kommer att ange. Inkludera yttranden som betyder samma sak, men som har utformats annorlunda i ord-längd och ord placering.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 3c3c54faa882a38fb6c55c9fc0476a569f25cb98
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638325"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Förstå vilka bra yttranden är för din LUIS-app

**Yttranden** är indata från användaren som appen behöver tolka. För att träna LUIS att extrahera avsikter och entiteter från dem är det viktigt att samla in en mängd olika exempel yttranden för varje avsikt. Active Learning eller processen att fortsätta träna på nya yttranden är viktigt för att kunna registrera information som LUIS tillhandahåller.

Samla in yttranden som du tror att användarna kommer att ange. Ta med yttranden, vilket innebär att samma sak är konstruerad på olika sätt:

* Uttryck length – Short, medium och Long för ditt klient program
* Ord och fras längd 
* Ord placering – entitet i början, mitten och slutet av uttryck
* Grammatikfel 
* Pluralization
* Följd
* Val av Substantiv och verb
* Interpunktion – en lämplig mängd som använder korrekt, felaktig och ingen grammatik

## <a name="how-to-choose-varied-utterances"></a>Så här väljer du varierande yttranden

När du först börjar med att [lägga till exempel yttranden](luis-how-to-add-example-utterances.md) i din Luis-modell finns det några principer som du bör tänka på.

### <a name="utterances-arent-always-well-formed"></a>Yttranden är inte alltid korrekt utformade

Det kan vara en mening, t. ex. "boka en biljett till Paris för mig" eller ett fragment i en mening, t. ex. "bokning" eller "Paris flygning".  Användare gör ofta stavfel. När du planerar din app bör du överväga om du använder [stavningskontroll i Bing](luis-tutorial-bing-spellcheck.md) för att korrigera användarindata innan du skickar den till Luis. 

Om du inte stavningskontrollerar användar yttranden bör du träna LUIS på yttranden som innehåller skrivfel och fel stavningar.

### <a name="use-the-representative-language-of-the-user"></a>Använd användarens representativa språk

När du väljer yttranden bör du tänka på att vad du tycker är en vanlig term eller fras kanske inte är korrekt för den typiska användaren av klient programmet. De har kanske inte domän erfarenhet. Var försiktig när du använder termer eller fraser som en användare bara skulle säga om de vore en expert.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Välj varierande terminologi och formuleringen

Du kommer att se att även om du gör ansträngningar för att skapa olika menings mönster, kommer du fortfarande att upprepa vissa ord listor.

Gör följande exempel yttranden:

|Exempel på yttranden|
|--|
|Hur får jag en dator?|
|Var får jag en dator?|
|Jag vill skaffa en dator, hur gör jag?|
|När kan jag ha en dator?| 

Kärn termen här, "dator", är inte varierande. Använd alternativ som stationär dator, bärbar dator, arbets Station eller till och med bara dator. LUIS härleder intelligenta synonymer från kontexten, men när du skapar yttranden för utbildning är det fortfarande bättre att variera.

## <a name="example-utterances-in-each-intent"></a>Exempel på yttranden i varje avsikt

Varje avsikt måste ha exempel yttranden, minst 15. Om du har en avsikt som inte har något exempel yttranden, kan du inte träna LUIS. Om du har en avsikt med ett eller väldigt få exempel yttranden, kommer LUIS inte att förutsäga avsikten korrekt. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Lägg till små grupper på 15 yttranden för varje redigering av iterationer

I varje iteration av modellen ska du inte lägga till en stor mängd yttranden. Lägg till yttranden i kvantiteter på 15. [Träna](luis-how-to-train.md), [publicera](luis-how-to-publish-app.md)och [testa](luis-interactive-test.md) igen.  

LUIS skapar effektiva modeller med yttranden som väljs noggrant av LUIS Model-författaren. Att lägga till för många yttranden är inte värdefullt eftersom det introducerar förvirring.  

Det är bättre att börja med några få yttranden och sedan [Granska slut punkts yttranden](luis-how-to-review-endpoint-utterances.md) för korrekt matchning av avsikts förutsägelse och enhets extrahering.

## <a name="utterance-normalization"></a>Uttryck-normalisering

Uttryck normalisering är processen att ignorera effekterna av interpunktion och dia kritiska tecken under utbildning och förutsägelse.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Uttryck-normalisering för dia kritiska tecken och interpunktion

Uttryck-normalisering definieras när du skapar eller importerar appen eftersom den är en inställning i appens JSON-fil. Inställningarna för uttryck-normalisering är inaktiverade som standard. 

Dia kritiska tecken markeras eller signeras i texten, t. ex.: 

```
İ ı Ş Ğ ş ğ ö ü
```

Om din app aktiverar normalisering på, kommer resultat i **test** fönstret, batch-test och slut punkts frågor att ändras för alla yttranden med dia kritiska tecken eller skiljetecken.

Aktivera uttryck-normalisering för dia kritiska tecken eller interpunktion till din Luis JSON-app-fil i `settings` parametern.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Normalisera **interpunktion** innebär att innan dina modeller blir utbildade och innan dina slut punkts frågor hämtas, tas skiljetecken bort från yttranden. 

Normaliserar **dia kritiska** tecken ersätter tecknen med dia kritiska tecken i yttranden med vanliga tecken. Till exempel: `Je parle français` blir `Je parle francais`. 

Normalisering innebär inte att du inte ser interpunktion och dia kritiska tecken i dina exempel yttranden eller förutsägelse svar, bara att de kommer att ignoreras vid inlärning och förutsägelse.


### <a name="punctuation-marks"></a>Skiljetecken

Skiljetecken är en separat token i LUIS. En uttryck som innehåller en period i slutet jämfört med en uttryck som inte innehåller en period i slutet är två separata yttranden och kan få två olika förutsägelser. 

Om interpunktion inte är normaliserad ignorerar LUIS inte skiljetecken som standard eftersom vissa klient program kan placera signifikans på dessa märken. Se till att ditt exempel yttranden använder både skiljetecken och ingen interpunktion för att båda formaten ska returnera samma relativa resultat. 

Kontrollera att modellen hanterar skiljetecken antingen i den [exempel yttranden](luis-concept-utterance.md) (med och inte har något skiljetecken) eller i den [mönster](luis-concept-patterns.md) där det är enklare att Ignorera skiljetecken med särskild syntax: `I am applying for the {Job} position[.]`

Om interpunktionen inte har någon specifik betydelse i klient programmet, bör du överväga att [Ignorera interpunktion](#utterance-normalization) genom normalisera interpunktion. 

### <a name="ignoring-words-and-punctuation"></a>Ignorerar ord och interpunktion

Om du vill ignorera vissa ord eller interpunktion i mönster, använder du ett [mönster](luis-concept-patterns.md#pattern-syntax) med kommandot _Ignore_ för hakparenteser `[]`. 

## <a name="training-utterances"></a>Utbildning yttranden

Träning är vanligt vis icke-deterministiskt: uttryck förutsägelse kan variera något mellan versioner eller appar. Du kan ta bort icke-deterministisk utbildning genom att uppdatera [](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API för versions inställningar `UseAllTrainingData` med namn/värde-paret för att använda alla tränings data.

## <a name="testing-utterances"></a>Testa yttranden 

Utvecklare bör börja testa sitt LUIS-program med verklig trafik genom att skicka yttranden till [förutsägelse slut punktens](luis-how-to-azure-subscription.md) URL. Dessa yttranden används för att förbättra prestanda för avsikter och entiteter med gransknings [yttranden](luis-how-to-review-endpoint-utterances.md). Tester som skickats med LUIS webbplats test panel skickas inte via slut punkten och bidrar därför inte till aktiv inlärning. 

## <a name="review-utterances"></a>Granska yttranden

När din modell har tränat, publicerat och tagit emot [slut punkts](luis-glossary.md#endpoint) frågor, [granskar du yttranden](luis-how-to-review-endpoint-utterances.md) som föreslås av Luis. LUIS väljer slut punkts yttranden som har låga Poäng för antingen avsikten eller entiteten. 

## <a name="best-practices"></a>Bästa praxis

Granska [metod tips](luis-concept-best-practices.md) och Använd dem som en del av din vanliga redigerings cykel.

## <a name="next-steps"></a>Nästa steg
Se [Lägg till exempel yttranden](luis-how-to-add-example-utterances.md) för information om hur du tränar en Luis-app för att förstå användar yttranden.

