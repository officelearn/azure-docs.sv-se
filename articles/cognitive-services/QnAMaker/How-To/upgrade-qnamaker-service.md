---
title: Uppgradera tjänsten frågor och svar om Maker - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Så här uppgraderar du frågor och svar om Maker-tjänsten
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e8d3713d6729c4e30da9a64a382e9d5a647dfefd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354045"
---
# <a name="upgrade-your-qna-maker-service"></a>Uppgradera tjänsten frågor och svar om Maker
Du kan välja att uppgradera enskilda komponenter i frågor och svar om Maker stacken efter att. Visa information om den beroende komponenter och val av SKU [här](https://aka.ms/qnamaker-docs-capacity).

## <a name="upgrade-qna-maker-management-sku"></a>Uppgradera frågor och svar om Maker Management SKU
Så här uppgraderar du frågor och svar om Maker management SKU:
1. Gå till din frågor och svar om Maker resurs i Azure-portalen och välj **prisnivå**.

    ![Frågor och svar om Maker resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Välj lämplig SKU och tryck på **Välj**.

    ![Skapa frågor och svar om prissättning](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Uppgradera App service
Du kan [skala upp](https://docs.microsoft.com/azure/app-service/web-sites-scale) eller skala ned App service.

1. Gå till App service-resurs i Azure-portalen och välj **skala upp** eller **skala** alternativ efter behov.

    ![Frågor och svar om Maker app service skala](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Uppgradera Azure Search-tjänsten
För närvarande det går inte att utföra en plats för i uppgraderingen av Azure söka SKU. Dock skapa en ny Azure search-resurs med önskad SKU: N, återställa data till den nya resursen och länka det till frågor och svar om Maker stacken.

1. Skapa en ny Azure search-resurs i Azure-portalen och välja önskad SKU: N.

    ![Frågor och svar om Maker Azure search-resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Återställa index från din ursprungliga Azure search-resursen till en ny. Se exempelkoden återställningsverktyg [här](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. När data har återställts, gå till din nya Azure search resurs, Välj **nycklar**, och notera den **namn** och **administrationsnyckeln**.

    ![Frågor och svar om Maker Azure search-nycklar](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Länka den nya Azure search-resursen till stacken frågor och svar om Maker genom att gå till frågor och svar om Maker App service.

    ![Frågor och svar om Maker apptjänst](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Välj **programinställningar** och Ersätt den **AzureSearchName** och **AzureSearchAdminKey** fält från steg 3.

    ![Frågor och svar om Maker apptjänst inställning](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Starta om appen.

    ![Frågor och svar om Maker apptjänst omstart](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda frågor och svar om Maker API](../Quickstarts/csharp.md)
