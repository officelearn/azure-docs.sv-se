---
title: Skapa kunskaps bas – QnA Maker
titleSuffix: Azure Cognitive Services
description: Använd API för QnA Maker service portal för att lägga till en kunskaps bas med en CHI2TEST-chatt. Detta gör din app intressant. Lägg till en i förväg ifylld uppsättning av den främsta CHI2TEST i din KB som utgångs punkt för din robots CHI2TEST och spara tid och kostnad för att skriva dem från grunden.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b2fb7496f16359f01ddbbe6db31b2d047a2ab4df
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012817"
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

1. Ge din tjänst ett **namn** , till `my first kb`exempel. Dubblettnamn och specialtecken stöds.

1. Lägg till sidan QNA Maker fel sökning som en URL `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`: och välj `+ Add URL`sedan. Se mer information om vilka typer av datakällor som stöds [här](../Concepts/data-sources-supported.md). I den här snabb starten **laddar du inte upp filer** för data som du vill extrahera. Se den [prisinformation](https://aka.ms/qnamaker-pricing) att se hur många dokument som du kan lägga till.

1. Lägg till  **_Professional_ CHI2TEST-chatt** till din KB. 

1. Välj **skapa din Kunskapsbas**.

    ![Skapa KB](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Det kan ta några minuter för data att extraheras.

    ![Extrahering](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. När din kunskaps bas har skapats omdirigeras du till sidan **kunskaps bas** .

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med kunskaps basen tar du bort den i QnA Maker-portalen.

## <a name="next-steps"></a>Nästa steg

För kostnads besparingar kan du [dela](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) några men inte alla Azure-resurser som skapats för QNA Maker.

> [!div class="nextstepaction"]
> [Lägg till personliga chit-chatt](./chit-chat-knowledge-base.md)
