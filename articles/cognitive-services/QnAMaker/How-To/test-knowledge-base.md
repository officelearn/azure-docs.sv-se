---
title: Hur man testar en kunskapsbas - QnA Maker
description: Att testa din QnA Maker-kunskapsbas är en viktig del av en iterativ process för att förbättra noggrannheten i de svar som returneras. Du kan testa kunskapsbasen genom ett förbättrat chattgränssnitt som du också kan göra ändringar.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927265"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Testa din kunskapsbas i QnA Maker

Att testa din QnA Maker-kunskapsbas är en viktig del av en iterativ process för att förbättra noggrannheten i de svar som returneras. Du kan testa kunskapsbasen genom ett förbättrat chattgränssnitt som du också kan göra ändringar.

## <a name="interactively-test-in-qna-maker-portal"></a>Testa interaktivt i QnA Maker-portalen

1. Öppna kunskapsbasen genom att välja dess namn på sidan **Mina kunskapsbaser.**
1. Om du vill komma åt panelen Testa utbild väljer du **Testa** på programmets övre panel.
1. Ange en fråga i textrutan och välj Retur.
1. Det bäst matchade svaret från kunskapsbasen returneras som svar.

### <a name="clear-test-panel"></a>Rensa testpanelen

Om du vill ta bort alla inmatade testfrågor och deras resultat från testkonsolen väljer du **Börja i** det övre vänstra hörnet på testpanelen.

### <a name="close-test-panel"></a>Stäng testpanelen

Om du vill stänga testpanelen markerar du knappen **Testa** igen. När testpanelen är öppen kan du inte redigera knowledge base-innehållet.

### <a name="inspect-score"></a>Kontrollera poäng

Du kontrollerar information om testresultatet i kontrollpanelen.

1.  När panelen Testa utbild är öppen väljer du **Kontrollera** om du vill ha mer information om svaret.

    ![Granska svar](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Inspektionspanelen visas. Panelen innehåller den högsta poängavsikten samt alla identifierade entiteter. Panelen visar resultatet av det valda uttrycket.

### <a name="correct-the-top-scoring-answer"></a>Korrigera det högsta poängsvaret

Om det högsta poängsvaret är felaktigt väljer du rätt svar i listan och väljer **Spara och träna**.

![Korrigera det högsta poängsvaret](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Lägg till alternativa frågor

Du kan lägga till alternativa formulär för en fråga i ett visst svar. Skriv de alternativa svaren i textrutan och klicka på Retur för att lägga till dem. Välj **Spara och träna** om du vill lagra uppdateringarna.

![Lägg till alternativa frågor](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Lägga till ett nytt svar

Du kan lägga till ett nytt svar om något av de befintliga svaren som matchades är felaktiga eller om svaret inte finns i kunskapsbasen (ingen bra matchning finns i KB).

Längst ned i svarslistan använder du textrutan för att ange ett nytt svar och trycker på Retur för att lägga till det.

Välj **Spara och träna** om du vill spara det här svaret. Ett nytt frågesvarspar har nu lagts till i din kunskapsbas.

> [!NOTE]
> Alla ändringar i din kunskapsbas sparas bara när du trycker på knappen **Spara och träna.**

### <a name="test-the-published-knowledge-base"></a>Testa den publicerade kunskapsbasen

Du kan testa den publicerade versionen av kunskapsbasen i testfönstret. När du har publicerat KB väljer du rutan Publicerad KB och skickar en fråga för att få resultat från den publicerade KB.Once you have published the KB, select the **Published KB** and send a query to get results from the published KB.

![Testa mot en publicerad KB](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Batchtest med verktyg

Använd batchtestverktyget när du vill:

* bestämma topp svar och poäng för en uppsättning frågor
* validera förväntat svar för uppsättning frågor

Läs [batchtestningshandledningen](../Quickstarts/batch-testing.md) för steg-för-steg-instruktioner.

Batchtestning är försedd med batchtestverktyget. Detta verktyg finns som en [zippad körbar](https://aka.ms/qnamakerbatchtestingtool) för nedladdning eller som [C # källkod](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting).

[Referensdokumentation om verktyget](../reference-tsv-format-batch-testing.md) innehåller:

* kommandoradsexemplet för verktyget
* formatet för TSV-in- och outfile-filer

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Publicera en kunskapsbas](./publish-knowledge-base.md)
