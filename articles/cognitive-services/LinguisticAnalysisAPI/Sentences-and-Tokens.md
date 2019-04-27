---
title: Meningar och token - API för Lingvistisk analys
titlesuffix: Azure Cognitive Services
description: 'Läs mer om uppdelning i meningar och tokenisering i API: T för Lingvistisk analys.'
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: davl
ROBOTS: NOINDEX
ms.openlocfilehash: 435513023cf74bbc259cb922220d5f9940452d79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635474"
---
# <a name="sentence-separation-and-tokenization"></a>Uppdelning i meningar och Tokenisering

> [!IMPORTANT]
> Förhandsversionen av Språkanalys upphörde den 9 augusti 2018. Vi rekommenderar att du använder [Azure Machine Learning-textanalysmoduler](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) för textbearbetning och -analys.

## <a name="background-and-motivation"></a>Bakgrund och motivationen

En viss text, är det första steget i språklig analys att bryta ned den i meningar och token.

### <a name="sentence-separation"></a>Uppdelning i meningar

På första titt det verkar som att dela upp text i meningar är enkel: bara hitta slutet av meningar-markörer och delar det meningar.
Dessa märken är dock ofta komplicerade och tvetydigt.

Överväg följande exempel:

> Vad sa du?!? Jag fick inget svar om den director ”nytt förslag”. Det är viktigt att Herr och fru Smith.

Den här texten innehåller tre meningar:

- Vad sa du?!?
- Jag fick inget svar om den director ”nytt förslag”.
- Det är viktigt att Herr och fru Smith.

Observera hur avslutar meningar markeras på mycket olika sätt.
Först slutar med en kombination av frågetecken och utropstecken (kallas ibland en interrobang).
Andra servrar med en punkt eller punkt, men det följande citattecknet ska hämtas till den tidigare meningen.
I den tredje meningen ser du hur den samma punkt kan användas för att markera samt förkortningar.
Tittar bara på skiljetecken ger en bra, men ytterligare arbete krävs för att identifiera gränserna som SANT mening.

### <a name="tokenization"></a>Tokenisering

Nästa uppgift är att dela dessa meningar i token.
För det mesta avgränsas engelska token med blanksteg.
(Att hitta token eller ord är mycket enklare på engelska än i kinesiska, där används huvudsakligen inte blanksteg mellan ord.
Första meningen kan vara skrivna som ”Whatdidyousay”?)

Det finns några svårt fall.
Först skiljetecken ofta (men inte alltid) ska delas upp från den omgivande kontext.
Andra engelska har *förkortningar*, t.ex. ”inte” eller ”det är”, där ord har komprimeras och förkortas i mindre delar.
Målet med tokenizer är att bryta teckensekvensen in ord.

Nu ska vi gå tillbaka till exempel meningarna ovan.
Nu har vi placerat en ”center punkt” (&middot;) mellan varje distinkta token.

- Vad &middot; gjorde &middot; du &middot; säger &middot; ?!?
- Jag &middot; gjorde &middot; nte &middot; höra &middot; om &middot; den &middot; director &middot; 's &middot; ” &middot; nya &middot; förslag &middot; . &middot; "
- Den &middot; 's &middot; viktiga &middot; till &middot; Herr &middot; och &middot; FRU. &middot; Smith &middot; .

Observera hur de flesta token är ord som du hittar i ordlistan (till exempel *viktiga*, *director*).
Andra bestå enbart av skiljetecken.
Slutligen finns mer ovanliga-tokens för att representera förkortningar som *nte* för *inte*, och genitiv som *'s*.
Den här tokenisering ger oss möjlighet att hantera ordet *inte* och frasen *inte* på ett mer konsekvent sätt.

## <a name="specification"></a>Specifikationen

Det är viktigt att fatta konsekvent beslut om vad består av en mening och en token.
Vi förlitar sig på specifikation från den [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42) (vissa ytterligare information finns på ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html).
