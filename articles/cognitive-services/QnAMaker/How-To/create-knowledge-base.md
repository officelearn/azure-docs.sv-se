---
title: Skapa kunskapsbas
titleSuffix: QnA Maker API - Azure Cognitive Services
description: Använd QnA Maker API-tjänstportalen måste du lägger till kan du skapa en kunskapsbas med chit-chatt. Detta gör att din app engagerande. Lägg till en i förväg uppsättning övre chit-chatt i din Kunskapsbas som utgångspunkt för din robot chit-chatt och spara tid och pengar på att skriva dem från grunden.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: b4553a392795bb8578f24848ccacc870b654bce9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116240"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Snabbstart: Skapa en kunskapsbas med QnA Maker API service-portalen

QnA Maker API service-portalen gör det enkelt att lägga till befintliga datakällor när du skapar en kunskapsbas. Du kan skapa en ny QnA Maker-kunskapsbas från följande dokumenttyper:

<!-- added for scanability -->
* Vanliga frågor och svar-sidor
* Produkter handböcker
* Strukturerade dokument

Inkludera en chit-chatt personlighet om du vill göra dina kunskaper mer engagerande med dina användare.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="create-a-new-knowledge-base"></a>Skapa en ny kunskapsbas

1. Logga in på att den [QnA Maker portal](https://qnamaker.ai) med dina autentiseringsuppgifter för Azure och välja **skapa en kunskapsbas**.

1. Om du inte redan har skapat en QnA Maker-tjänsten väljer **skapar du en tjänst för frågor och svar om**. 

1. Välj din Azure-klient, namn på Azure-prenumeration och Azure-resursnamn som är associerade med QnA Maker-tjänsten från listor i **steg 2** i QnA Maker-portalen. Välj Azure QnA Maker-tjänsten som är värd för Knowledge Base.

    ![Konfigurera frågor och svar om tjänsten](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Ange namnet på din kunskapsbas och datakällorna för nya kunskapsbasen.

    ![Set-datakällor](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Ge din tjänst en **namn.** Dubblettnamn och specialtecken stöds.
    - Lägg till URL: er för data som du vill extrahera. Se mer information om vilka typer av datakällor som stöds [här](../Concepts/data-sources-supported.md).
    - Ladda upp filer för data som du vill extrahera. Se den [prisinformation](https://aka.ms/qnamaker-pricing) att se hur många dokument som du kan lägga till.
    - Om du vill lägga till kunskapsbaser manuellt kan du hoppa över **steg 4** visas i föregående bild.

1. Lägg till **Chit-chatt** till din KB. Välja att lägga till chit chat-supporten för din robot genom att välja någon av de 3 personligheter. 

    ![Lägg till chit-chatt i KB](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Välj **skapa din Kunskapsbas**.

    ![Skapa KB](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Det tar några minuter innan data ska extraheras.

    ![Extrahering](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. När din kunskapsbas har skapats, omdirigeras du till den **kunskapsbas** sidan.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med kunskapsbasen kan du ta bort den i QnA Maker-portalen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till personliga chit-chatt](./chit-chat-knowledge-base.md)
