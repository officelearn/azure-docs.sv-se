---
title: Konfigurera en QnA Maker-tjänsten – QnA Maker
titleSuffix: Azure Cognitive Services
description: Innan du kan skapa alla QnA Maker kunskapsbaser, måste du först ställa in QnA Maker-tjänsten i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan ställa in QnA Maker-tjänsten.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/13/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 239633950e4672693e3684345749f9c46f01c037
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605656"
---
# <a name="create-a-qna-maker-service"></a>Skapa en QnA Maker-tjänsten

Innan du kan skapa alla QnA Maker kunskapsbaser, måste du först ställa in QnA Maker-tjänsten i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan ställa in QnA Maker-tjänsten.

## <a name="create-a-new-service"></a>Skapa en ny tjänst

Den här proceduren distribuerar några Azure-resurser. Tillsammans resurserna hantera kunskapsbas-innehåll och tillhandahåller funktioner för besvara fråga om en slutpunkt.

1. Logga in på Azure-portalen och [skapa en QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) resurs.

1. Välj **skapa** när du har läst villkoren.

    ![Skapa en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. I **QnA Maker**, väljer du lämpliga nivåer och regioner.

    ![Skapa en ny tjänst för QnA Maker - prisnivå och regioner](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Fyll i **namn** med ett unikt namn som identifierar den här QnA Maker-tjänsten. Det här namnet identifierar även QnA Maker-slutpunkten som din kunskapsbaser kommer att associeras.
    * Välj den **prenumeration** i vilket QnA Maker-resursen ska distribueras.
    * Välj den **prisnivå** för QnA Maker management services (portal och API: er hantering). Se [här](https://aka.ms/qnamaker-pricing) för information om priser för SKU: er.
    * Skapa en ny **resursgrupp** (rekommenderas) eller Använd en befintlig där du kan distribuera den här QnA Maker-resursen. QnA Maker skapar flera Azure-resurser. När du skapar en resursgrupp för att lagra dessa resurser, kan du enkelt hitta, hantera och ta bort dessa resurser av resursgruppens namn.
    * Välj en **resursgruppsplats**.
    * Välj den **Search prisnivån** för Azure Search-tjänsten. Om du ser den kostnadsfria nivån nedtonat innebär det att du redan har en kostnadsfri Azure Search-nivå som distribuerats i prenumerationen. I så fall behöver du börjar med den grundläggande Azure Search-nivån. Se information om priser för Azure search [här](https://azure.microsoft.com/pricing/details/search/).
    * Välj den **sökplats** där du vill att Azure Search-data som ska distribueras. Begränsningar i där kunddata måste lagras informerar den plats som du väljer för Azure Search.
    * Namnge din App service i **appnamn**.
    * Som standard i App service som standard till standardnivån (S1). Du kan ändra planen efter har skapats. Se mer information om priser för Apptjänst [här](https://azure.microsoft.com/pricing/details/app-service/).
    * Välj den **plats för webbplats** där App Service ska distribueras.

        > [!NOTE]
        > Sökplats kan skilja sig från den plats för webbplats.

    * Välj om du vill aktivera **Application Insights** eller inte. Om **Application Insights** är aktiverad, QnA Maker samlar in telemetri på trafik, chattloggarna och fel.
    * Välj den **App insights-plats** där Application Insights-resurs ska distribueras.
    * För kostnaden besparingar mått, kan du [dela](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) vissa men inte alla Azure-resurser skapas för QnA Maker. 

1. När alla fält bekräftas kan du välja **skapa** att starta distributionen av dessa tjänster i din prenumeration. Det tar några minuter att slutföra.

1. När distributionen är klar visas i följande resurser som skapats i din prenumeration.

    ![Skapa en ny QnA Maker-tjänsten för resursen](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Region för Hanteringstjänst

Management-tjänsten för QnA Maker kan bara användas för portalen & för inledande databearbetning är endast tillgänglig i USA, västra. Inga kunddata lagras i den här tjänsten i västra USA.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och publicera en kunskapsbas](../Quickstarts/create-publish-knowledge-base.md)
