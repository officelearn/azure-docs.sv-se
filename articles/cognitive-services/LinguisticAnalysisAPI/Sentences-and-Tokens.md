---
title: 'Meningar med token i API: et för språkliga analys | Microsoft Docs'
description: Läs mer om meningen separation och tokenisering i språkliga analys API i kognitiva Services.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: davl
ms.openlocfilehash: 4681098a0e56640e95463272be44f7432be26839
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351642"
---
# <a name="sentence-separation-and-tokenization"></a>Meningen Separation och Tokenisering

## <a name="background-and-motivation"></a>Bakgrund och syfte

Det första steget i språkliga analys är ges en brödtext att dela i meningar och token.

### <a name="sentence-separation"></a>Meningen uppdelning

På första titt verkar att dela upp text i meningar är enkel: bara söka efter slutet av meningar markörer och dela det meningar.
Men är dessa ofta komplicerade och tvetydig.

Beakta följande exempel:

> Vad sa du?!? Jag fick inget om den director ”nytt förslag”. Det är viktigt att Herr och fru Smith.

Den här texten innehåller tre meningar:

- Vad sa du?!?
- Jag fick inget om den director ”nytt förslag”.
- Det är viktigt att Herr och fru Smith.

Observera hur avslutar meningar markeras på mycket olika sätt.
Först slutar i en kombination av frågetecken och utropstecken (kallas ibland för en interrobang).
Andra avslutas med en punkt eller fullständig stoppa, men följande citattecken ska användas i föregående mening.
Du kan se hur samma period tecknet kan användas för att markera förkortningar samt i den tredje meningen.
Bara granskar skiljetecken ger en bra kandidat uppsättning, men ytterligare arbete krävs för att identifiera true meningen gränser.

### <a name="tokenization"></a>Tokenisering

Nästa uppgift är att dela dessa meningar i token.
Engelska token är oftast, avgränsade med blanksteg.
(Hitta token eller ord är mycket enklare på engelska än i kinesiska, där används främst inte blanksteg mellan ord.
Första meningen kan skrivas som ”Whatdidyousay”?)

Det finns några svårt fall.
Först skiljetecken ofta (men inte alltid) ska delas upp från den omgivande kontext.
Andra engelska har *contractions*, t.ex. ”inte” eller ”det är”, där ord har komprimeras och förkortas i mindre delar. Syftet med tokenizer är att dela teckensekvensen i ord.

Nu ska vi gå tillbaka till exempel meningar från ovan.
Nu har vi placerat ”mittpunkt” (&middot;) mellan varje distinkta token.

- Vad &middot; har &middot; du &middot; säger &middot; ?!?
- Jag &middot; har &middot; nte &middot; höra &middot; om &middot; den &middot; katal &middot; 's &middot; ” &middot; nya &middot; förslag &middot; . &middot; "
- Den &middot; 's &middot; viktiga &middot; till &middot; Herr &middot; och &middot; FRU. &middot; Smith &middot; .

Observera hur de flesta token är ord som du hittar i ordlistan (t.ex. *viktiga*, *katal*).
Andra bestå enbart av skiljetecken.
Slutligen finns mer ovanliga token som representerar förkortningar som *nte* för *inte*, t.ex. genitiv *'s*osv. Den här tokenisering gör att vi kan hantera ordet *inte* och frasen *uppfyllde inte* på ett mer konsekvent sätt, till exempel.

## <a name="specification"></a>Specifikationen

Det är viktigt att fatta konsekvent beslut om vad omfattar en mening och en token.
Vi är beroende av specifikationen från den [Sandberg Treebank](https://www.cis.upenn.edu/~treebank/) (vissa ytterligare information finns här: [https://www.cis.upenn.edu/~treebank/tokenization.html]).
