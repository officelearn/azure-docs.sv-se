---
title: Testa LUIS-appen – Azure | Microsoft Docs
description: Använd Språkförståelse (LUIS) för att fungera kontinuerligt för ditt program för att förfina och förbättra dess språkförståelse.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: d231eaf98358e3f8237a820e59433558d293872f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224355"
---
# <a name="testing-in-luis"></a>Testa i LUIS

Testning är processen med att tillhandahålla exempel yttranden till LUIS och hämtar ett svar av LUIS-identifieras avsikter och entiteter. 

Du kan [testa](luis-interactive-test.md) LUIS interaktivt, ett uttryck i taget, eller ange en [batch](luis-concept-batch-test.md) av yttranden. Testar kan du jämföra aktuellt [active](luis-concept-version.md#active-version) modeller till den publicerade modellen. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>
## <a name="what-is-a-score-in-testing"></a>Vad är en poäng i testning?
Se [förutsägelse poäng](luis-concept-prediction-score.md) begrepp om du vill veta mer om poäng för förutsägelse.

## <a name="interactive-testing"></a>Interaktiv testning
Interaktiv tester utförs från den **Test** panelen på webbplatsen. Du kan ange ett uttryck för att se hur avsikter och entiteter identifieras och poängsätts. Om LUIS inte är att förutsäga avsikter och entiteter som förväntat på ett uttryck i fönstret testning, kopiera den till den **avsikt** sidan som en ny uttryck. Sedan märka delar av den uttryck och träna LUIS. 

## <a name="batch-testing"></a>Batch-testning
Se [batch testning](luis-concept-batch-test.md) om du vill testa flera uttryck i taget.

## <a name="endpoint-testing"></a>Slutpunkt för testning
Du kan testa med den [endpoint](luis-glossary.md#endpoint) med högst två versioner av din app. Med din huvudsakliga eller live-version av appen som den **produktion** slutpunkt, lägga till en andra version till den **mellanlagring** slutpunkt. Den här metoden får du tre versioner av ett uttryck: den aktuella modellen i rutan av den [LUIS](luis-reference-regions.md) webbplats och de två versionerna på två olika slutpunkter. 

Alla endpoint testet räknas mot din kvot. 

## <a name="do-not-log-tests"></a>Logga inte tester
Om du testa mot en slutpunkt och inte vill att de uttryck som loggas, Kom ihåg att använda den `logging=false` konfigurera frågesträngar.

## <a name="where-to-find-utterances"></a>Var du hittar yttranden
LUIS lagrar alla loggade yttranden i loggfilen för frågor, tillgänglig för hämtning på den [LUIS](luis-reference-regions.md) webbplats **appar** listsidan, samt LUIS [redigera API: er](https://aka.ms/luis-authoring-apis). 

Yttranden LUIS är osäker på visas i den **[granska endpoint yttranden](luis-how-to-review-endoint-utt.md)** för den [LUIS](luis-reference-regions.md) webbplats. 

![Granska slutpunktsyttranden](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Kom ihåg att träna
Kom ihåg att [träna](luis-how-to-train.md) LUIS när du gör ändringar i modellen. Ändringar av LUIS-app visas inte i testet tills appen har tränats. 

## <a name="best-practices"></a>Bästa praxis
Lär dig [bästa praxis](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [testning](luis-interactive-test.md) din yttranden.