---
title: Gott exempel yttranden - LUIS
titleSuffix: Azure Cognitive Services
description: Yttranden är indata från användaren som appen behöver tolka. Samla in fraser som du tror att användarna kommer att ange. Inkludera yttranden som betyder samma sak men är konstruerade på olika sätt i ordlängd och ordplacering.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 7412677773b60a1894a6ece7251e797bfddee091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219922"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Förstå vad bra yttranden är för din LUIS-app

**Yttranden matas** in från användaren som appen behöver tolka. Om du vill träna LUIS att extrahera avsikter och entiteter från dem är det viktigt att fånga en mängd olika exempelyttranden för varje avsikt. Aktivt lärande, eller processen att fortsätta att träna på nya yttranden, är avgörande för maskininlärd intelligens som LUIS tillhandahåller.

Samla yttranden som du tror att användarna kommer att ange. Inkludera yttranden, vilket betyder samma sak men är konstruerade på en mängd olika sätt:

* Utterance längd - kort, medium och lång för din klient-ansökan
* Ord- och fraslängd 
* Ordplacering - entitet i början, mitten och slutet av yttrandet
* Grammatik 
* Pluralisering
* Avledning
* Substantiv och verb val
* Interpunktion - en bra sort med korrekt, felaktig och ingen grammatik

## <a name="how-to-choose-varied-utterances"></a>Hur man väljer olika yttranden

När du först kommer igång genom att [lägga till exempel yttranden](luis-how-to-add-example-utterances.md) till din LUIS-modell, här är några principer att tänka på.

### <a name="utterances-arent-always-well-formed"></a>Yttranden är inte alltid välformade

Det kan vara en mening, som "Boka en biljett till Paris för mig", eller ett fragment av en mening, som "Bokning" eller "Paris flygning."  Användare gör ofta stavfel. När du planerar appen bör du överväga om du använder Stavningskontroll av [Bing](luis-tutorial-bing-spellcheck.md) för att korrigera användarindata innan du skickar den till LUIS. 

Om du inte stavningskontroll användare yttranden, bör du träna LUIS på yttranden som innehåller stavfel och felstavningar.

### <a name="use-the-representative-language-of-the-user"></a>Använd användarens representativa språk

När du väljer yttranden bör du vara medveten om att det du tycker är en vanlig term eller fras kanske inte är korrekt för den typiska användaren av klientprogrammet. De kanske inte har domänerfarenhet. Var försiktig när du använder termer eller fraser som en användare bara skulle säga om de var en expert.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Välj varierad terminologi samt frasering

Du kommer att upptäcka att även om du gör ansträngningar för att skapa olika mening mönster, kommer du fortfarande upprepa några ordförråd.

Ta så här exempelyttranden:

|Exempel på yttranden|
|--|
|Hur får jag en dator?|
|Var får jag tag i en dator?|
|Jag vill skaffa en dator, hur gör jag det?|
|När kan jag få en dator?| 

Kärntermen här, "dator", är inte varierad. Använd alternativ som stationär dator, bärbar dator, arbetsstation eller bara maskin. LUIS kan på ett intelligent sätt härleda synonymer från kontexten, men när du skapar yttranden för träning är det alltid bättre att variera dem.

## <a name="example-utterances-in-each-intent"></a>Exempel på yttranden i varje avsikt

Varje avsikt måste ha exempel yttranden, minst 15. Om du har en avsikt som inte har några exempel yttranden, kommer du inte att kunna träna LUIS. Om du har en avsikt med ett eller mycket få exempel yttranden, LUIS kanske inte exakt förutsäga avsikten. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Lägga till små grupper med 15 yttranden för varje redigeringsiteration

I varje iteration av modellen ska du inte lägga till en stor mängd yttranden. Lägg till yttranden i kvantiteter om 15. [Träna,](luis-how-to-train.md) [publicera](luis-how-to-publish-app.md)och [testa](luis-interactive-test.md) igen.  

LUIS bygger effektiva modeller med yttranden som är noggrant utvalda av LUIS-modellförfattaren. Att lägga till för många yttranden är inte värdefullt eftersom det skapar förvirring.

Det är bättre att börja med några yttranden och sedan [granska slutpunktsyttranden](luis-how-to-review-endpoint-utterances.md) för korrekt avsiktsförutsägels och entitetsextrahering.

## <a name="utterance-normalization"></a>Normalisering av yttrande

Utterance normalisering är processen att ignorera effekterna av skiljetecken och diakritiska tecken under träning och förutsägelse.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Utterance normalisering för diakritiska tecken och interpunktion

Uttrycksnormalisering definieras när du skapar eller importerar appen eftersom det är en inställning i appen JSON-filen. Normaliseringsinställningarna för uttryck är inaktiverade som standard. 

Diakritiska tecken är märken eller tecken i texten, till exempel: 

```
İ ı Ş Ğ ş ğ ö ü
```

Om din app aktiverar normalisering ändras poäng i **testfönstret,** batchtester och slutpunktsfrågor för alla yttranden med hjälp av diakritiska tecken eller skiljetecken.

Aktivera uttrycksnormalisering för diakritiska tecken eller interpunktion till `settings` luis Json-appfilen i parametern.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Normalisering **av interpunktion** innebär att innan dina modeller får utbildning och innan dina slutpunktsfrågor förutses, kommer skiljetecken att tas bort från yttrandena. 

Normaliserande **diakritiska tecken ersätter** tecknen med diakritiska tecken i yttranden med vanliga tecken. Till `Je parle français` exempel: `Je parle francais`blir . 

Normalisering betyder inte att du inte kommer att se skiljetecken och diakritiska tecken i ditt exempel yttranden eller förutsägelse svar, bara att de kommer att ignoreras under utbildning och förutsägelse.


### <a name="punctuation-marks"></a>Skiljetecken

Interpunktion är en separat token i LUIS. Ett uttryck som innehåller en period i slutet kontra ett uttryck som inte innehåller en period i slutet är två separata yttranden och kan få två olika förutsägelser. 

Om interpunktion inte normaliseras ignorerar LUIS inte interpunktionstecken som standard eftersom vissa klientprogram kan placera betydelse på dessa märken. Kontrollera att dina exempelyttranden använder både interpunktion och ingen interpunktion för att båda formaten ska kunna returnera samma relativa poäng. 

Kontrollera att modellen hanterar skiljetecken antingen i exempelyttrandena (med och inte har skiljetecken) eller i de [mönster](luis-concept-patterns.md) där det är lättare att ignorera interpunktion med den speciella syntaxen:`I am applying for the {Job} position[.]`

Om interpunktion inte har någon specifik betydelse i klientprogrammet kan du [ignorera interpunktion](#utterance-normalization) genom att normalisera interpunktion. 

### <a name="ignoring-words-and-punctuation"></a>Ignorera ord och interpunktion

Om du vill ignorera specifika ord eller skiljetecken i mönster använder du ett `[]` [mönster](luis-concept-patterns.md#pattern-syntax) med ignorerasyntaxen för hakparenteser. _ignore_ 

## <a name="training-utterances"></a>Utbildning yttranden

Utbildning är i allmänhet inte deterministisk: uttryck förutsägelse kan variera något mellan versioner eller appar. Du kan ta bort icke-deterministisk utbildning genom `UseAllTrainingData` att uppdatera [API:et](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) för versionsinställningar med namn-/värdeparet för att använda alla träningsdata.

## <a name="testing-utterances"></a>Testa yttranden 

Utvecklare bör börja testa sina LUIS-program med verklig trafik genom att skicka yttranden till [url:en för förutsägelseslutpunkt.](luis-how-to-azure-subscription.md) Dessa yttranden används för att förbättra prestanda för avsikter och entiteter med [granska yttranden](luis-how-to-review-endpoint-utterances.md). Tester som skickas med testfönstret på LUIS-webbplatsen skickas inte via slutpunkten och bidrar därför inte till aktiv inlärning. 

## <a name="review-utterances"></a>Granska yttranden

När din modell har tränats, publicerats och tagit emot [slutpunktsfrågor](luis-glossary.md#endpoint) [granskar du de yttranden](luis-how-to-review-endpoint-utterances.md) som luis föreslår. LUIS väljer slutpunktsyttranden som har låga poäng för antingen avsikten eller entiteten. 

## <a name="best-practices"></a>Bästa praxis

Granska [metodtips](luis-concept-best-practices.md) och tillämpa dem som en del av din vanliga redigeringscykel.

## <a name="label-for-word-meaning"></a>Etikett för ordbestelse

Om ordet val eller ord arrangemang är samma, men betyder inte samma sak, inte märka det med entiteten. 

Följande yttranden, ordet `fair` är en homograf. Det stavas samma men har en annan innebörd:

|Yttrande|
|--|
|Vilken typ av county mässor händer i Seattle-området i sommar?|
|Är det nuvarande betyget för Seattle översyn rättvist?|

Om du vill att en händelseentitet `fair` ska hitta alla händelsedata märker du ordet i det första uttrycket, men inte i det andra.


## <a name="next-steps"></a>Nästa steg
Se [Lägga till exempelyttranden](luis-how-to-add-example-utterances.md) för information om hur du utbildar en LUIS-app för att förstå användaryttranden.

