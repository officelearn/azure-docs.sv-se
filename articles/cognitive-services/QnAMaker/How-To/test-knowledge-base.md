---
title: Så här testar du en kunskaps bas – QnA Maker
description: Att testa din QnA Maker kunskaps bas är en viktig del av en iterativ process för att förbättra noggrannheten i svaren som returneras. Du kan testa kunskaps basen via ett förbättrat chatt-gränssnitt som gör det möjligt att redigera.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78927265"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Testa din kunskaps bas i QnA Maker

Att testa din QnA Maker kunskaps bas är en viktig del av en iterativ process för att förbättra noggrannheten i svaren som returneras. Du kan testa kunskaps basen via ett förbättrat chatt-gränssnitt som gör det möjligt att redigera.

## <a name="interactively-test-in-qna-maker-portal"></a>Testa interaktivt i QnA Maker Portal

1. Gå till kunskaps basen genom att välja namnet på sidan **Mina kunskaps baser** .
1. Välj **testa** i programmets övre panel för att komma åt test-bildspel-panelen.
1. Ange en fråga i text rutan och välj RETUR.
1. Det bäst matchade svaret från kunskaps basen returneras som svar.

### <a name="clear-test-panel"></a>Rensa test panelen

Om du vill rensa alla angivna test frågor och resultat från test konsolen väljer du **börja** i det övre vänstra hörnet på test panelen.

### <a name="close-test-panel"></a>Stäng test panelen

Välj knappen **testa** igen för att stänga test panelen. Medan test panelen är öppen kan du inte redigera innehållet i kunskaps basen.

### <a name="inspect-score"></a>Granska Poäng

Du har granskat informationen om test resultatet i inspektions panelen.

1.  Med test-out-panelen öppen väljer du **Granska** för mer information om svaret.

    ![Inspektera svar](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Inspektions panelen visas. Panelen innehåller den översta bedömnings avsikten samt alla identifierade entiteter. Panelen visar resultatet av den valda uttryck.

### <a name="correct-the-top-scoring-answer"></a>Korrigera det främsta bedömnings svaret

Om det översta bedömnings svaret är felaktigt väljer du rätt svar i listan och väljer **Spara och träna**.

![Korrigera det främsta bedömnings svaret](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Lägg till alternativa frågor

Du kan lägga till alternativa formulär för en fråga till ett angivet svar. Skriv de alternativa svaren i text rutan och klicka på RETUR för att lägga till dem. Välj **Spara och träna** för att lagra uppdateringarna.

![Lägg till alternativa frågor](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Lägg till ett nytt svar

Du kan lägga till ett nytt svar om något av de befintliga svaren som matchades är felaktigt eller om svaret inte finns i kunskaps basen (ingen lämplig matchning hittades i KB).

Längst ned i listan med svar använder du text rutan för att ange ett nytt svar och trycker på RETUR för att lägga till den.

Välj **Spara och träna** för att spara det här svaret. Ett nytt fråge-/svar-par har nu lagts till i din kunskaps bas.

> [!NOTE]
> Alla redigeringar av kunskaps basen sparas bara när du trycker på knappen **Spara och träna** .

### <a name="test-the-published-knowledge-base"></a>Testa den publicerade kunskaps basen

Du kan testa den publicerade versionen av kunskaps basen i test fönstret. När du har publicerat KB väljer du rutan **publicerad KB** och skickar en fråga för att få resultat från den publicerade KB.

![Testa mot en publicerad KB](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Batch-test med verktyget

Använd batch-testverktyget när du vill:

* Fastställ bästa svar och Poäng för en uppsättning frågor
* verifiera förväntat svar för uppsättning av frågor

Läs [själv studie kursen](../Quickstarts/batch-testing.md) för batch-testning för steg-för-steg-anvisningar.

Batch-testning ingår i batch-testverktyget. Det här verktyget är tillgängligt som en [zippad körbar fil](https://aka.ms/qnamakerbatchtestingtool) för nedladdning eller som [C#-källkod](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting).

[Referens dokumentation om verktyget](../reference-tsv-format-batch-testing.md) inkluderar:

* kommando rads exemplet för verktyget
* formatet för TSV-och utdatafiler

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Publicera en kunskapsbas](./publish-knowledge-base.md)
