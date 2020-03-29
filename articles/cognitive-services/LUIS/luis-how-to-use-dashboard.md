---
title: Instrumentpanel - Språkförståelse - LUIS
titleSuffix: Azure Cognitive Services
description: Åtgärda avsikter och entiteter med instrumentpanelen för den tränade appen. Instrumentpanelen visar övergripande appinformation med högdagrar av avsikter som ska åtgärdas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888206"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Så här använder du instrumentpanelen för att förbättra appen

Hitta och åtgärda problem med den tränade appens avsikter när du använder exempelyttranden. Instrumentpanelen visar övergripande appinformation med högdagrar av avsikter som ska åtgärdas. 

Granska Dashboard analys är en iterativ process, upprepa när du ändrar och förbättra din modell.

Den här sidan har inte relevant analys för appar som inte har några exempelyttranden i avsikter, så kallade _mönsterappar._ 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Vilka problem kan åtgärdas från instrumentpanelen?

De tre problem som tas upp i instrumentpanelen är:

|Problem|Diagramfärg|Förklaring|
|--|--|--|
|Obalans i data|-|Detta inträffar när antalet exempelytningar varierar avsevärt. Alla avsikter måste ha _ungefär_ samma antal exempel yttranden - utom ingen avsikt. Den bör bara ha 10%-15% av den totala mängden yttranden i appen.<br><br> Om data är obalanserade men avsiktsnoggrannheten ligger över ett visst tröskelvärde rapporteras inte denna obalans som ett problem.<br><br>**Börja med det här problemet - det kan vara orsaken till de andra problemen.**|
|Oklara förutsägelser|Orange|Detta inträffar när den översta avsikten och nästa avsiktspoäng är tillräckligt nära för att de ska kunna vändas på nästa träning, på grund av [negativ sampling](luis-how-to-train.md#train-with-all-data) eller fler exempelyttranden som lagts till i avsikt. |
|Felaktiga förutsägelser|Röd|Detta inträffar när ett exempel yttrande inte förutspås för den märkta avsikten (avsikten den är i).|

Korrekta förutsägelser representeras med färgen blå.

Instrumentpanelen visar dessa problem och talar om vilka avsikter som påverkas och föreslår vad du ska göra för att förbättra appen. 

## <a name="before-app-is-trained"></a>Innan appen tränas 

Innan du tränar appen innehåller instrumentpanelen inga förslag på korrigeringar. Träna din app för att se dessa förslag.  

## <a name="check-your-publishing-status"></a>Kontrollera publiceringsstatus

**Statuskortet Publicering** innehåller information om den aktiva versionens senaste publicering. 

Kontrollera att den aktiva versionen är den version som du vill åtgärda. 

![Instrumentpanelen visar appens externa tjänster, publicerade regioner och aggregerade slutpunktsträffar.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Detta visar även alla externa tjänster, publicerade regioner och aggregerade slutpunktsträffar. 

## <a name="review-training-evaluation"></a>Granska utvärdering av utbildning

**Utvärderingskortet för utbildning** innehåller den aggregerade sammanfattningen av appens övergripande noggrannhet efter område. Poängen anger avsiktskvalitet. 

![Utvärderingskortet för utbildning innehåller det första informationsområdet om appens övergripande noggrannhet.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Diagrammet visar korrekt förutsedda avsikter och problemområden med olika färger. När du förbättrar appen med förslagen ökar den här poängen. 

De föreslagna korrigeringarna avgränsas efter problemtyp och är de viktigaste för din app. Om du föredrar att granska och åtgärda problem per avsikt använder du kortet **[Avsikter med fel](#intents-with-errors)** längst ned på sidan. 

Varje problemområde har avsikter som måste åtgärdas. När du väljer avsiktsnamnet öppnas sidan **Avsikt** med ett filter som tillämpas på yttrandena. Med det här filtret kan du fokusera på de yttranden som orsakar problemet.

### <a name="compare-changes-across-versions"></a>Jämföra ändringar mellan versioner

Skapa en ny version innan du gör ändringar i appen. I den nya versionen gör du de föreslagna ändringarna i avsiktens exempelyttranden och tränar sedan igen. På utvärderingskortet **För** instrumentpanelssidans utvärderingskort för utbildning använder du visa ändring från **tränad version** för att jämföra ändringarna. 

![Jämföra ändringar mellan versioner](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Åtgärda version genom att lägga till eller redigera exempelyttranden och omskolning

Den primära metoden för att åtgärda din app är att lägga till eller redigera exempelyttranden och omskola. De nya eller ändrade yttrandena måste följa riktlinjer för [olika yttranden](luis-concept-utterance.md).

Lägga till exempel yttranden bör göras av någon som:

* har en hög grad av förståelse för vilka yttranden som finns i de olika syftena.
* vet hur yttranden i en avsikt kan förväxlas med en annan avsikt.
* kan avgöra om två avsikter, som ofta förväxlas med varandra, bör kollapsas till en enda avsikt. Om så är fallet måste de olika uppgifterna hämtas med entiteter.

### <a name="patterns-and-phrase-lists"></a>Mönster och fraslistor

Analyssidan anger inte när [mönster](luis-concept-patterns.md) eller [fraslistor ska](luis-concept-feature.md)användas. Om du lägger till dem kan det hjälpa till med felaktiga eller otydliga förutsägelser, men det hjälper inte med dataobalans. 

### <a name="review-data-imbalance"></a>Granska obalans i data

Börja med det här problemet - det kan vara orsaken till de andra problemen.

Avsiktslistan **för dataobalans** visar avsikter som behöver fler yttranden för att korrigera dataobalansen. 

**Så här löser du problemet:**

* Lägg till fler yttranden till avsikten och träna sedan igen. 

Lägg inte till yttranden till ingen avsikten om det inte föreslås på instrumentpanelen.

> [!Tip]
> Använd det tredje avsnittet på sidan, **Yttranden per avsikt** med inställningen **Yttranden (tal),** som en snabb visuell guide som avsikter behöver fler uttryck.  
    ![Använd "Yttranden (tal)" för att hitta avsikter med dataobalans.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Granska felaktiga förutsägelser

Listan **felaktig förutsägelseavsikt** visar avsikter som har yttranden, som används som exempel för en viss avsikt, men som förutses för olika avsikter. 

**Så här löser du problemet:**

* Redigera yttranden för att vara mer specifika för avsikten och träna igen.
* Kombinera avsikter om yttranden är för nära anpassade och träna igen.

### <a name="review-unclear-predictions"></a>Granska oklara förutsägelser

Listan **över oklara** avsiktsförsiktningspunkter visar avsikter med yttranden med förutsägelsepoäng som inte är tillräckligt långt från deras närmaste rival, att den högsta avsikten för uttryck kan ändras på nästa träning, på grund av [negativ sampling](luis-how-to-train.md#train-with-all-data).

**Så här åtgärdar du problemet**;

* Redigera yttranden för att vara mer specifika för avsikten och träna igen.
* Kombinera avsikter om yttranden är för nära anpassade och träna igen.

## <a name="utterances-per-intent"></a>Yttranden per avsikt

Det här kortet visar den övergripande appens hälsa i alla avsikter. När du åtgärdar avsikter och omskolar, fortsätt att titta på det här kortet för problem.

Följande diagram visar en välbalanserad app med nästan inga problem att åtgärda.

![Följande diagram visar en välbalanserad app med nästan inga problem att åtgärda.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Följande diagram visar en dåligt balanserad app med många problem att åtgärda.

![Följande diagram visar en välbalanserad app med nästan inga problem att åtgärda.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Hovra över varje avsiktsfält för att få information om avsikten. 

![Följande diagram visar en välbalanserad app med nästan inga problem att åtgärda.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Använd funktionen **Sortera efter** för att ordna avsikter efter ärendetyp så att du kan fokusera på de mest problematiska avsikter med det problemet. 

## <a name="intents-with-errors"></a>Avsikter med fel

Med det här kortet kan du granska problem för en viss avsikt. Standardvyn för det här kortet är de mest problematiska avsikter så att du vet var du ska fokusera dina ansträngningar.

![Med kortet Avsikter med fel kan du granska problem för en viss avsikt. Kortet filtreras som standard till de mest problematiska avsikter, så att du vet var du ska fokusera dina ansträngningar.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Det översta donutdiagrammet visar problemen med avsikten mellan de tre problemtyperna. Om det finns problem i de tre problemtyperna har varje typ ett eget diagram nedan, tillsammans med eventuella rivaliserande avsikter. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrera avsikter efter utleverans och procent

I det här avsnittet av kortet kan du hitta exempelyttranden som faller utanför felgränsen. Helst vill du att korrekta förutsägelser ska vara betydande. Den procentsatsen är affärs- och kunddriven. 

Bestäm tröskelprocent som du är bekväm med för ditt företag. 

Med filtret kan du hitta avsikter med specifika problem:

|Filter|Föreslagen procentsats|Syfte|
|--|--|--|
|Mest problematiska avsikter|-|**Börja här** - Om du åtgärdar yttranden i den här avsikten förbättras appen mer än andra korrigeringar.|
|Korrigera förutsägelser nedan|60 %|Det här är procentandelen yttranden i den valda avsikten som är korrekta men har en konfidenspoäng under tröskelvärdet. |
|Oklara prognoser ovan|15 %|Det här är procentandelen yttranden i den valda avsikten som förväxlas med den närmaste rivaliserande avsikten.|
|Felaktiga förutsägelser ovan|15 %|Det här är procentandelen yttranden i den valda avsikten som är felaktigt förutsedda. |

### <a name="correct-prediction-threshold"></a>Korrigera tröskelvärde för förutsägelse

Vad är en säker förutsägelse förtroende poäng till dig? I början av apputveckling kan 60% vara ditt mål. Använd **rätt förutsägelser nedan** med procentandelen 60 % för att hitta eventuella yttranden i den valda avsikten som måste åtgärdas.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Oklart eller felaktigt tröskelvärde för förutsägelse

Med de här två filtren kan du hitta yttranden i den valda avsikten utanför tröskelvärdet. Du kan tänka på dessa två procentsatser som felprocent. Om du är bekväm med en felfrekvens på 10–15 % för förutsägelser anger du filtertröskeln till 15 % för att hitta alla yttranden över det här värdet. 

## <a name="next-steps"></a>Nästa steg

* [Hantera dina Azure-resurser](luis-how-to-azure-subscription.md)
