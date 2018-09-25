---
title: Uppgradera din QnA Maker-tjänsten – QnA Maker
titleSuffix: Azure Cognitive Services
description: Du kan välja att uppgradera enskilda komponenter i QnA Maker-stacken efter att.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 8542b1f6dfe031de58ea6eeb931027ee03bd81f2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030973"
---
# <a name="upgrade-your-qna-maker-service"></a>Uppgradera din QnA Maker-tjänst
Du kan välja att uppgradera enskilda komponenter i QnA Maker-stacken efter att. Se information om beroende komponenter och SKU-markering [här](https://aka.ms/qnamaker-docs-capacity).

## <a name="upgrade-qna-maker-management-sku"></a>Uppgradera QnA Maker Management SKU
Så här uppgraderar du QnA Maker management SKU:
1. Gå till QnA Maker-resursen i Azure-portalen och välj **prisnivå**.

    ![QnA Maker-resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Välj lämplig SKU och tryck på **Välj**.

    ![Priser för QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Uppgradera App service
Du kan [skala upp](https://docs.microsoft.com/azure/app-service/web-sites-scale) eller skala ned App service.

1. Gå till service-appresursen i Azure-portalen och välj **skala upp** eller **Nedskalning** alternativ efter behov.

    ![QnA Maker app service-skala](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Uppgradera Azure Search-tjänst
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
