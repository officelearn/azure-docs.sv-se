---
title: Skapa en kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: Att lägga till chit-chatt i din robot gör det mer konversationsanpassade och engagerande. QnA Maker kan du enkelt lägga till en i förväg uppsättning övre chit-chatt i din Kunskapsbas. Detta kan vara en startpunkt för din robot chit-chatt och spara tid och pengar på att skriva dem från grunden.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 66705a0afdcdb04fe49bea0bfc03286df3611071
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543366"
---
# <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

QnA Maker gör det mycket enkelt att integrera din befintliga datakällor för att skapa en kunskapsbas. Du kan skapa en ny QnA Maker-kunskapsbas från vanliga frågor och svar-sidor, produkter handböcker, strukturerade dokument eller lägga till dem redigeringsmässigt.

## <a name="steps"></a>Steg

1. För att komma igång, logga in på att den [QnA Maker portal](https://qnamaker.ai) med dina autentiseringsuppgifter för azure och klicka på **Skapa ny tjänst**.

    ![Skapa KB ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Om du inte redan har skapat en QnA Maker-tjänsten väljer **skapar du en tjänst för frågor och svar om**. Annars väljer du en QnA Maker-tjänsten från listrutor i steg 2. Välj QnA Maker-tjänsten som är värd för Knowledge Base.

    ![Konfigurera frågor och svar om tjänsten](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Ange följande information för att skapa i knowledge base.

    ![Set-datakällor](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Ge din tjänst en **namn.** Dubblettnamn som stöds och specialtecken stöds också.
    - Klistra in URL: er som extraheras från. Se mer information om vilka typer av datakällor som stöds [här](../Concepts/data-sources-supported.md).
    - Alternativt kan överföra filer från vilket data ska extraheras. Se den [prisinformation](https://aka.ms/qnamaker-pricing
) att se hur många dokument som du kan lägga till.
    - Om du vill lägga till kunskapsbaser manuellt kan du hoppa över länka filer.

4. Välj **Skapa**.

    ![Skapa KB](../media/qnamaker-how-to-create-kb/create-kb.png)

5. Det tar några minuter innan data ska extraheras.

    ![Extrahering](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Om din kunskapsbas har skapats, kommer du att omdirigeras till den **kunskapsbas** sidan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Importera en kunskapsbas](../Tutorials/migrate-knowledge-base.md)
