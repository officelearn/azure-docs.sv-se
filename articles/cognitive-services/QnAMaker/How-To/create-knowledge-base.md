---
title: Skapa kunskaps bas – QnA Maker
titleSuffix: Azure Cognitive Services
description: Använd API för QnA Maker service portal för att lägga till en kunskaps bas med en CHI2TEST-chatt. Detta gör din app intressant. Lägg till en i förväg ifylld uppsättning av den främsta CHI2TEST i din KB som utgångs punkt för din robots CHI2TEST och spara tid och kostnad för att skriva dem från grunden.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: dfd0663c09b78539a1d6b78aa7820385b9ddbdca
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563093"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Snabbstart: Skapa en kunskaps bas med hjälp av API för QnA Maker service portal

API för QnA Maker service portal gör det enkelt att lägga till befintliga data källor när du skapar en kunskaps bas. Du kan skapa en ny QnA Maker-kunskapsbas från följande dokumenttyper:

<!-- added for scanability -->
* Vanliga frågor och svar-sidor
* Produkter handböcker
* Strukturerade dokument

Ta med en användare av en CHI2TEST för att göra din kunskap mer engagerande med dina användare.

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

1. Lägg till **Chit-chatt** till din KB. Välj att lägga till stöd för CHI2TEST för din robot genom att välja någon av dina personliga. 

    ![Lägga till en CHI2TEST-chatt i KB](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Välj **skapa din Kunskapsbas**.

    ![Skapa KB](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Det tar några minuter innan data ska extraheras.

    ![Extrahering](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. När din kunskapsbas har skapats, omdirigeras du till den **kunskapsbas** sidan.

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med kunskaps basen tar du bort den i QnA Maker-portalen.

## <a name="next-steps"></a>Nästa steg

För kostnads besparingar kan du [dela](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) några men inte alla Azure-resurser som skapats för QNA Maker.

> [!div class="nextstepaction"]
> [Lägg till personliga chit-chatt](./chit-chat-knowledge-base.md)
