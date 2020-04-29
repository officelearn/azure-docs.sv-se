---
title: Testa din LUIS-app
titleSuffix: Azure Cognitive Services
description: Testning är en process för att tillhandahålla exempel yttranden till LUIS och få svar på LUIS-identifierade avsikter och entiteter.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 25b360f90a0920aad2ea5e68cda31a68be5d37a9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "73486683"
---
# <a name="testing-example-utterances-in-luis"></a>Testa exempel yttranden i LUIS

Testning är en process för att tillhandahålla exempel yttranden till LUIS och få svar på LUIS-identifierade avsikter och entiteter. 

Du kan testa LUIS interaktivt, en uttryck i taget, eller ange en av yttranden. Vid testning kan du jämföra den aktuella aktiva modellens förutsägelse svar på den publicerade modellens förutsägelse svar. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Vad är en poäng i testningen?
Se [förutsägelse Poäng](luis-concept-prediction-score.md) för att lära dig mer om förutsägelse resultat.

## <a name="interactive-testing"></a>Interaktiv testning
Interaktiv testning görs från **test** panelen i Luis-portalen. Du kan ange en uttryck för att se hur avsikter och entiteter identifieras och visas. Om LUIS inte förutsäger de intentor och entiteter som du förväntar dig på en uttryck på test panelen, kopierar du den till sidan **avsikt** som en ny uttryck. Märk sedan delarna i den uttryck för entiteter och träna LUIS. 

## <a name="batch-testing"></a>Batch-testning
Se [batch-testning](luis-concept-batch-test.md) om du testar mer än en uttryck i taget.

## <a name="endpoint-testing"></a>Slut punkts testning
Du kan testa att använda [slut punkten](luis-glossary.md#endpoint) med högst två versioner av appen. Med din huvud-eller Live-version av din app inställd som **produktions** slut punkt lägger du till en andra version till **mellanlagringsplatsen** . Den här metoden ger dig tre versioner av en uttryck: den aktuella modellen i test fönstret på [Luis](luis-reference-regions.md) -webbplatsen och de två versionerna på de två olika slut punkterna. 

All slut punkts testning räknas mot din användnings kvot. 

## <a name="do-not-log-tests"></a>Logga inte tester
Kom ihåg att använda `logging=false` frågesträngen om du testar mot en slut punkt och inte vill att uttryck ska loggas.

## <a name="where-to-find-utterances"></a>Var du hittar yttranden
LUIS lagrar alla loggade yttranden i fråge loggen som är tillgängliga för hämtning på LUIS-portalen på sidan **Apps** , samt Luis [redigerings-API: er](https://go.microsoft.com/fwlink/?linkid=2092087). 

Alla yttranden-LUIS är inte säkra i listan på yttranden-sidan för **[gransknings slut punkt](luis-how-to-review-endpoint-utterances.md)** på [Luis](luis-reference-regions.md) -webbplatsen. 

## <a name="remember-to-train"></a>Kom ihåg att träna
Kom ihåg att [träna](luis-how-to-train.md) Luis när du har gjort ändringar i modellen. Ändringar i LUIS-appen visas inte i testning förrän appen har tränats. 

## <a name="best-practices"></a>Bästa praxis
Lär dig [metod tips](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att [testa](luis-interactive-test.md) din yttranden.
