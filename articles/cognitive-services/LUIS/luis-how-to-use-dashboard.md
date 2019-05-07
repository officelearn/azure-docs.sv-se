---
title: Instrumentpanel – Språkförståelse
titleSuffix: Azure Cognitive Services
description: Åtgärda avsikter med analytics sammanfattande instrumentpanel, en visualiserade Rapporteringsverktyg.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a518a697369ff74689a0c4ac05af96453b6a5ca4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072472"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Hur du använder instrumentpanelen för att förbättra din app

Hitta och åtgärda problem med din tränade appavsikter när du använder exemplet yttranden. Instrumentpanel för nätverkssammanfattning visar övergripande appinformation med höjdpunkter i avsikter som ska åtgärdas. 

Granska instrumentpanelen analysen är en iterativ process, upprepas när du ändrar och förbättra din modell.

Den här sidan kommer inte att ha relevanta analys för appar som inte har några exempel yttranden i avsikter kallas _endast mönstret_ appar. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Vilka problem kan åtgärdas från instrumentpanelen?

De tre problem som åtgärdas i instrumentpanelen är:

|Problem|Diagramfärg|Förklaring|
|--|--|--|
|Data obalans|-|Detta inträffar när antalet exempel yttranden kan variera avsevärt. Alla avsikter måste ha _ungefär_ samma antal exempel yttranden - utom avsikt None. Det bör bara ha 10 – 15% av det totala antalet uttryck i appen.<br><br> Om data är imbalanced men avsikt noggrannheten är över visst tröskelvärde rapporteras inte detta som ett problem.<br><br>**Börja med det här problemet – det kan vara orsaken till de andra problem.**|
|Oklart förutsägelser|Orange|Detta inträffar när främsta syftet och nästa avsikten poäng ligger nära nog att de kan Vänd på nästa-utbildning på grund av [negativt sampling](luis-how-to-train.md#train-with-all-data) eller flera exempel yttranden som lagts till avsikt. |
|Felaktig förutsägelser|Röd|Detta inträffar när en exempel-uttryck inte förväntas för den märkta avsikten (intent som den tillhör).|

Korrekta förutsägelser visas med blå färg.

Instrumentpanel för nätverkssammanfattning visar de här problemen och talar om vilka avsikter som påverkas och ger förslag på vad du bör göra för att förbättra appen. 

## <a name="before-app-is-trained"></a>Innan app har tränats 

Innan du tränar appen innehåller instrumentpanel för nätverkssammanfattning inte några förslag för korrigeringar. Träna din app för att se dessa förslag.  

## <a name="check-your-publishing-status"></a>Kontrollera din Publiceringsstatus

Den **Publiceringsstatus** kort innehåller information om aktivt version användarens senaste publiceringen. 

Kontrollera att den aktiva versionen är den version du vill åtgärda. 

![Instrumentpanel för nätverkssammanfattning visar appens externa tjänster, publicerat regioner och aggregeras endpoint träffar.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Detta också visar eventuella externa tjänster, publicerade regioner och aggregeras endpoint träffar. 

## <a name="review-training-evaluation"></a>Granska utbildning utvärdering

Den **utbildning utvärdering** kort innehåller sammanställda sammanfattningen av appens övergripande Precision per område. Poängen anger avsikt kvalitet. 

![Utbildning utvärdering kort innehåller det första området för information om din app övergripande precision.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Diagrammet visar korrekt förväntade avsikter och problemområden med olika färger. Som du förbättrar appen med förslag, detta poäng ökar. 

Föreslagna korrigeringar avgränsas med problemtyp och är de viktigaste för din app. Om du föredrar att granska och åtgärda problem per avsikt, Använd den **[avsikter med fel](#intents-with-errors)** kortet längst ned på sidan. 

Varje problemområden har avsikter som behöver åtgärdas. När du väljer namnet på avsikt, den **avsikt** öppnas med ett filter som tillämpas på talade. Det här filtret kan du fokusera på yttranden som orsakar problemet.

### <a name="compare-changes-across-versions"></a>Jämföra ändringar mellan versioner

Skapa en ny version innan du gör ändringar i appen. I den nya versionen, göra de föreslagna ändringarna i det syftet exempel yttranden och sedan träna igen. På sidan instrumentpanel **utbildning utvärdering** kortet, Använd den **Show ändring från tränade version** att jämföra ändringarna. 

![Jämföra ändringar mellan versioner](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Åtgärda version genom att lägga till eller redigera exempel yttranden och träna

Det bästa sättet att åtgärda din app kommer att lägga till eller redigera exempel yttranden och träna om. Nya eller ändrade yttranden måste du följa riktlinjerna för [olika yttranden](luis-concept-utterance.md).

Att lägga till exempel yttranden bör utföras av någon som:

* har en hög grad av förståelse för vilka yttranden som finns i olika avsikter
* vet hur yttranden i en avsikt kan blandas ihop med en annan avsikt
* kan fastställa om två avsikter som ofta förväxlas med varandra, ska vara dolda i en enda avsikten och andra data som hämtats med entiteter

### <a name="patterns-and-phrase-lists"></a>Mönster och frasen listor

Analyssidan anger inte när du ska använda [mönster](luis-concept-patterns.md) eller [fras listor](luis-concept-feature.md). Om du lägger till dem, kan hjälpa dig med felaktig eller oklart förutsägelser men med data obalans hjälp inte. 

### <a name="review-data-imbalance"></a>Granska data obalans

Börja med det här problemet – det kan vara orsaken till de andra problem.

Den **data obalans** avsikt i listan visas avsikter som behöver mer yttranden för att korrigera data obalans. 

**Lös problemet**:

* Lägg till mer yttranden med intentionen och sedan träna igen. 

Lägg inte till yttranden avsikt NONE, såvida inte som föreslås på instrumentpanel för nätverkssammanfattning.

> [!Tip]
> Använd det tredje avsnittet på sidan **yttranden per avsikt** med den **yttranden (nummer)** anger som en visual snabbguide som avsikter behöver mer yttranden.  
    ![Använd ”yttranden (tal)” för att hitta avsikter med data obalans.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Granska felaktig förutsägelser

Den **felaktig förutsägelse** avsikt i listan visas avsikter som har yttranden som används som exempel för en specifik avsikt, men förutspås för olika avsikter. 

**Lös problemet**:

* Redigera uttryck för att vara mer specifikt för avsikt och träna igen.
* Kombinera avsikter om yttranden justeras alltför mycket och träna igen.

### <a name="review-unclear-predictions"></a>Granska oklart förutsägelser

Den **oklart förutsägelse** avsikt i listan visas avsikter med yttranden med förutsägelse poäng som inte är tillräckligt långt sätt från sin närmaste rival som den översta uttryck som används kan ändras vid nästa utbildning på grund av [ negativt sampling](luis-how-to-train.md#train-with-all-data).

**Lös problemet**;

* Redigera uttryck för att vara mer specifikt för avsikt och träna igen.
* Kombinera avsikter om yttranden justeras alltför mycket och träna igen.

## <a name="utterances-per-intent"></a>Yttranden per avsikt

Det här kortet visar den övergripande hälsan för appen i avsikter. Fortsätt alternativen i det här kortet för problem Eftersom du åtgärda avsikter och omtrimning.

Följande diagram visar en väl avvägd app nästan utan problem att åtgärda.

![Följande diagram visar en väl avvägd app nästan utan problem att åtgärda.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Följande diagram visar en felaktigt balanserade app med många problem att åtgärda.

![Följande diagram visar en väl avvägd app nästan utan problem att åtgärda.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Hovra över fältet för varje avsikt att få information om avsikten. 

![Följande diagram visar en väl avvägd app nästan utan problem att åtgärda.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Använd den **sortera efter** funktionen för att ordna avsikter av typ av problem så att du kan fokusera på de mest problematiska avsikter med problemet. 

## <a name="intents-with-errors"></a>Avsikter med fel

Det här kortet kan du granska problem för en specifik avsikt. Standardvyn för det här kortet är de mest problematiska avsikter så att du vet var du vill fokusera.

![Avsikter med fel kort kan du granska problem för en specifik avsikt. Kortet är filtrerats till de mest problematiska avsikter som standard så att du vet var du vill fokusera.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Det främsta ringdiagrammet visar problem med avsikten i tre problemtyper. Om det finns problem i tre problemtyper visas har varje typ sin egen diagrammet nedan, och eventuella rival avsikter. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrera avsikter av problemet och procent

Det här avsnittet av kortet kan du söka efter exempel yttranden som som faller utanför ditt tröskelvärde för fel. Vi rekommenderar att du vill korrekta förutsägelser vara betydande. När är verksamhets- och drivs i procent. 

Fastställa tröskelvärden som du är nöjd med för din verksamhet. 

Filtret kan du söka efter avsikter med specifika problem:

|Filter|Föreslagna procent|Syfte|
|--|--|--|
|De mest problematiska avsikter|-|**Börja här** -åtgärda talade i den här förbättrar appen som är mer än andra korrigeringar.|
|Korrekta förutsägelser nedan|60 %|Detta är i procent av yttranden i den valda metoden som är rätt men har ett förtroenderesultat under tröskeln. |
|Oklart förutsägelser ovan|15 %|Detta är i procent av yttranden i den valda metoden som förväxlas med närmaste rival avsikten.|
|Felaktig förutsägelser ovan|15 %|Detta är i procent av yttranden i den valda metoden som förutspås felaktigt. |

### <a name="correct-prediction-threshold"></a>Tröskelvärde för rätt förutsägelse

Vad är en säker på förutsägelse förtroendepoäng för dig? I början av apputveckling kanske 60% målet. Använd den **korrigera förutsägelser nedan** med procentandelen 60% för att hitta yttranden i den valda metoden som behöver åtgärdas.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Tröskelvärde för oklart eller felaktig förutsägelse

Dessa två filter kan du hitta yttranden i den valda metoden din överskrids. Du kan tänka på de här två procenttal som procentandel fel. Om du är nöjd med en 10 – 15% Felfrekvens för förutsägelser att ange filter tröskelvärde för 15% för att hitta alla yttranden ovanför det här värdet. 

## <a name="next-steps"></a>Nästa steg

* [Hantera Azure-resurser](luis-how-to-azure-subscription.md)
