---
title: Konfigurera en QnA Maker-tjänsten – QnA Maker
titleSuffix: Azure Cognitive Services
description: Innan du kan skapa alla QnA Maker kunskapsbaser, måste du först ställa in QnA Maker-tjänsten i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan ställa in QnA Maker-tjänsten.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: f30b55eda4a02cfb3e961c0019128e4fe686cf53
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967671"
---
# <a name="create-a-qna-maker-service"></a>Skapa en QnA Maker-tjänsten

Innan du kan skapa alla QnA Maker kunskapsbaser, måste du först ställa in QnA Maker-tjänsten i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan ställa in QnA Maker-tjänsten.

## <a name="create-a-new-service"></a>Skapa en ny tjänst

Den här proceduren distribuerar några Azure-resurser. Tillsammans resurserna hantera kunskapsbas-innehåll och tillhandahåller funktioner för besvara fråga om en slutpunkt.

1. Logga in på Azure Portal och [skapa en QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) resurs.

1. Välj **skapa** när du har läst de allmänna villkoren.

    ![Skapa en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. I **QnA Maker**, väljer du lämpliga nivåer och regioner.

    ![Skapa en ny tjänst för QnA Maker - prisnivå och regioner](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Fyll i **namn** med ett unikt namn som identifierar den här QnA Maker-tjänsten. Det här namnet identifierar även QnA Maker-slutpunkten som din kunskapsbaser kommer att associeras.
    * Välj den **prenumeration** i vilket QnA Maker-resursen ska distribueras.
    * Välj **pris nivå** för QNA Maker hanterings tjänster (portal-och hanterings-API: er). Se [här](https://aka.ms/qnamaker-pricing) för information om priser för SKU: er.
    * Skapa en ny **resursgrupp** (rekommenderas) eller Använd en befintlig där du kan distribuera den här QnA Maker-resursen. QnA Maker skapar flera Azure-resurser. När du skapar en resurs grupp som innehåller dessa resurser kan du enkelt hitta, hantera och ta bort dessa resurser med resurs gruppens namn.
    * Välj en **resurs grupps plats**.
    * Välj den **Search prisnivån** för Azure Search-tjänsten. Om du ser den kostnadsfria nivån nedtonat innebär det att du redan har en kostnadsfri Azure Search-nivå som distribuerats i prenumerationen. I så fall behöver du börjar med den grundläggande Azure Search-nivån. Se information om priser för Azure search [här](https://azure.microsoft.com/pricing/details/search/).
    * Välj den **sökplats** där du vill att Azure Search-data som ska distribueras. Begränsningar i där kunddata måste lagras informerar den plats som du väljer för Azure Search.
    * Namnge din App service i **appnamn**.
    * Som standard i App service som standard till standardnivån (S1). Du kan ändra planen efter har skapats. Se mer information om priser för Apptjänst [här](https://azure.microsoft.com/pricing/details/app-service/).
    * Välj den **plats för webbplats** där App Service ska distribueras.

        > [!NOTE]
        > Sökplats kan skilja sig från den plats för webbplats.

    * Välj om du vill aktivera **Application Insights** eller inte. Om **Application Insights** är aktiverad, QnA Maker samlar in telemetri på trafik, chattloggarna och fel.
    * Välj den **App insights-plats** där Application Insights-resurs ska distribueras.
    * För kostnads besparingar kan du [dela](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) några men inte alla Azure-resurser som skapats för QNA Maker. 

1. När alla fält har verifierats kan du välja **skapa** för att starta distributionen av de här tjänsterna i din prenumeration. Det tar några minuter att slutföra.

1. När distributionen är klar visas i följande resurser som skapats i din prenumeration.

    ![Skapa en ny QnA Maker-tjänsten för resursen](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Region för hanterings tjänst

Hanterings tjänsten för QnA Maker, som endast används för Portal & för inledande data bearbetning, är endast tillgänglig i västra USA. Ingen kund information lagras i den här västra USA-tjänsten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och publicera en kunskapsbas](../Quickstarts/create-publish-knowledge-base.md)
