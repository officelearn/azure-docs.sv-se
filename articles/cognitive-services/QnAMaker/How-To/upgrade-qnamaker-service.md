---
title: Uppgradera din QnA Maker-tjänsten – QnA Maker
titleSuffix: Azure Cognitive Services
description: Dela eller uppgradera dina QnA Maker-tjänster för att kunna hantera resurserna som är bättre.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: tulasim
ms.openlocfilehash: cb38fdcfa8cbbfa0b90141c2de2280ff1ef08c3b
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437995"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Dela eller uppgradera QnA Maker-tjänsten
Dela eller uppgradera dina QnA Maker-tjänster för att kunna hantera resurserna som är bättre. 

Du kan välja att uppgradera enskilda komponenter i QnA Maker-stacken efter att. Se information om beroende komponenter och SKU-markering [här](https://aka.ms/qnamaker-docs-capacity).

## <a name="share-existing-services-with-qna-maker"></a>Dela befintliga tjänster med QnA Maker

QnA Maker skapar flera Azure-resurser. Använd följande tabell för att minska hanteringen och dra nytta av kostnadsfördelning, för att förstå vad du kan se och kan inte dela:

|Tjänst|Dela|
|--|--|
|Cognitive Services|X|
|Apptjänstavtal|✔|
|Apptjänst|X|
|Application Insights|✔|
|Söktjänst|✔|

## <a name="upgrade-qna-maker-management-sku"></a>Uppgradera QnA Maker Management SKU

När du har fler frågor och svar i kunskapsbasen, uppgradera din prisnivå QnA Maker-tjänsten utanför din aktuella nivån. 

Så här uppgraderar du QnA Maker management SKU:

1. Gå till QnA Maker-resursen i Azure-portalen och välj **prisnivå**.

    ![QnA Maker-resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Välj lämplig SKU och tryck på **Välj**.

    ![Priser för QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Uppgradera App service

 När din kunskapsbas behöver hantera flera begäranden från klientappen, uppgradera din app service-prisnivå.

Du kan [skala upp](https://docs.microsoft.com/azure/app-service/web-sites-scale) eller skala ned App service.

1. Gå till service-appresursen i Azure-portalen och välj **skala upp** eller **Nedskalning** alternativ efter behov.

    ![QnA Maker app service-skala](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Uppgradera Azure Search-tjänst

När du planerar att ha många kunskapsbaser uppgradera prisnivån Azure Search-tjänsten. 

För närvarande går det inte att utföra en på plats uppgradering av Azure Sök SKU. Dock skapa en ny resurs i Azure search med önskad SKU, återställa data till den nya resursen och sedan länka det till QnA Maker-stacken.

1. Skapa en ny resurs i Azure search i Azure-portalen och välj önskad SKU: N.

    ![QnA Maker Azure search-resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Återställa index från din ursprungliga Azure search-resurs till en ny. Se kodproverna återställningsverktyg [här](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. När data har återställts, går du till nya Azure search-resursen, väljer **nycklar**, och Skriv ned den **namn** och **administratörsnyckel**.

    ![QnA Maker Azure search-nycklar](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Om du vill länka den nya Azure search-resursen till QnA Maker-stacken, går du till QnA Maker App service.

    ![QnA Maker appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Välj **programinställningar** och Ersätt den **AzureSearchName** och **AzureSearchAdminKey** fält från steg 3.

    ![QnA Maker appservice inställningen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Starta om App service.

    ![QnA Maker appservice omstart](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använd API för QnA Maker](../Quickstarts/csharp.md)
