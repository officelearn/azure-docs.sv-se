---
title: Testa luis-appen
titleSuffix: Azure Cognitive Services
description: Testning är processen att ge exempel yttranden till LUIS och få ett svar på LUIS-erkända avsikter och entiteter.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73486683"
---
# <a name="testing-example-utterances-in-luis"></a>Testa exempelyttranden i LUIS

Testning är processen att ge exempel yttranden till LUIS och få ett svar på LUIS-erkända avsikter och entiteter. 

Du kan testa LUIS interaktivt, ett uttryck i taget eller ge ett uttryck. När du testar kan du jämföra den aktuella aktiva modellens förutsägelsesvar med den publicerade modellens förutsägelsesvar. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Vad är en poäng i testning?
Se [Förutsägelsepoängbegrepp](luis-concept-prediction-score.md) om du vill veta mer om förutsägelsepoäng.

## <a name="interactive-testing"></a>Interaktiv testning
Interaktiv testning görs från **testpanelen** i LUIS-portalen. Du kan ange ett uttryck för att se hur avsikter och entiteter identifieras och poängsätts. Om LUIS inte förutsäger avsikter och entiteter som du förväntar dig på ett uttryck på testpanelen, kopiera den till **sidan Avsikt** som ett nytt uttryck. Märk sedan de delar av uttrycket för entiteter och träna LUIS. 

## <a name="batch-testing"></a>Batch-testning
Se [batchtestning](luis-concept-batch-test.md) om du testar mer än ett uttryck i taget.

## <a name="endpoint-testing"></a>Testning av slutpunkt
Du kan testa med [slutpunkten](luis-glossary.md#endpoint) med högst två versioner av appen. Med din huvudsakliga eller levande version av appen som **produktionsslutpunkt** lägger du till en andra version i **mellanlagringsslutpunkten.** Den här metoden ger dig tre versioner av ett uttryck: den aktuella modellen i testfönstret på [LUIS-webbplatsen](luis-reference-regions.md) och de två versionerna vid de två olika slutpunkterna. 

Alla slutpunktstestning räknas in i din användningskvot. 

## <a name="do-not-log-tests"></a>Logga inte tester
Om du testar mot en slutpunkt och inte vill att uttrycket ska loggas, kom ihåg att använda `logging=false` frågesträngkonfigurationen.

## <a name="where-to-find-utterances"></a>Var du hittar yttranden
LUIS lagrar alla loggade yttranden i frågeloggen, tillgängliga för nedladdning på LUIS-portalen från listsidan **appar,** samt [LUIS-redigerings-API:er](https://go.microsoft.com/fwlink/?linkid=2092087). 

Eventuella yttranden LUIS är osäker på visas på sidan **[Granska slutpunktsyttranden](luis-how-to-review-endpoint-utterances.md)** på [LUIS-webbplatsen.](luis-reference-regions.md) 

## <a name="remember-to-train"></a>Kom ihåg att träna
Kom ihåg att [träna](luis-how-to-train.md) LUIS när du har gjort ändringar i modellen. Ändringar i LUIS-appen visas inte i testningen förrän appen har tränats. 

## <a name="best-practices"></a>Bästa praxis
Läs [bästa praxis](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att testa](luis-interactive-test.md) dina yttranden.
