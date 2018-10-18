---
title: Konfigurera en QnA Maker-tjänsten – QnA Maker
titleSuffix: Azure Cognitive Services
description: Innan du kan skapa alla QnA Maker kunskapsbaser, måste du först ställa in QnA Maker-tjänsten i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan ställa in QnA Maker-tjänsten.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: c4175a22cc503113f3b3ef64f03f0ae0913bd20e
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364905"
---
# <a name="create-a-qna-maker-service"></a>Skapa en QnA Maker-tjänsten

Innan du kan skapa alla QnA Maker kunskapsbaser, måste du först ställa in QnA Maker-tjänsten i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan ställa in QnA Maker-tjänsten.

Det här installationsprogrammet distribuerar några Azure-resurser. Tillsammans resurserna hantera kunskapsbas-innehåll och tillhandahåller funktioner för besvara fråga om en slutpunkt.

1. Logga in på [Azure-portalen](<https://portal.azure.com>).

2.  Klicka på **Lägg till ny resurs**, och skriver ”qna maker” i sökningen och välj resursen som QnA Maker

    ![Skapa en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/create-new-resource.png)

3.  Klicka på **skapa** när du har läst villkoren.

    ![Skapa en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

4. I **QnA Maker**, väljer du lämpliga nivåer och regioner.

    ![Skapa en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Fyll i **namn** med ett unikt namn som identifierar den här QnA Maker-tjänsten. Det här namnet identifierar även QnA Maker-slutpunkten som din kunskapsbaser kommer att associeras.
    * Välj den **prenumeration** i vilket QnA Maker-resursen ska distribueras.
    * Välj den **Management prisnivån** för QnA Maker management services (portal och API: er hantering). Se [här](https://aka.ms/qnamaker-pricing) för information om priser för SKU: er.
    * Skapa en ny **resursgrupp** (rekommenderas) eller Använd en befintlig där du kan distribuera den här QnA Maker-resursen.
    * Välj den **Search prisnivån** för Azure Search-tjänsten. Om du ser den kostnadsfria nivån nedtonat innebär det att du redan har en kostnadsfri Azure Search-nivå som distribuerats i prenumerationen. I så fall behöver du börjar med den grundläggande Azure Search-nivån. Se information om priser för Azure search [här](https://azure.microsoft.com/pricing/details/search/).
    * Välj den **sökplats** där du vill att Azure Search-data som ska distribueras. Begränsningar i där kunddata måste lagras informerar den plats som du väljer för Azure Search.
    * Namnge din App service i **appnamn**.
    * Som standard i App service som standard till standardnivån (S1). Du kan ändra planen efter har skapats. Se mer information om priser för Apptjänst [här](https://azure.microsoft.com/en-in/pricing/details/app-service/).
    * Välj den **plats för webbplats** där App Service ska distribueras.

        > [!NOTE]
        > Sökplats kan skilja sig från den plats för webbplats.

    * Välj om du vill aktivera **Application Insights** eller inte. Om **Application Insights** är aktiverad, QnA Maker samlar in telemetri på trafik, chattloggarna och fel.
    * Välj den **App insights-plats** där Application Insights-resurs ska distribueras.

5. När alla fält verifierats, kan du klicka på **skapa** att starta distributionen av dessa tjänster i din prenumeration. Det tar några minuter att slutföra.

6.  När distributionen är klar visas i följande resurser som skapats i din prenumeration.

    ![Skapa en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och publicera en kunskapsbas](../Quickstarts/create-publish-knowledge-base.md)