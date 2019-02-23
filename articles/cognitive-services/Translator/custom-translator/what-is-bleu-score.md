---
title: Vad är en BLEU poäng? – Anpassade Translator
titleSuffix: Azure Cognitive Services
description: BLEU är ett mått på skillnaderna mellan en automatisk översättning och en eller flera mänskliga skapade referens översättningar av samma källa mening. Algoritmen BLEU jämför på varandra följande fraser för automatisk översättning med på varandra följande fraser det hittar i referens översättning och räknar antalet matchningar i ett viktat sätt.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-rada
ms.openlocfilehash: 354c26e1d7290a6296b80eabcd8e63ff304e180f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730179"
---
# <a name="what-is-a-bleu-score"></a>Vad är en BLEU poäng?

[BLEU (tvåspråkig utvärdering Understudy)](https://en.wikipedia.org/wiki/BLEU) är ett mått på skillnaderna mellan en automatisk översättning och en eller flera mänskliga skapade referens översättningar av samma källa mening.

## <a name="scoring-process"></a>Riskpoängprocessen

Algoritmen BLEU jämför på varandra följande fraser för automatisk översättning med på varandra följande fraser det hittar i referens översättning och räknar antalet matchningar i ett viktat sätt. Dessa matchningar är oberoende position. En högre grad matchning anger en högre grad av likheter med referens översättning och högre poäng. Uppfattbarheten avsevärt och grammatisk är korrekt beaktas inte.

## <a name="how-bleu-works"></a>Hur fungerar BLEU?

BLEUS styrkan är det korrelerar bra med mänsklig förfogande genom att räkna ut medelvärdet ut enskild mening förfogande fel över en test-Kristi i stället för att försök att utforma exakta mänskliga förfogande för varje mening.

En mer omfattande beskrivning av BLEU poäng är [här](https://youtu.be/-UqDljMymMg).

BLEU resultat beroende starkt bredden på din domän, testdata med utbildningen och justera data, konsekvens och hur mycket data som finns tillgängliga för att träna. Om dina modeller har tränats på en smal domän och dina utbildningsdata är konsekvent med din testdata, du kan förvänta dig en hög BLEU poäng.

>[!NOTE]
>En jämförelse mellan BLEU poäng är endast motiverade när BLEU resultat jämförs med samma uppsättning Test och samma språk par samma MT-motor. En BLEU poäng från en testmängd olika är bunden till vara olika.
