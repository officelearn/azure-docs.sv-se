---
title: Hur du ställer in en frågor och svar om Maker tjänst - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Hur du konfigurerar en frågor och svar om Maker-tjänst
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce452dd686529e017b4eae4717eadb044b389409
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354069"
---
# <a name="create-a-qna-maker-service"></a>Skapa en frågor och svar om Maker-tjänst

Innan du kan skapa någon frågor och svar om Maker knowledge databas måste du först ställa in en frågor och svar om Maker tjänst i Azure. Alla med behörighet att skapa nya resurser i en prenumeration kan ställa in en frågor och svar om Maker-tjänst.

Det här installationsprogrammet distribuerar några Azure-resurser. Tillsammans resurserna hantera knowledge base-innehåll och innehåller funktioner för frågan genom att besvara trots att en slutpunkt.

1. Logga in på [Azure-portalen](<https://portal.azure.com>).

2.  Klicka på **Lägg till ny resurs**, och Skriv ”frågor och svar om maker” i sökningen och välj frågor och svar om Maker-resurs

    ![Skapa en ny frågor och svar om Maker-tjänst](../media/qnamaker-how-to-setup-service/create-new-resource.png)

3.  Klicka på **skapa** när du har läst de allmänna villkoren.

    ![Skapa en ny frågor och svar om Maker-tjänst](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

4. I **frågor och svar om Maker**, väljer du lämpliga nivåer och regioner.

    ![Skapa en ny frågor och svar om Maker-tjänst](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Fyll i **namn** med ett unikt namn för att identifiera Maker frågor och svar om tjänsten. Det här namnet identifierar också frågor och svar om Maker slutpunkten knowledge-baser ska kopplas.
    * Välj den **prenumeration** i vilka frågor och svar om Maker resursen ska distribueras.
    * Välj den **Management prisnivån** för frågor och svar om Maker management services (portal och management API: er). Se [här](https://aka.ms/qnamaker-pricing) för information om priser för de SKU: er.
    * Skapa en ny **resursgruppen** (rekommenderas) eller använda en befintlig där du vill distribuera frågor och svar om Maker resursen.
    * Välj den **Sök prisnivån** för Azure Search-tjänsten. Om du ser den kostnadsfria nivån nedtonat innebär det att du redan har en kostnadsfri Azure Search-nivå som distribuerats i din prenumeration. I så fall behöver du börja med grundläggande Azure Search-nivå. Se detaljer för Azure search-priser [här](https://azure.microsoft.com/en-us/pricing/details/search/).
    * Välj den **sökplats** där du vill Azure Search-data som ska distribueras. Begränsningar i där kundinformation måste lagras informerar den plats som du väljer för Azure Search.
    * Namnge din apptjänst i **appnamn**.
    * Som standard App service som standard standardnivån (S1). Du kan ändra schemat för när den har skapats. Se mer information om priser för Apptjänst [här](https://azure.microsoft.com/en-in/pricing/details/app-service/).
    * Välj den **webbplats plats** där tjänsten appen ska distribueras.

        > [!NOTE]
        > Platsen för sökningen kan skilja sig från platsen för webbplatsen.

    * Välj om du vill aktivera **Programinsikter** eller inte. Om **Programinsikter** är aktiverad, frågor och svar om Maker samlar in telemetri på trafik, chatt loggar och fel.
    * Välj den **insikter platsen** där Application Insights-resursen ska distribueras.

5. När alla fält validerats, kan du klicka på **skapa** Inled distributionen av dessa tjänster i din prenumeration. Det tar några minuter att slutföra.

6.  När distributionen är klar visas i följande resurser som skapats i din prenumeration.

    ![Skapa en ny frågor och svar om Maker-tjänst](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och publicera en kunskapsbas](../Quickstarts/create-publish-knowledge-base.md)