---
title: Så här testar du en kunskapsbas - QnA Maker
titlesuffix: Azure Cognitive Services
description: Testa kunskapsbasen QnA Maker är en viktig del av en iterativ process för att förbättra svaren som returneras. Du kan testa kunskapsbas via ett förbättrat chatt-gränssnitt som också kan du göra ändringar.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 12/17/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2ad8d4fa04801996364755d1e0270a6509912679
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581954"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>Testa din kunskapsbas interaktivt i QnA Maker

Testa kunskapsbasen QnA Maker är en viktig del av en iterativ process för att förbättra svaren som returneras. Du kan testa kunskapsbas via ett förbättrat chatt-gränssnitt som också kan du göra ändringar.

## <a name="test-answer-matching"></a>Testa matcha svar

1.  Få åtkomst till din kunskapsbas genom att välja dess namn på den **min kunskapsbaser** sidan.
2.  Om du vill få åtkomst till panelen Test bild ut, Välj **Test** i övre panelen för ditt program.

    ![Åtkomstpanel för testning](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Ange en fråga i textrutan och tryck RETUR.

4.  Bäst matchade svaret från kunskapsbasartikel returneras som svaret.

## <a name="clear-test-panel"></a>Rensa test panelen

Om du vill ta bort alla angivna test-frågor och resultatet av test-konsolen, Välj **börja om från början** i det övre vänstra hörnet av panelen Test.

## <a name="close-test-panel"></a>Stäng test panelen

Om du vill stänga panelen Test, Välj den **Test** igen. Panelen Test är öppen, kan du inte redigera innehållet i kunskapsbasen.

## <a name="inspect-score"></a>Granska resultatet

Du kan se mer information om testresultat i panelen Granska.

1.  Med Test bild ut panelen öppen väljer **granska** för mer information om det svaret.

    ![Granska svar](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Panelen inspektion visas. Panelen visas den översta bedömning avsikt, samt alla identifierade entiteter. På panelen visas resultatet av den valda uttryck.

## <a name="correct-the-top-scoring-answer"></a>Korrigera upp bedömning svar

Om upp bedömning svar är felaktigt, väljer du rätt svar från listan och välj **spara och träna**.

![Korrigera upp bedömning svar](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Lägga till alternativa frågor

Du kan lägga till alternativa former av en fråga till ett visst svar. Typ av alternativ som svarar på i textrutan och klicka på Ange om du vill lägga till dem. Välj **spara och träna** att lagra uppdateringarna.

![Lägga till alternativa frågor](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Lägg till ett nytt svar

Du kan lägga till ett nytt svar om någon av de befintliga svar som kunde matchas är felaktiga eller svaret inte finns i knowledge base (ingen bra matchning hittades i KB). Ange ny besvara aktuella frågan i textrutan och tryck på RETUR om du vill lägga till den. 

Välj **spara och träna** att spara det här svaret. Ett nytt par frågor svar har nu lagts till din kunskapsbas.

![Lägga till nya frågor och svar-par](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Alla ändringar till din kunskapsbas endast sparas när du trycker på den **spara och träna** knappen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Publicera en kunskapsbas](./publish-knowledge-base.md)
