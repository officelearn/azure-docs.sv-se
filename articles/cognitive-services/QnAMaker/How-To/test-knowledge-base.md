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
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 6ce3fc774d23914ab150e1dc1371391fc9fc548b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034393"
---
# <a name="test-your-knowledge-base"></a>Testa din kunskapsbas

Testa kunskapsbasen QnA Maker är en viktig del av en iterativ process för att förbättra svaren som returneras. Du kan testa kunskapsbas via ett förbättrat chatt-gränssnitt som också kan du göra ändringar.

## <a name="test-answer-matching"></a>Testa matcha svar

1.  Få åtkomst till din kunskapsbas genom att välja dess namn på den **min kunskapsbaser** sidan.
2.  Om du vill få åtkomst till panelen Test bild ut, Välj **Test** i övre panelen för ditt program.

    ![Åtkomst-Test](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Ange en fråga i textrutan och tryck RETUR.

4.  Bäst matchade svaret från kunskapsbasartikel returneras som svaret.

## <a name="clear-test-panel"></a>Rensa test panelen

Om du vill ta bort alla angivna test-frågor och resultatet av test-konsolen, Välj **börja om från början** i det övre vänstra hörnet av panelen Test.

## <a name="close-test-panel"></a>Stäng test panelen

Om du vill stänga panelen Test, Välj den **Test** igen. Panelen Test är öppen, kan du inte redigera innehållet i kunskapsbasen.

## <a name="inspect-score"></a>Granska resultatet

Du kan se mer information om testresultat i panelen Granska.

1.  Med Test bild ut panelen öppen väljer **granska** för mer information om det svaret.

    ![Granska svar](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Panelen inspektion visas. Panelen visas den översta bedömning avsikt, samt alla identifierade entiteter. På panelen visas resultatet av den valda uttryck.

## <a name="correct-the-top-scoring-answer"></a>Korrigera upp bedömning svar

Om upp bedömning svar är felaktigt, väljer du rätt svar från listan och välj **spara och träna**.

![Åtkomst-Test](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Lägga till alternativa frågor

Du kan lägga till alternativa former av en fråga till ett visst svar. Typ av alternativ som svarar på i textrutan och klicka på Ange om du vill lägga till dem. Välj **spara och träna** att lagra uppdateringarna.

![Åtkomst-Test](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Lägg till ett nytt svar

Du kan lägga till ett nytt svar om någon av de befintliga svar som kunde matchas är felaktiga eller svaret inte finns i knowledge base (ingen bra matchning hittades i KB). Ange ny besvara aktuella frågan i textrutan och tryck på RETUR om du vill lägga till den. 

Välj **spara och träna** att spara det här svaret. Ett nytt par frågor svar har nu lagts till din kunskapsbas.

![Åtkomst-Test](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Alla ändringar till din kunskapsbas endast sparas när du trycker på den **spara och träna** knappen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Publicera en kunskapsbas](./publish-knowledge-base.md)
