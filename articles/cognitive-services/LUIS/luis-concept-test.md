---
title: Testa appen THOMAS - Azure | Microsoft Docs
description: Använda språk förstå (THOMAS) kontinuerligt arbetar med ditt program att modifiera den och förbättra sin kunskap för språk.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 8c702d2adbadd2736eed05c7580e8aabf69affbf
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266336"
---
# <a name="testing-in-luis"></a>Testa i THOMAS

Testning är en process för att tillhandahålla exempel utterances THOMAS och får ett svar av THOMAS identifieras avsikter och enheter. 

Du kan [testa](interactive-test.md) THOMAS interaktivt, en utterance samtidigt, eller ange en [batch](luis-concept-batch-test.md) av utterances. Testar kan du jämföra aktuellt [active](luis-concept-version.md#active-version) modellen till publicerade modellen. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>
## <a name="what-is-a-score-in-testing"></a>Vad är ett resultat i tester?
Se [förutsägelse poäng](luis-concept-prediction-score.md) begrepp för mer information om förutsägelse resultat.

## <a name="interactive-testing"></a>Interaktiva testning
Interaktiva testning från den **Test** panelen på webbplatsen. Du kan ange en utterance om du vill se hur avsikter och enheter identifieras och bedömas. Om THOMAS inte förutsäga avsikter och enheter som du förväntar dig på en utterance i fönstret tester, kopierar du den till den **avsikt** sida som en ny utterance. Sedan etikett delar av den utterance och träna THOMAS. 

## <a name="batch-testing"></a>Testa batch
Se [batch testning](luis-concept-batch-test.md) om du testar mer än en utterance i taget.

## <a name="endpoint-testing"></a>Testning av slutpunkten
Du kan testa med hjälp av den [endpoint](luis-glossary.md#endpoint) med högst två versioner av appen. Med din primära eller live version av appen som den **produktion** slutpunkt, lägga till en andra version till den **mellanlagring** slutpunkt. Den här metoden ger tre versioner av en utterance: den aktuella modellen i rutan för den [THOMAS] [ LUIS] webbplats och de två versionerna på två olika slutpunkter. 

Alla endpoint tester antal mot din kvot. 

## <a name="do-not-log-tests"></a>Logga inte tester
Om du testar mot en slutpunkt och inte vill att utterance loggas, Kom ihåg att använda den `logging=false` frågan sträng konfiguration.

## <a name="where-to-find-utterances"></a>Var du hittar utterances
THOMAS lagrar alla loggade utterances i loggfilen för hämtas den [THOMAS] [ LUIS] webbplats **appar** sidan, samt THOMAS [redigering API: er ](https://aka.ms/luis-authoring-apis). 

Alla utterances THOMAS är osäker på visas i den **[granska endpoint utterances](label-suggested-utterances.md)** sida av den [THOMAS] [ LUIS] webbplats. 

![Granska endpoint utterances](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Kom ihåg att träna
Kom ihåg att [träna](luis-how-to-train.md) THOMAS när du gör ändringar i modellen. Ändringar i appen THOMAS visas inte i tester tills appen tränats. 

## <a name="best-practices"></a>Bästa praxis
Läs [metodtips](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [testning](interactive-test.md) din utterances.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions