---
title: Så här testar du en kognitiva knowledge base - frågor och svar om Maker - tjänster | Microsoft Docs
description: Testa kunskapsbasen innan du publicerar den.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: cffb63666edab25e1b3b0739d0e0f2f828600f3a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354096"
---
# <a name="test-your-knowledge-base"></a>Testa kunskapsbasen

Testa frågor och svar om Maker kunskapsbasen är en viktig del av en återkommande process förbättrar svar returneras. Du kan testa kunskapsbasen via ett förbättrat chatt-gränssnitt som också kan du göra ändringar.

## <a name="test-answer-matching"></a>Testa matcha svar

1.  Komma åt kunskapsbasen genom att markera namnet på den **min knowledge baser** sidan.
2.  Panelen Test bild ut, markerar **Test** i övre panelen för ditt program.

    ![Åtkomst-Test](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Ange en fråga i textrutan och välj RETUR.

4.  Bäst matchade svaret från knowledge base returneras som svar.

## <a name="clear-test-panel"></a>Rensa test panelen

Om du vill rensa alla angivna test-frågor samt resultaten från test-konsolen, Välj **börja om från början** i det övre vänstra hörnet på panelen Test.

## <a name="close-test-panel"></a>Stäng test panelen

Om du vill stänga panelen Test, Välj den **Test** igen. Panelen Test är öppen, kan du inte redigera innehållet Knowledge Base.

## <a name="inspect-score"></a>Inspektera poäng

Du kan granska information om testresultat på panelen Granska.

1.  Med Test bild ut panelen Öppna Välj **granska** för mer information om svaret.

    ![Inspektera svar](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Panelen kontroll visas. Panelen innehåller upp bedömningen avsikt, samt alla identifierade enheter. På panelen visas resultatet av den valda utterance.

## <a name="correct-the-top-scoring-answer"></a>Korrigera upp bedömningen svar

Om upp bedömningen svaret är felaktigt, väljer du rätt svar i listan och välj **spara och träna**.

![Åtkomst-Test](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Lägg till alternativa frågor

Du kan lägga till alternativa former av en fråga till ett visst svar. Typ av alternativa svar i textrutan och klicka på Ange för att lägga till dem. Välj **spara och träna** för lagring av uppdateringar.

![Åtkomst-Test](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Lägga till nya svar

Du kan lägga till nya svar om någon av de befintliga svar som matchade är felaktiga eller svaret inte finns i knowledge base (bra att hitta någon matchning i KB). Ange nya besvara aktuella frågan i textrutan och tryck på RETUR om du vill lägga till den. 

Välj **spara och träna** att spara det här svaret. Ett nytt par frågor svar har nu lagts till i knowledge base.

![Åtkomst-Test](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Alla ändringar i knowledge base endast sparas när du trycker på den **spara och träna** knappen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Publicera en kunskapsbas](./publish-knowledge-base.md)
