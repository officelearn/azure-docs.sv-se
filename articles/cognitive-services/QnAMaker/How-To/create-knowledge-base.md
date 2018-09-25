---
title: Skapa en kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: Att lägga till chit-chatt i din robot gör det mer konversationsanpassade och engagerande. QnA Maker kan du enkelt lägga till en i förväg uppsättning övre chit-chatt i din Kunskapsbas. Detta kan vara en startpunkt för din robot chit-chatt och spara tid och pengar på att skriva dem från grunden.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 274c2289c75f44c5a1c8dd3799612a23f46a6d67
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037702"
---
# <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

QnA Maker gör det enkelt att lägga till befintliga datakällor när du skapar en kunskapsbas. Du kan skapa en ny QnA Maker-kunskapsbas från följande dokumenttyper:

<!-- added for scanability -->
* Vanliga frågor och svar-sidor
* Produkter handböcker
* Strukturerade dokument

## <a name="steps"></a>Steg

1. Logga in på att den [QnA Maker portal](https://qnamaker.ai) med dina autentiseringsuppgifter för Azure och välja **Skapa ny tjänst**.

    ![Skapa KB ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Om du inte redan har skapat en QnA Maker-tjänsten väljer **skapar du en tjänst för frågor och svar om**. 

3. Välj din Azure-klient, namn på Azure-prenumeration och Azure-resursnamn som är associerade med QnA Maker-tjänsten från listor i **steg 2** i QnA Maker-portalen. Välj Azure QnA Maker-tjänsten som är värd för Knowledge Base.

    ![Konfigurera frågor och svar om tjänsten](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

4. Ange namnet på din kunskapsbas och datakällorna för nya kunskapsbasen.

    ![Set-datakällor](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Ge din tjänst en **namn.** Dubblettnamn och specialtecken stöds.
    - Lägg till URL: er för data som du vill extrahera. Se mer information om vilka typer av datakällor som stöds [här](../Concepts/data-sources-supported.md).
    - Ladda upp filer för data som du vill extrahera. Se den [prisinformation](https://aka.ms/qnamaker-pricing) att se hur många dokument som du kan lägga till.
    - Om du vill lägga till kunskapsbaser manuellt kan du hoppa över **steg 4** visas i föregående bild.

5. Lägg till **Chit-chatt** till din KB. Välja att lägga till chit chat-supporten för din robot genom att välja någon av de fördefinierade personligheter 3. 

    <!-- TBD: add back in when chit chat how-to is merged
    ![Add chit-chat to KB ](../media/qnamaker-how-to-chitchat/create-kb-chit-chat.png)
    -->

6. Välj **skapa din Kunskapsbas**.

    ![Skapa KB](../media/qnamaker-how-to-create-kb/create-kb.png)

7. Det tar några minuter innan data ska extraheras.

    ![Extrahering](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

8. När din kunskapsbas har skapats, omdirigeras du till den **kunskapsbas** sidan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till personliga chit-chatt](./chit-chat-knowledge-base.md)
